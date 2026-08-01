# 03 — Data Plan

## Architecture: zone-indexed knowledge, dialogue-shaped training data
Two artifacts, strictly separated:
1. **Zone corpus** — structured factual base ("scrape everything" lives here), compiled by the
   researcher agent (04). Also reused verbatim by the app-layer RAG.
2. **Training set** — synthetic *consultation dialogues* generated FROM the corpus in the
   01 product format. The model is trained on behavior, grounded in the corpus facts.

## The regional index: ~12 African macro agro-ecological zones
Simplified from the FAO agro-ecological zoning tradition; enough resolution to be credible,
few enough to finish. Working set:
1. Sahel (semi-arid band)                     7. East African highlands (KE/TZ/RW/UG)
2. Sudanian savanna                            8. Horn semi-arid (ET lowlands/SO)
3. Guinea savanna                              9. East African coastal strip
4. West African humid forest (cocoa belt)     10. Miombo / Central-Southern plateau (ZW/ZM/MW/MZ)
5. Congo basin humid                          11. Kalahari & SW semi-arid (BW/NA/ZA interior)
6. Ethiopian highlands                        12. Mediterranean North + Nile valley (MA/TN/DZ/EG)
Madagascar handled as a note under 7/9. Zimbabwe's Natural Regions I–V get extra depth inside
zone 10 (home-field credibility for the video + Shona set).

## Per-zone schema (the researcher agent fills this; YAML, one file per zone)
- climate: rainfall pattern + range, season calendar, ENSO sensitivity (what El Niño / La Niña
  historically mean here), drought/flood cycle notes
- soils: dominant types, common deficiencies, pH tendencies, cheap diagnostics
- staple + cash crops: per crop → key varieties/classes with strengths/weaknesses (maturity,
  drought tolerance, disease resistance), planting windows, spacing/population, fertility needs
- pests & diseases: top 5–8 per major crop → identification cues, cultural/IPM controls,
  chemical class (NO dosages), economic threshold logic
- livestock: dominant systems, top health issues, husbandry basics, vet-referral triggers
- economics: typical input cost anchors, marketing channels, storage options, price seasonality
- sources: every fact block carries its source tag

## Crop priority (coverage order)
Maize, cassava, sorghum/millet, rice, beans/cowpea, groundnut, tomato + leafy veg, sweet potato,
banana/plantain, cocoa, coffee, tea, cotton, tobacco, wheat (zone 12), + cattle/goats/poultry.
Depth beats breadth: a zone is "done" at its top 4 crops + livestock, then widen.

## Sources (open, citable; licensing noted per item in corpus)
FAO (FAOSTAT, crop calendars, AEZ materials), CGIAR centers (CIMMYT, IITA, ICRISAT, ILRI,
AfricaRice), national extension bodies (AGRITEX Zimbabwe, KALRO Kenya, IAR Nigeria, EIAR
Ethiopia), Plantwise/PlantVillage knowledge banks, Access Agriculture, WFP/FEWS NET seasonal
monitors (historical patterns only). Rule: prefer primary/institutional sources; every corpus
fact carries a source; no scraping of paywalled or restrictively licensed material.

## Synthetic training-data recipe (teacher-model distillation)
- Teacher: strongest available large model, prompted with (a) the 01 behavior spec + format
  contract, (b) a sampled zone file, (c) a scenario seed. Output: full multi-turn consultation.
- Volume target: **8,000–15,000 dialogues.** Mix: 55% English advisory consultations,
  15% Shona / Shona-English code-switch, 10% economics-led (budgeting, sell-vs-store, ROI),
  10% intake-only and profile-update turns, 5% safety-rail and refusal cases (dosage requests,
  weather-prediction requests), 5% general-instruct retention data.
- Diversity axes: zone × crop × season type × budget band × user persona (officer vs farmer)
  × problem ambiguity. Scenario seeds enumerated programmatically to prevent mode collapse.
- Quality gates (per ai-first-engineering: eval coverage beats anecdotes): teacher self-grades
  against the 05 rubric; 5% human spot-check per batch; dedup by embedding similarity;
  **decontamination:** nothing from the 48-prompt held-out set or its paraphrases enters training.

## Shona strategy (the +15% claim must survive scrutiny)
- Sources: AGRITEX vernacular extension materials, VOA/ Kwayedza-style agri reporting for
  register, teacher translation of English dialogues with **native validation**.
- Volume modest (≈15%) to avoid degrading English capability; consistency via a fixed
  agricultural glossary (chibage=maize, mbesa/mhodzi=seed, ivhu=soil, mvura=rain, zvirwere=diseases,
  zvipfuyo=livestock — glossary file lives in /data and is enforced in generation prompts).
- **Generation method — English-pivot, borrowed from InkubaLM (Tonja et al. 2024):** generate
  the consultation in English first, translate to Shona, native-validate — not direct Shona
  prompting of the teacher. Cheaper, more consistent glossary control, and InkubaLM's own results
  show this pivot approach competitive even on their *smallest*-resourced language (isiZulu,
  172.7M tokens) — evidence a 15% Shona slice is enough to move the needle, not a shot in the dark.
- Validation protocol: Shona validator blind-rates 30 sampled sn outputs for fluency +
  correctness; results published in REPORT.md (this is the evidence the panel — which has no
  Shona speaker — can trust). Claim wording in metadata mirrors demonstrated capability only.
- **Held-out Shona eval prompts are authored natively in Shona**, not translated at test time —
  InkubaLM's own eval used native-language prompts for final reported numbers, and mixing the
  two would quietly test something other than what ships.
- **Scope discipline (rejected pivot, logged):** a 7-language "continental coverage" stack
  (en/fr/ar/sw/ha/yo/am) was proposed and rejected 20 Jul — no per-language score bonus exists
  (any single African language qualifies for +15%), it collides with the ≤1.3B/16 tok-s/1.8GB
  kill thresholds, and InkubaLM's own paper (5 languages, 0.4B, purpose-built, years of prior
  work) is the reference point for how much specialization multilingual coverage actually costs.
  Depth on Shona beats breadth on seven languages neither the timeline nor the hardware affords.

## Deliverables & acceptance criteria (delegation-ready)
- 12 zone YAML files passing schema check; ≥90% of fact blocks sourced; zero dosage entries.
- Generation scripts reproducible end-to-end from `/data` with one command.
- Corpus v1 by **Aug 2** (infra checkpoint); training set v1 by **Aug 5**; v2 after A/B findings.
