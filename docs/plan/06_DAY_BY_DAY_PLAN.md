# 06 — Day-by-Day Implementation Plan

Mirrors the calendar exactly. Every weekday block is 12:30–14:30 CAT (2h); Sunday checkpoints
are 09:00–12:00 CAT (3h) and are the four kill-rule gates from `05_EVAL_PLAN`.

## Delegation split (who does what in each session)
- **DeepSeek** (cheap, strong at code/data): pipeline scripts, GGUF conversion/quantization,
  training configs, profiler automation, eval-harness code, app-layer scaffolding.
- **Haiku / Cowork sessions**: zone corpus research (the `04` prompt), seed-dialogue drafting,
  REPORT.md drafting, glossary compilation, video scripting.
- **Adrian (never delegated):** architecture decisions (ADR-1), the safety-rail audit, the
  Aug 9 blind-A/B verdict, the Aug 16 freeze go/no-go, final video narration, final submit.
Per ai-first-engineering: Adrian's attention goes to correctness and judgment calls, not typing.

---
## Week 1 — Jul 27–31: Infra + corpus + bake-off
**Mon 27** — Infra day. Provision 4vCPU/8GB Ubuntu VPS; install llama.cpp + adtc-profiler,
smoke-test. Submit Udutech GPU-credit application. Launch Zone 10 (Miombo/Zimbabwe) researcher
session via Cowork using `04_RESEARCHER_AGENT_PROMPT`.
**Tue 28** — Review Zone 10 YAML against the acceptance checklist. Launch Zone 7 (E. Africa
highlands) and Zone 1 (Sahel) research sessions in parallel. Seed the Shona agri glossary file.
**Wed 29** — Bake-off harness (DeepSeek): llama.cpp build validated on M1-Metal and VPS-CPU;
download/convert/quantize scripts for all 6 candidates, dry-run on one. Review zone batch 2.
**Thu 30** — Run the full bake-off conversion: all 6 candidates → GGUF → Q4_K_M; profiler pass
on VPS for stock tok/s + RSS baselines. Confirm Rule-0 compatibility (drop anything that won't
load in stock llama.cpp). Review zone batch 3–4.
**Fri 31** — Bake-off quality pass: 12 prompts, all 6 stock models, blind-scored against the
`05` rubric. **ADR-1 drafted** — primary base + fallback, with numbers. MoE prune-probe timeboxed
for the weekend. Corpus at ≥4 zones.

### Sun Aug 2 — Checkpoint: infra (kill rule 1)
VPS live · profiler clean on ≥2 candidates · MoE-probe verdict logged · corpus pipeline
producing · Udutech credits applied. Any miss → minimal-ship mode.

---
## Week 2 — Aug 3–7: Data generation + first training run (the core lift)
**Mon 3** — Data pipeline (DeepSeek): teacher-model synthetic generator implementing the
English-pivot recipe. **10 seed dialogues hand-reviewed against the `01` rubric by Adrian** —
the spec-quality gate before anything scales.
**Tue 4** — Scale to ~2,000 English dialogues across zones/crops/budgets. Run dedup +
decontamination against the 48-prompt held-out set. Adrian spot-checks 5%.
**Wed 5** — Push to ≥6,000 English dialogues. Start Shona pivot-translation batch (first 500).
Confirm Shona validator is briefed and scheduled.
**Thu 6** — Assemble training set v1 (8–15k dialogues, mix ratios per `03`), train/dev split.
Write LoRA config (DeepSeek); launch first LoRA run on free-tier Colab/Kaggle.
**Fri 7** — Monitor first checkpoint (loss + quick qualitative read). Queue the Udutech-funded
full run for the weekend. Prepare the imatrix calibration file for quantization.

### Sun Aug 9 — Checkpoint: kill/ship (kill rule 2) — decisive day, Adrian-run
Personally run the 48-prompt blind A/B this session — not delegated. Bar: ≥60% blind win vs
base, zero English regression, ≥16 tok/s, ≤1.8GB RSS. Fail → minimal-ship mode, stop here.

---
## Week 3 — Aug 10–14: Refinement (continues only if Aug 9 passed)
**Mon 10** *(check: possible Zim public holiday — biggest build day if so)* — Error-analyze the
Aug 9 blind-test failures by pillar (intake / tiers / ROI / climate / safety / language).
Generate a targeted data patch (DeepSeek); launch LoRA iteration 2.
**Tue 11** *(check: possible Zim public holiday)* — Finetune + quantize the second candidate
(MoE-prune winner if it cleared the bake-off, else the fallback dense model). Profiler pass
on both leading candidates.
**Wed 12** — Q4_K_M vs Q5_K_M tradeoff on RSS/quality. **Full safety-rail sweep, Adrian-run**
(dosage / forecast / livestock-dose refusal set) — any failure gets a targeted data patch and
a retrain flag, no exceptions.
**Thu 13** — Shona validation: native validator blind-rates 30 sampled outputs. Glossary
consistency check. Start app-layer (thin RAG demo) scaffolding (DeepSeek).
**Fri 14** — Lock the final candidate (ADR-1 updated with final numbers). Finalize repo
structure against the submission template. App-layer demo working end-to-end on M1.

### Sun Aug 16 — Checkpoint: model freeze (kill rule 3) — Adrian-run go/no-go
Full submission dry-run: metadata validates, download script pulls the GGUF, profiler runs
clean end-to-end. Pass → **no more model changes.** Fail → fix same day or ship prior candidate.

---
## Week 4 — Aug 17–21: Report + video only (model is frozen)
**Mon 17** — REPORT.md first full draft (Cowork drafts from `01`–`05` + actual results; Adrian
edits). All sections present: problem, constraints, design decisions, tools, benchmarks.
**Tue 18** — 2-min video script finalized (Adrian narrates; Shona exchange is the money shot).
Screen-capture footage: model running in LM Studio/terminal, both EN and SN.
**Wed 19** — Video rough cut assembled. `metadata.json` finalized (2 test prompts,
cross-disciplinary pairing, african_alpha_claim, budget-profile claim). README polished.
**Thu 20** — Video final cut, ≤2 minutes. Full repo walkthrough against the submission
checklist end to end. ADRs committed.
**Fri 21** — Buffer day: fresh-eyes reread of REPORT.md, fix any gaps found. Everything staged.

### Sun Aug 23 — Checkpoint: final submission pass
Final package assembled and reviewed: REPORT.md, video, metadata, test prompts.

**Mon 24** — Buffer day. Submit on Devpost; confirm the commit-hash download link resolves.
**Tue 25, 08:45 CAT — deadline.**
