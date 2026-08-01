# 02 — Technical Plan

## Constraint stack (from rules + profiler source)
- **llama.cpp only, GGUF only.** Judges also load the raw GGUF in stock LM Studio / Ollama.
  Consequence: nothing custom executes at generation time — no custom routing, samplers,
  speculative decoding, grammars, or plugins. Levers = weights + baked chat template.
- Telemetry: throughput via `llama-bench -p 512 -n 128`; efficiency via **peak RSS** of the
  llama.cpp process; Seff = 100×((7GB−peak)/7GB); Sperf relative to the **fastest submission**;
  thermal −10 (likely inert on VM eval, treat as real anyway); **OOM = disqualification**.
- Accuracy = judge panel on 4 prompts (2 self-authored + 2 hidden in-domain) + docs quality +
  integration/UX + later gates. Multipliers: African language +15%, budget profile +10%.

## The "very good base model" question, answered
The property being bought is **knowledge density + instruction-following per active parameter**
(pretraining quality). It is chosen empirically, not by taste. Separately, "accurate feedback"
comes from a **teacher model** (large frontier/open model, online, training-time only): it
generates synthetic consultation data and rubric-grades candidate outputs. Offline rules bind
inference at judging, not training.

## Bake-off candidates (week 1; compatibility is gate zero)
Rule 0: a candidate must load and run in current llama.cpp + the ADTC profiler on day one,
or it is out — no assumed support, verify empirically.

| Candidate | Class | Why it's here |
|---|---|---|
| Qwen3-0.6B (thinking OFF) | dense 0.6B | strongest small instruct quality; fastest dense |
| Gemma 3 1B | dense 1B | multilingual pretraining; Google-story side value |
| Llama 3.2 1B | dense 1B | control; strong tooling |
| LFM2-1.2B | efficiency hybrid | CPU-edge architecture; honors the "efficient design" instinct |
| Granite 3.1 1B-A400M | MoE, ~400M active | Adrian's "600M with a few experts" instinct, literal form |
| Granite 3.1 3B-A800M | MoE, ~800M active | bigger-brain MoE; **prune-probe target** |

Bake-off protocol: same 12 agri prompts (drawn from eval design, not the held-out set), blind
labels, scored on the 05 rubric; profiler run per candidate on the VPS for tok/s + RSS.
Output: one primary base + one fallback. Decision recorded as ADR-1 in the repo.

## MoE expert-prune probe (4h hard timebox)
1. Run the agri corpus through Granite 3B-A800M capturing expert routing statistics.
2. Prune never/rarely-routed experts (metadata + tensor surgery), keeping architecture family valid.
3. Heal with a short LoRA pass; verify the pruned GGUF loads in **stock** llama.cpp.
4. Compare vs dense candidates on quality/tok-s/RSS. Admit to fine-tune stage only if it wins.
If conversion fights back for more than the timebox — drop it. This is a differentiator,
not a dependency. (If admitted, it is the headline systems section of REPORT.md.)

## Why not "everything from experts and reasoning"
Partly time + the 5h training budget (long-CoT distillation/RL is out of scope). The harder
blocker is **token economics**: a thinking-mode preamble of 300–800 hidden tokens at ~16 tok/s
is 20–50 seconds of silence in the judges' chat window — it reads as broken. Instead we train
**short structured reasoning inside the visible answer** (assessment → profile → tiers), which
is reasoning the judges can see, at a length the hardware can afford. Qwen candidates run with
thinking disabled at train and eval time.

## Fine-tune plan
- Method: LoRA/QLoRA SFT (Unsloth) on the synthetic consultation corpus (03_DATA_PLAN).
- Where: free Colab/Kaggle tiers first; **Udutech 5h GPU credits** reserved for the two best
  full runs. 0.6–1.3B LoRA fits comfortably in free-tier T4/P100 budgets.
- Anti-lobotomy: mix ~10–15% general instruct data; English sanity set gates every candidate.
- Merge LoRA → fp16 → convert to GGUF → quantize on the M1.

## Quantization
- Baseline **Q4_K_M**; compute an **importance matrix (imatrix) calibrated on the agri corpus**
  and quantize with it — measurable quality-per-bit win + a REPORT.md section the systems
  judges (Ing, Fadiran, Oyinlola) will recognize as craft. Try Q5_K_M only if RSS ≤1.8GB holds.
- Bake the chat template + system persona into GGUF metadata. Ship recommended sampling
  defaults in README (temp ~0.7, min-p) knowing judges may keep app defaults — the model must
  behave well under default samplers, which the eval harness tests.

## Pipeline split (M1 Air 8GB, no MLX — correct call)
- **M1 (local):** data pipeline, eval harness + blind A/B, llama.cpp-Metal inference for fast
  iteration, GGUF conversion + imatrix + quantization, repo/docs. llama.cpp Metal build ~2GB
  headroom is fine at this model class.
- **Cloud (training-time only):** teacher-model data generation + grading; LoRA runs.
- **VPS (4 vCPU / 8GB x86, ~€7/mo):** the judge-environment replica — profiler runs, tok/s,
  peak RSS, and the numbers that go in REPORT.md. Nothing is "measured" on the M1 for the report.

## Thin app layer (post-freeze only; Integration award evidence)
A minimal local UI over llama.cpp: offline **RAG over the zone corpus** + a saved farm-profile
file (the persistent-memory story, honestly labeled as app-layer). Appears in the 2-min video
and Gate 2/3 — never claimed as part of the judged GGUF. Timebox: ≤6h in week 4.

## Repo layout (fork of adtc-2026-submission-template)
`/model` (gguf via download_model.sh) · `/data` (corpus + generation scripts) · `/training`
(configs, LoRA runs) · `/eval` (harness, held-out sets, A/B logs) · `/app` (thin demo) ·
`REPORT.md` · `metadata.json` (african_alpha_claim true; cross_disciplinary_pairing:
agronomy × agricultural economics, load_bearing) · ADRs in `/docs/adr`.
