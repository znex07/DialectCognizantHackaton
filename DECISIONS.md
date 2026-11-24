# Decisions

Newest first. Each entry: date, decision, reason. "Proposed" means not yet confirmed by the developer.

## 2026-11-24: Hard Gate G1 scope lock — two origins (A↔B), three scenarios (S1, S2a/S2b, S3), and built-in Privacy Receipt
We lock the active hackathon scope to **two hospital origins (Hospital A and Hospital B)**, **three active scenarios** (`S1` Referral Transfer, `S2a/S2b` Real MIMIC-IV Dictionary, `S3` LIS System Switch), **Conformance Validator v1 + embeddable SVG badge**, and a built-in **Live Egress & 0-Byte Privacy Receipt** inspector inside Hospital B. We explicitly cut **Hospital C** (`sites/hospital-c`), **Scenario S4** (eICU unlabelled intake), **Scenario S5** (non-healthcare Dialect Profile #2), and the standalone **Validator v2** cross-origin browser harness to the stretch list. Reason: cuts ~48 hours of stretch bloat to fund ~11 hours of high-ROI 10/10 upgrades, fitting comfortably inside **72 planned hours + a 12-hour holiday buffer** (84 hours total) while replacing the fragile Validator v2 harness with a stronger live browser receipt proving `0 bytes` of private context or keys reached Origin A.

## 2026-11-24: Workbench framing with 1-click FHIR R4 ConceptMap, CSV, and S3 Drift-Diff exports
Position Dialect explicitly as a **zero-install onboarding and LIS-migration drift workbench** whose human-approved review queue ends with **1-click exports**: valid FHIR R4 `ConceptMap` JSON, interface-engine CSV (for Mirth Connect, Rhapsody, Cloverleaf, or RELMA workflows), and an S3 drift-diff audit report. Human-confirmed local writes (`save_mapping` and `export_concept_map`) serve as Dialect's WebMCP-aligned grounded local site actions. Reason: real hospitals run pre-approved mapping tables server-side in interface engines rather than routing live patient HL7 traffic through a browser tab; ending the analyst journey with deployable standard exports makes the product immediately credible to hospital IT teams and Design judges.

## 2026-11-24: Deterministic client-side PHI stripping, 3-tier router (with On-Prem preset), and schema-locked proxy
Before any LLM inference call, plain TypeScript deterministically strips `PID`, `PV1`, `NK1`, `NTE`, patient IDs, and timestamps, sending only de-identified catalog metadata (`OBX-3` code/name, `OBX-6` unit, `OBX-7` reference range) and showing the exact stripped payload in a visible UI preview. Inference supports three tiers: (1) a **schema-locked**, per-IP rate-limited, provider-capped same-origin no-key Vercel proxy (accepting lab-mapping payloads only, with a clearly labelled recorded-run replay fallback when the daily cap is hit); (2) visitor BYOK cloud router (key stays in browser memory/settings, never sent to peer Origin A); and (3) a **1-click Local / On-Prem LLM preset** (`localhost:11434/v1` for OpenAI-compatible local servers like Ollama/vLLM). All docs use one harmonized privacy statement: hospital data, alias tables, and keys never reach peer sites; stripped catalog metadata reaches only the chosen LLM router (or zero external endpoints when using the Local/On-Prem preset). Reason: eliminates privacy copy contradictions for AI screeners, defends against public proxy bot-drain, and proves a concrete zero-external-egress path for real hospitals.

## 2026-11-24: Disentangled transfer, 4-way fair baseline (with abstain-prompted LLM), and MIMIC S2a/S2b with strict ODbL isolation
Upgrade the experimental design and tighten Gate G2 kill rules before coding:
- **Disentangled S1 Transfer**: report **S1a Repeat-Code Cache** (exact alias hits, 0 LLM tokens, workload savings) separately from **S1b Strictly Disjoint Unseen-Code Generalization** (brand-new Hospital B codes using B's approved examples as few-shot dialect hints). Enforce the G2 transfer drop-and-recovery kill rule strictly on **unseen codes** so adaptation cannot be dismissed as hash-map caching.
- **4-Way Fair Baselines**: benchmark (1) lexical/embedding match, (2) forced-choice single LLM call (LabBridge-style), (3) **abstain-prompted single LLM call**, and (4) Dialect evolved network on ≥200 held-out mappings with bootstrap 95% CIs and Risk-Coverage / cost Pareto curves. Enforce the G2 `+0.6` mean points/mapping win against **both** single-LLM baselines.
- **MIMIC-IV S2a vs. S2b & ODbL Isolation**: tag all 269 LOINC-coded MIMIC-IV Demo v1.0 `d_labitems` rows for unit dependence (e.g., Mass/vol vs. Moles/vol). Report **S2a** (no site unit convention → Dialect safely abstains instead of eating `-5` penalties) vs. **S2b** (Hospital R sets US conventional units in private `sly_data` → deterministic UCUM guardrail unlocks coverage). Keep MIMIC rows **strictly held-out for evaluation only**—never embed them into MIT-licensed few-shot prompts or evolved network JSON—so ODbL share-alike never contaminates our MIT codebase.

## 2026-11-24: Error-trace symbolic rule evolution with readable Gen 0 → Gen N diffs
Instead of searching only five discrete hyperparameters (which risks looking like an 80-run grid search), offline evolution in `evolve/` mutates concrete, interpretable **Neuro SAN disambiguation rules** directly from `-5` error traces (specimen conflicts, calculated-vs-measured tests, missing-unit escalation, panel-vs-analyte distinctions) alongside retrieval/threshold settings. Publish every generation's resolved Neuro SAN JSON, held-out score, and human-readable **`Gen 0 → Gen N` rule diffs** showing which clinical failure each generation eliminated. Reason: directly showcases verifiable evolutionary AI rather than trivial parameter tuning.

## 2026-11-24: Week-2 cross-origin nsDeploy smoke test and early mid-December Validator v1 + SVG badge launch
Pull a minimal 2-origin Vercel peer-load smoke test (Hospital A + minimal Hospital B) into **P2 (by Sun 6 Dec)** instead of deferring cross-origin verification to P3 (20 Dec). Publish **Conformance Validator v1** with an embeddable **`OAW Spec 7/7 Pass` SVG badge** and JSON report export in Cognizant's GitHub Discussions by **mid-December** (alongside an open `DialectProfile` v1 JSON schema). Reason: eliminates `nsDeploy`/CORS/`bridge/` surprises two weeks before the holiday period and gives other entrants time to adopt and cite our validator before judging starts.

## 2026-11-24: Automated CI `verify:docs` consistency guard
Add a lightweight Node script (`verify:docs`) running in GitHub Actions on every push to validate `agent-manifest.json`, Neuro SAN JSON, and `links.json` schemas, verify `docs/facts.md` ↔ `results.json` number parity, and fail CI if forbidden unqualified privacy claims or cut-scope drift reappear. Reason: prevents documentation contradictions that automated AI screeners penalize.

## 2026-11-24: MIT licence for our code and documents
The challenge requires the project to be open source. MIT was the developer's choice. Third-party data is not relicensed: the MIMIC-derived test file stays under ODbL (share-alike, strictly held-out for evaluation) and LOINC content under the LOINC licence, each in its own folder with its notice.

## 2026-11-24: Real lab names, fictional hospitals, labelled scenarios
The real-world test set is MIMIC-IV Demo v1.0 `d_labitems`: 1,630 real lab names, 269 with hospital-assigned LOINC codes, ODbL, no patient data (v2.2 dropped the LOINC column, so v1.0 it is). Hospitals and patients are fictional and labelled synthetic everywhere, including HL7 MSH-11 = `T`. *[Refined above on 2026-11-24 by the Gate G1 scope lock: three active scenarios (`S1` referral transfer, `S2a/S2b` real dictionary, `S3` lab system switch) share one engine; `S4` public health eICU intake moved to stretch.]* Reason: judges and AI screeners must see both that the data is synthetic and that it is grounded in real, cited sources.

## 2026-11-24: No hidden instructions for AI judges
The rules allow AI-driven judging and also allow disqualification for tampering with the judging. We prepare for AI screening only with clear structure, evidence and consistent facts (PLAN.md section 6), never with hidden text or prompts aimed at a judge.

## 2026-11-24: One facts file
Every number in the README, Devpost text, video and UI comes from `docs/facts.md`, so no two documents disagree. Contradictions are easy for an AI screener to spot.

## 2026-11-24: Two hospitals plus the conformance validator
*[Confirmed and expanded by the 2026-11-24 Gate G1 scope lock above.]* The challenge repo's validation step is still a TODO. A validator scores under Technological Implementation and Potential Impact, and doubles as our own Stage One test. Two hospitals are enough to show transfer. The third hospital is cut to the stretch list.

## 2026-11-23: Submit on 10 Jan, not 11 Jan
The hard deadline is Mon 11 Jan 2027 8:00 am PST (16:00 UTC), which is midnight Manila time going into Tue 12 Jan. Submitting a day early leaves room for upload or Devpost problems.

## 2026-11-23: Kill-rule numbers fixed before the spike
Contender 4 (evolved network) must beat single-call LLM baselines by at least 0.6 mean points per mapping on at least 200 held-out mappings, and the transfer test must show a drop of at least 10 accuracy points at a new site with at least half recovered after adapting. Fixed before coding so the result cannot be argued after the fact. *[Tightened on 2026-11-24 above to enforce transfer strictly on unseen codes and require beating both forced-choice and abstain-prompted single-LLM baselines.]* See PLAN.md section 5.

## 2026-11-23 (proposed): TypeScript, Node scripts, Vite for the sites
The browser runtime has to be JavaScript, the same stack as the developer's previous project, and Python is not installed on the dev machine. Confirm at G1, unless the spec dictates a specific runtime.

## 2026-11-23 (proposed): Generate synthetic HL7 in our own code
Synthea is Java and heavy for a 7.6 GB machine; hl7gen needs Python. A small TypeScript generator also gives us exact ground truth and full control over each hospital's quirks. Research file 05 may change this.

## 2026-11-23 (proposed): One origin per hospital site
Cross-site privacy and peer loading can only be shown between different origins. GitHub Pages project sites under one account share one origin, so each hospital needs its own Vercel project or its own subdomain. Confirm at the start of P2 (verified early via the Week-2 smoke test).
