# ADTC 2026 — Decision Record (grill closed 20 Jul 2026)

## Decision
Enter Africa Deep Tech Challenge 2026 (Laptop LLM), **Agriculture domain**, `language_scope ["en","sn"]`,
targeting the Grand Prize with the **$3,000 Best Integration** and **$1,500 Best Localisation** awards as
secondary targets. Objective: **win** (title + cash), not EV-maximize.

## What was rejected during the grill
- Unbounded "whatever it takes" → replaced by the **displacement tripwire** (below).
- Novel model architecture / custom routing / custom samplers → illegal: judges run stock llama.cpp
  runtimes (LM Studio / Ollama) against the raw GGUF. Only supported architectures load.
- "5-year weather anticipation" as forecasting → reframed as **climate-cycle-aware planning**
  (ENSO phase, zone drought history, season-type risk posture). The model never predicts weather.
- Creative Writing and Corporate domains → no judge champion / weakest differentiation.
- MLX toolchain → dead end; deliverable is GGUF, so llama.cpp (Metal) is the local runtime.

## Locked parameters
- Model class ceiling: **≤ ~1.3B active parameters**. Dense 1B candidates + small-MoE candidates
  (400–800M active). MoE expert-prune probe allowed, 4h timebox, admission by profiler + blind quality.
- Judged surface = the GGUF alone: fine-tuned behavior + baked chat template. No app code in scoring path.
- Product behavior: consultative agronomist-economist (see 01_PRODUCT_SPEC).
- Multiplier claims: African language (+15% panel score) and budget profile (+10%) — both mandatory.
- Pipeline: M1 Air = data/eval/quantization via llama.cpp-Metal; training = free Colab/Kaggle +
  Udutech GPU credits (5h); x86 4vCPU/8GB VPS = target-profile validation. Refurb i5 purchase only
  if alive after Aug 9 (then doubles as Securecam/SmartPay test rig).
- Team: solo entry; 1 agronomist advisor + 1 native Shona validator, credited in REPORT.md.

## Kill package (harsher, accepted)
1. **Sun Aug 2 — infra checkpoint.** VPS live; profiler clean on ≥2 candidates (incl. MoE probe verdict);
   data pipeline producing samples; Udutech credits applied. Miss any → **minimal-ship mode**.
2. **Sun Aug 9 — kill/ship.** Fine-tune wins **≥60% blind pairwise** vs its base on ≥40 held-out prompts;
   **no regression** on 10-prompt English sanity set; **≥16 tok/s**, **≤1.8 GB peak RSS** on the VPS.
   Fail any → minimal-ship mode.
3. **Sun Aug 16 — model freeze** after full submission dry-run passes. Final week = report + video only.
4. **Displacement tripwire.** Second time ADTC displaces a Lovies block, a RealTowers commitment, or a
   Google-app block → minimal-ship mode. **OOM on 8 GB = that config is dead the same day** (DQ risk).
5. **Gates 2/3** (Sept 8–29 audit + Q&A; Oct 17 live defense): continue only as semifinalist+;
   Gate 3 prep capped ~6h; if Lovies launch-critical path slips >3 days in Sept → mandatory Q&A only.

**Minimal-ship mode** = best stock-model configuration + honest REPORT.md + video, ≤8h total, then stop.
A submission still exists (semifinalist GPU credits remain live upside); investment does not.

## Sequencing (already on calendar)
Accelerate Africa owns the 12:30–14:30 band Mon 20–Fri 24 Jul. ADTC weekdays 27 Jul–24 Aug + four Sunday
checkpoint blocks. Google Lab assembly 26–28 Aug, submit 31 Aug. Lovies LAUNCH 27 Sept. ADTC deadline
Tue 25 Aug 08:45 CAT.

## Open items
- Does RealTowers observe Zimbabwe public holidays **Mon 10 + Tue 11 Aug**? If yes, those become the
  two biggest ADTC build days — block them.
- Recruit the agronomist advisor (AGRITEX/extension contact) and Shona validator by Aug 2.
- Confirm exact Accelerate Africa deadline date and submit inside this week's blocks.

## CRITICAL — eligibility risk found 22 Jul (read before registering)
The full rules (adtc-2026.devpost.com/rules) reveal this is a venture-eligibility contest, not a
plain hackathon: entrants must declare their **project or company is <12 months old**, ideation/
early-PoC stage, **not commercially launched**, **≤$25k external funding**. Finalists get
background-checked; misrepresentation = disqualification + prize clawback. Munda qualifies
cleanly on its own. **Avanzamos Africa (Pty) Ltd does not** — it's incorporated, older, and has
shipped monetized products (TinyInvoice/Gumroad, client work). Action: register as individuals
(Adrian + Ryan), submit Munda as a standalone project, keep Avanzamos's other products/history
out of the report and video entirely.
- **Confirm Ryan's country of residence is on the eligible-country list before adding him to the
  Devpost team** — team eligibility, not just his, is at stake.
- Read the Challenge Participation Agreement (Google Doc, linked from /rules) in full before
  either of you clicks agree — could not be rendered here; check IP-assignment language given
  Munda's planned reuse in the Google Lab application.
- REPORT.md must explicitly cite every open-source dependency (base model, llama.cpp, Unsloth,
  InkubaLM technique) — rules require clear citation of third-party tools as a condition of
  "original work."
- Watch the Devpost Updates tab for a possible separate Gate-1 proposal form (rules describe a
  two-step proposal-screen-then-prototype process; no distinct proposal artifact is listed yet).

## First concrete action
Tomorrow 12:30 — Accelerate Africa block. ADTC this week is limited to: Tue 16:00 knowledge session,
plus three micro-tasks outside blocks (<1h total): spin the VPS, apply for Udutech credits,
paste 04_RESEARCHER_AGENT_PROMPT into a Cowork session.
