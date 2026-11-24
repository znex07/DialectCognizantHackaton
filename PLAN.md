# Dialect: Project Plan

Open Agentic Web Challenge (Cognizant). Updated Tue 24 Nov 2026 (**10/10 Scope-Locked Revision**). Incorporates the pre-build critical audit in [docs/review.html](docs/review.html) and verified alignment with the live Devpost portal.

| | |
|---|---|
| **Hard deadline** | Mon 11 Jan 2027, 8:00 am PST (16:00 UTC; midnight going into Tue 12 Jan, Manila time) |
| **We submit** | **Sun 10 Jan 2027**. 11 Jan is not used |
| **Judging** | 11 to 18 Jan 2027 (winners announced ~19 Jan). Sites must stay live until 18 Jan, 5:00 pm PT |
| **Budget** | **72 hrs planned + 12 hr holiday buffer** (84 hrs total across 7 weeks, ~12 hrs/week) |
| **Read first** | [docs/idea-summary.md](docs/idea-summary.md) and [docs/review.html](docs/review.html) (Road to 10/10 Blueprint) |
| **Rules and spec** | [research/challenge-requirements.md](research/challenge-requirements.md) |
| **Single source of facts** | [docs/facts.md](docs/facts.md) |

---

## 1. What we are building

**Dialect** is a **zero-install hospital lab-code mapping and LIS-migration drift workbench** built as an open agentic website. It publishes evolvable Neuro SAN agent networks that learn each hospital's local lab-code "dialect", abstain (`+1 / 0 / -5`) instead of guessing, strip PHI deterministically in the browser, run locally without sending hospital data to peer sites, and export ready-to-import **FHIR R4 `ConceptMap` JSON, CSV, and drift-diff tables** for hospital interface engines (such as Mirth Connect, Rhapsody, or Cloverleaf).

Dialect hits **both** challenge tracks simultaneously: **agentified websites** (two live hospital origins) and **reusable community infrastructure** (Conformance Validator v1 + `DialectProfile` v1 schema + 0-Byte Privacy Receipt).

What we ship by 10 Jan:
1. **Two hospital open agentic websites** on two distinct origins (**Hospital A** and **Hospital B**). Hospital B loads A's published network by exact name after explicit origin consent, runs a **deterministic client-side PHI stripper** (`PID`/`PV1`/`NK1`/`NTE` removed in plain TypeScript with a visible stripped-payload preview), offers a **3-tier inference router** (schema-locked no-key proxy, visitor BYOK, and 1-click Local/On-Prem LLM preset), and displays a **Live Egress & 0-Byte Privacy Receipt** proving `0 bytes` of private context or keys reached Origin A.
2. **Interface-analyst review workbench**: displays auto-mapped proposals alongside the "ask a human" pile (top-3 candidates + reason), executes WebMCP-aligned **human-confirmed local site actions** (`save_mapping` into B's private `sly_data` alias table, showing destination and arguments before writing), and finishes with **1-click FHIR R4 `ConceptMap` JSON, CSV, and S3 Drift-Diff exports**.
3. **Error-trace symbolic evolution**: offline Node evolution in `evolve/` mutating concrete Neuro SAN disambiguation rules directly from `-5` failure traces, publishing every generation's resolved Neuro SAN JSON, held-out score, and **readable `Gen 0 → Gen N` rule diffs** on the site.
4. **Dialect Conformance Validator v1 + Embeddable SVG Badge**: a standalone static page anyone can point at any open agentic website to verify all Stage One / MVP spec checks, export a JSON report, and copy an embeddable **`OAW Spec 7/7 Pass` SVG badge**. Launched early in Cognizant's GitHub Discussions (where the official repo's validation step is still an empty `TODO`).
5. **Shared open tools & CI guard**: open **`DialectProfile` v1 JSON schema** (`profiles/lab-hl7/`), synthetic HL7 v2 dialect generator (`MSH-11=T`), abstain-aware scorer (`+1 / 0 / -5`), and an automated **CI `verify:docs` consistency guard**.

Scoring rule everywhere: **+1** correct, **0** "ask a human" (abstain), **-5** confident and wrong.

Rules we never break:
- **Synthetic data only** — fictional hospitals and patients (`MSH-11=T`, `SYN-000123`, `SYNTHETIC^PATIENT`); never real patient records.
- **Not a clinical tool** — every mapping screen shows *"Synthetic data. Not for clinical use. A human must approve."*
- **Human approves every write** — destination and arguments shown before writing to private `sly_data`.
- **Deterministic safety & privacy in plain code** — unit/UCUM validation and PHI stripping (`PID`/`PV1`/`NK1`/`NTE`) run in plain TypeScript, never as LLM prompt instructions.
- **Honest privacy boundaries** — hospital data, private alias tables, and visitor BYOK keys **never reach peer sites** (verifiable 0-byte receipt); only PHI-stripped catalog metadata reaches the site's chosen LLM router (or zero external endpoints when using the 1-click Local/On-Prem LLM preset).
- **Strict ODbL / MIT isolation** — MIMIC-IV Demo v1.0 rows remain **strictly held-out for evaluation only** and never enter few-shot prompts embedded in MIT-licensed network JSON.

---

## 2. Scope decisions (locked at Gate G1)

| Decision | Locked 10/10 Plan | Why |
|---|---|---|
| Hospitals | **Two origins (A & B)**; Hospital C cut | Two distinct origins prove 100% of cross-origin consent, privacy, and transfer; cutting Hospital C saves ~14 hrs and eliminates doc drift |
| Runtime | Reuse `nsDeploy` reference runtime (Apache-2.0) + **Week-2 2-origin smoke test** | Pulling minimal A↔B cross-origin peer loading into P2 (by 6 Dec) catches CORS/`bridge/` issues two weeks before holidays |
| 3-Tier Inference Router | **(1)** Schema-locked, IP-rate-limited, daily-capped same-origin no-key Vercel proxy (+ labelled replay fallback); **(2)** Visitor BYOK; **(3)** 1-click **Local / On-Prem LLM preset** (`localhost:11434/v1`) | Spec requires a working no-key default path; schema-locking blocks bot-drain; BYOK proves key isolation; Local/On-Prem proves true zero external egress for real hospitals |
| Privacy Verification | **Client PHI Stripper** + built-in **Live Egress & 0-Byte Privacy Receipt** inside Hospital B | Instruments `fetch()` + `PerformanceResourceTiming` to export a verifiable 0-byte receipt for Origin A; replaces the fragile ~18-hr standalone Validator v2 harness in ~4 hrs |
| Evolution | Offline Node scripts evolving **error-trace symbolic disambiguation rules** + `Gen 0 → Gen N` diffs | Browser runtime is tab-scoped; mutating concrete clinical rules from `-5` traces avoids looking like an 80-run hyperparameter grid search |
| Test data & Scenarios | **Three locked active scenarios** (`S1` Referral Transfer, `S2a/S2b` MIMIC-IV 269 Real Dictionary, `S3` LIS System Switch). `S4` (eICU) & `S5` cut to stretch | Cuts ~16 hrs of unlabelled eICU extraction while S1 + S2a/S2b + S3 answer every scientific and real-world judge question |
| Analyst Exports | **1-click FHIR R4 `ConceptMap` JSON, CSV, and S3 Drift-Diff audit report** | Turns a browser demo into a deployable workbench feeding Mirth Connect, Rhapsody, or Cloverleaf |
| Community Tooling & CI | **Conformance Validator v1** (launched mid-Dec) + `OAW Spec 7/7 Pass` SVG badge + `DialectProfile` v1 schema + **CI `verify:docs`** | High-leverage community adoption under Potential Impact + automated schema/`facts.md` parity guard for AI screeners |
| Stack & Hosting | TypeScript, Vite, Node scripts, Vitest; two separate Vercel projects (`*.vercel.app`) | JavaScript browser runtime; no Python installed; separate Vercel origins enforce true cross-origin boundaries |

---

## 3. Holiday-proof timeline (72 hrs planned + 12 hr buffer = 84 hrs)

Cutting ~48 hrs of low-ROI stretch work (Hospital C, S4 eICU, standalone Validator v2 harness) pays 4× over for ~11 hrs of 10/10 upgrades while keeping a 12-hour emergency buffer. Core coding finishes by **Sun 20 Dec** so Christmas, Rizal Day, and New Year are strictly cached offline eval runs, charts, and user tests.

| Phase | Dates | Hours | Main output | Gate |
|---|---|---|---|---|
| **P0 Research** | Mon 23 to Wed 25 Nov | **4** | `research/00-summary.md` + 7 files, MIMIC 269 unit-dependence audit | **G1, Wed 25 Nov:** confirm locked scope, MIMIC S2a/S2b split, and remaining eligibility checks |
| **P1 Spike** | Thu 26 to Sat 28 Nov | **9** | Scorer, generator, **4-way fair baselines**, **disentangled S1a/S1b transfer**, S2a/S2b check, error-trace rule mutation prototype | **G2, Sat 28 Nov:** tightened kill rule (section 5) |
| **P2 Stage One + Validator v1 + 2-Origin Smoke Test** | Sun 29 Nov to Sun 6 Dec | **14** | Hospital A live + schema-locked proxy + PHI stripper + Validator v1 w/ SVG badge + **minimal Hospital B cross-origin `nsDeploy` smoke test** + starter CI `verify:docs` | **G3, Sun 6 Dec:** Validator v1 passes 7/7 on live Hospital A AND minimal B loads A across two origins |
| **P3 Core Workbench & Privacy Proof** | Mon 7 to Sun 20 Dec | **20** | Full Hospital B workbench, `DialectProfile` v1 schema, Live 0-Byte Privacy Receipt, FHIR `ConceptMap`/CSV/Drift exports, 1-click Localhost LLM preset, **mid-Dec Validator v1 launch in Discussions** | **G3b, Sun 20 Dec:** full A↔B workflow live + Validator v1 posted + 1st mock judging |
| **P4 Evolution, Empirical Proof & Polish** | Mon 21 Dec to Sun 3 Jan | **13** | Offline error-trace evolution + `Gen 0 → Gen N` diffs, cached 4-way/S1/S2a/S2b/S3 evals (`results.json` + 95% CIs + Pareto chart), 3-user timed test (<3 min), practitioner quote, full CI `verify:docs` | **G4, Sun 3 Jan:** feature freeze + 2nd mock judging |
| **P5 Submission** | Mon 4 to Sat 9 Jan | **12** | 2-min video (own voice, trademark-clean), Devpost text, annotated screenshots, final README & CI green check | Video done **Thu 7 Jan** |
| **Buffer** | Reserved (P2–P5) | **12** | Emergency cushion for `nsDeploy` quirks or holiday slips | |
| **Submit** | **Sun 10 Jan** | | Entry submitted 24 hrs ahead of Mon 11 Jan 8:00 am PST hard deadline | |

---

## 4. Phase details

### P0 Research (Mon 23 to Wed 25 Nov · 4 hrs)
- [ ] Run [research/RESEARCH-PROMPT.md](research/RESEARCH-PROMPT.md) and get the 8 research files.
- [x] Find a **real, legally usable** list of local lab test names with known LOINC codes. **Found 24 Nov:** MIMIC-IV Demo v1.0 `d_labitems`, 1,630 real names, 269 with hospital-assigned LOINC codes, ODbL, no patient data. See [docs/data-and-scenarios.md](docs/data-and-scenarios.md).
- [x] Confirm the LOINC licence lets us bundle a subset. **Yes, 24 Nov:** records may be deleted, fields not changed. Still to find: the exact notice text to include.
- [ ] **MIMIC 269 unit-dependence audit**: inspect the 269 LOINC-coded MIMIC rows and tag each as unit-dependent (e.g., Mass/vol vs. Moles/vol) vs. unit-invariant to prepare the **S2a vs. S2b** benchmark.
- [ ] Clear remaining P0 checks: MSH-11 table 0103 values, a cited source for SI vs. conventional units, LOINC synonym field names, web check that our fictional hospital names are not real. *(eICU check moved to stretch with S4.)*
- [ ] Clear every CHECK item in [research/challenge-requirements.md](research/challenge-requirements.md), including age (18+).
- [ ] Spot-check 5 cited sources directly.

**G1 (Wed 25 Nov):** verify all scope locks and open questions in `DECISIONS.md` and `PLAN.md` §9.

### P1 Spike (Thu 26 to Sat 28 Nov · 9 hrs)
- [ ] **Scorer first, with unit tests:** `+1 / 0 / -5`, plus accuracy, abstain rate, wrong-confident rate, and bootstrap 95% confidence intervals.
- [ ] **LOINC subset:** ~100–300 common lab tests including every code present in the MIMIC 269 test set (fields unchanged; LOINC notice included).
- [ ] **Dialect generator:** clean result + hospital profile → HL7 v2 `ORU^R01` (`MSH-11=T`, `SYNTHETIC^PATIENT`) with hospital quirks (local codes, abbreviations, SI vs. conventional units, missing fields) and exact ground truth.
- [ ] **4-way fair baseline matrix** on ≥200 held-out mappings:
  1. Lexical / string-similarity match
  2. Forced-choice single LLM call with retrieval (LabBridge-style)
  3. **Abstain-prompted single LLM call** with retrieval (explicitly told `+1 / 0 / -5`)
  4. Dialect evolved network (deterministic UCUM guardrail + error-trace disambiguation rules)
- [ ] **Disentangled transfer test (S1a vs. S1b):** tune on Hospital A, run on Hospital B, adapt on B, and evaluate separately on:
  - **S1a Repeat-Code Cache**: previously approved codes (0 LLM tokens, workload savings)
  - **S1b Strictly Disjoint Unseen-Code Generalization**: brand-new Hospital B codes using B's approved examples as few-shot dialect hints
- [ ] **MIMIC S2a vs. S2b sanity check:** verify unit-dependent rows abstain cleanly without site unit mode (S2a) and recover coverage once Hospital R sets US conventional units in `sly_data` (S2b), keeping MIMIC strictly held-out.
- [ ] **Error-trace rule mutation prototype:** confirm mutating concrete disambiguation rules from `-5` traces (specimen conflict, calculated-vs-measured, missing-unit escalation, panel-vs-analyte) alongside retrieval `k` and abstain threshold improves held-out score.

**G2 (Sat 28 Nov):** evaluate tightened kill rule (section 5) and record result in `DECISIONS.md`.

### P2 Stage One + Validator v1 + 2-Origin Smoke Test (Sun 29 Nov to Sun 6 Dec · 14 hrs)
Build Hospital A, Validator v1, and a minimal Hospital B cross-origin smoke test together so cross-origin `nsDeploy` risk is retired in Week 2.

Hospital A (Origin A, public Vercel URL):
- [ ] `agent-manifest.json` (networks, default router & model, `peer_policy`, `allowed_peers`)
- [ ] Fully resolved Neuro SAN `.json` agent network with front agent
- [ ] `links.json` peer-network link map + `bridge/` page + CORS headers on every public artifact
- [ ] `nsDeploy` browser runtime + clean interface
- [ ] **Deterministic client-side PHI stripper** in plain TypeScript (`PID`, `PV1`, `NK1`, `NTE`, patient IDs/timestamps removed before any LLM call) + unit tests
- [ ] **Schema-locked default no-key proxy** (same-origin Vercel function accepting only lab-mapping payloads, per-IP rate limit, hard provider daily spend cap, and clearly labelled recorded-run replay fallback)
- [ ] BYOK settings (key stays strictly in browser) + **1-click Local / On-Prem LLM preset** (`localhost:11434/v1`)
- [ ] WebMCP-aligned confirmed local site action (`save_mapping`) displaying destination and arguments before writing
- [ ] Visible error stops for all 4 spec conditions: invalid network, composition cycle, over-budget request, denied origin
- [ ] Mandatory banner on every mapping screen: *"Synthetic scenario. Real standards and public lab names; fictional hospitals and patients. Not for clinical use. A human must approve."*

Validator v1 (static page fetching public files only):
- [ ] Checks any site URL for manifest, Neuro SAN JSON, `links.json`, CORS headers, front agent, exact-name resolution, and schema validity
- [ ] Displays pass/fail per spec section with spec citation and concrete fix hint
- [ ] Exports JSON conformance report AND generates an embeddable **`OAW Spec 7/7 Pass` SVG badge** + markdown snippet

De-risking & CI:
- [ ] **Week-2 Cross-Origin `nsDeploy` Smoke Test:** deploy minimal Hospital B on a second Vercel origin (`*.vercel.app`) and verify explicit origin consent, exact-name load of Hospital A's network, and CORS end-to-end
- [ ] **Starter CI `verify:docs` guard** in GitHub Actions checking manifest/network/`links.json` JSON validity and unit tests on every push

**G3 (Sun 6 Dec):** Validator v1 passes all checks on live Hospital A in a fresh browser profile with no key, AND minimal Hospital B passes the 2-origin peer-load smoke test.

### P3 Core Workbench & Privacy Proof (Mon 7 to Sun 20 Dec · 20 hrs)
Week 1 (7–13 Dec):
- [ ] Package generator, LOINC subset, and scorer into `profiles/lab-hl7/` conforming to an open **`DialectProfile` v1 JSON schema** (`profiles/dialect-profile.schema.json`).
- [ ] Full multi-agent network: HL7 parser agent, mapper agent, deterministic plain-code UCUM unit guardrail, front agent.
- [ ] "Ask a human" review queue showing top-3 LOINC candidates, exact abstention reason, and **visible stripped-payload preview** (showing zero PHI sent to the router).
- [ ] **Mid-Dec Community Launch (~10–13 Dec):** post **Conformance Validator v1 + SVG badge** and `DialectProfile` v1 schema in Cognizant's `agentic-web` GitHub Discussions so other entrants test their sites and cite it before judging.

Week 2 (14–20 Dec):
- [ ] Complete **Hospital B** workbench on Origin B: origin consent card, exact-name peer load from Hospital A, approve/fix/skip review queue writing to B's private `sly_data` alias table, live accuracy/score update.
- [ ] **1-Click Exports (confirmed local action `export_concept_map`):** download valid **FHIR R4 `ConceptMap` JSON**, interface-engine **CSV** (Mirth/Rhapsody/Cloverleaf compatible), and **S3 LIS Drift-Diff audit report**.
- [ ] **Live Egress & 0-Byte Privacy Receipt Inspector** inside Hospital B: instruments outbound `fetch()` + `PerformanceResourceTiming` to show a real-time origin ledger, payload hashes, and an exportable JSON receipt proving `0 bytes` of private context, alias table, or BYOK key reached Origin A (replaces standalone Validator v2 harness).
- [ ] Live "Conformance" panel on the site verifying all 7 spec interoperability checks.

**G3b (Sun 20 Dec):** full two-origin Hospital A↔B workbench live, Validator v1 launched in Discussions, and **first mock judging** completed (section 6).

### P4 Evolution, Empirical Proof & Polish (Mon 21 Dec to Sun 3 Jan · 13 hrs, holiday-light)
- [ ] Run offline **error-trace symbolic evolution** in `evolve/` (population 8, ~10 generations, held-out fitness) mutating concrete disambiguation rules from `-5` traces.
- [ ] Publish every generation's resolved Neuro SAN JSON, held-out score, and a readable **`Gen 0 → Gen N` rule diff** view on Hospital A.
- [ ] Run the full benchmark suite (section 7) using cached eval scripts and publish `results.json` + interactive/clean results cards:
  - 4-way fair baselines with **bootstrap 95% CIs** & **Risk-Coverage / Cost Pareto chart**
  - Disentangled S1a (Repeat-Code Cache) vs. S1b (Unseen-Code Generalization) curves
  - S2a vs. S2b MIMIC-IV 269 real-world results (strictly held-out, reported separately from generated data)
  - S3 LIS migration drift + drift-diff audit results
- [ ] **"Try your own code" box:** visitor enters any local lab name/unit and sees a mapping, stripped payload preview, or honest "ask a human".
- [ ] Expand **CI `verify:docs`** to verify `docs/facts.md` ↔ `results.json` number parity and block forbidden privacy/scope wording drift.
- [ ] Optional polish (only if ahead of schedule): add NANDA Index discovery descriptor.
- [ ] **3-person unassisted timed user test** (target: <3 minutes end-to-end from batch load to `ConceptMap` export); fix UX friction and record exact completion times in `docs/facts.md`.
- [ ] Capture a **2-sentence quoted review** (with permission) from one real hospital IT, HIMS, or lab informatics practitioner.

**G4 (Sun 3 Jan): feature freeze** + **second mock judging**.

### P5 Submission (Mon 4 to Sat 9 Jan · 12 hrs)
- [ ] **Demo video** (done **Thu 7 Jan**): under 2 minutes, **your own voice (not AI-generated)**, live browser capture, public on YouTube with uploaded exact captions. First 30 seconds hit the S1 unseen-code transfer curve and `-5` refusal; middle 60 seconds show `Gen 0 → Gen N` rule diff, PHI stripper + 0-Byte Privacy Receipt, and 1-click FHIR `ConceptMap` export; closing 30 seconds show Conformance Validator v1 + community badge.
  - **Video trademark hygiene:** no copyrighted music and no third-party vendor brand logos/trademarks on screen (use generic on-screen labels such as *"hospital interface engines"* and *"local OpenAI-compatible server"*, and follow Regenstrief's LOINC® notice rules).
- [ ] Devpost text following section 6 (exact criterion headings, sourced claims from `docs/facts.md`, Otari email + feedback if Otari is used).
- [ ] Annotated screenshots, each with a one-line caption stating the exact claim it proves.
- [ ] Final README update with live URLs, live Validator v1 links, and measured numbers copied verbatim from `docs/facts.md` / `results.json`.
- [ ] Verify **CI `verify:docs`** is green on `main`, test both origins on mobile and laptop in fresh browser profiles (no key), and run Validator v1 on both live origins one final time.
- [ ] **Submit Sun 10 Jan 2027.** Keep sites live through **18 Jan 2027, 5:00 pm PT**.

---

## 5. Kill rule (G2, Sat 28 Nov)

Mean score per mapping runs from `-5` to `+1` (a 6-point range). **Drop or pivot the idea if either condition fails:**
1. **Fair Baseline Win:** The evolved Dialect network fails to beat **both** the forced-choice single LLM call **and** the **abstain-prompted single LLM call** by at least **+0.6 mean points per mapping** on at least **200 held-out mappings** (bootstrap 95% CI reported).
2. **True Unseen-Code Transfer:** On **strictly disjoint unseen local codes** at Hospital B (excluding exact-alias cache hits), accuracy before adapting does not drop at least **10 points** below Hospital A, OR adapting via B's approved few-shot dialect hints does not recover at least **half** of that drop.

If either fails, pivot to a fallback idea (Silicon Breeders, Jury of Strangers, or Safe Generative UI) while keeping Conformance Validator v1 (which applies to any entry). Record the Gate G2 decision and raw numbers in `DECISIONS.md` either way.

---

## 6. Passing both AI screening and human judges

The rules allow automated AI-driven analysis, and judges need not watch past 2 minutes. An AI screener parses headings, JSON files, links, and cross-doc consistency; a human judge looks for real-world credibility and one unforgettable proof moment. We engineer for both.

### What an AI screener checks, and how we pass cleanly
| An AI screener will likely… | So we… |
|---|---|
| Match text against each judging criterion | Use the four exact criterion names as headings (*Technological Implementation*, *Design*, *Potential Impact*, *Quality of the Idea*) and answer each in the first two sentences |
| Verify Stage One conformance | Map every spec requirement to a live URL, ship a live Conformance panel, and attach our own Validator v1 JSON report + `OAW Spec 7/7 Pass` badge |
| Parse published JSON artifacts | Validate `agent-manifest.json`, Neuro SAN JSON, `links.json`, `DialectProfile` schema, `results.json`, and 0-Byte Privacy Receipt JSON in CI |
| Check claims against evidence | Attach a number, live link, or screenshot to every claim; mark anything unmeasured as `Pending` or `UNVERIFIED` |
| Catch contradictions & privacy overclaims | Maintain `docs/facts.md` as the single source of truth, enforce harmonized privacy boundaries (peer 0-byte vs. PHI-stripped router vs. On-Prem preset), and run **CI `verify:docs`** on every push |
| Read the video transcript | Upload exact human-checked captions to YouTube |
| Check novelty vs. prior art | Explicitly name RELMA and LabBridge (81–90%) and state exact deltas (peerless browser transfer, `-5` scored abstention, error-trace symbolic evolution, FHIR export) |
| Audit synthetic vs. real data hygiene | Label every scenario synthetic in UI banner, `scenario.json`, HL7 `MSH-11=T`, `DATA.md`, and Devpost; cite MIMIC-IV v1.0 (ODbL, strictly held-out) and LOINC licences cleanly |

**Never** add hidden text or prompt injections aimed at AI judges. Rules allow disqualification for tampering, and clean verifiable evidence scores higher.

### Four proof moments built for both human judges and AI screeners
1. **Disentangled transfer + MIMIC S2a/S2b proof:** unseen-code accuracy drops at Hospital B and climbs back after a handful of local approvals; on real MIMIC 269 rows, Dialect safely abstains without unit context (S2a) and recovers deterministically once site unit mode is set (S2b).
2. **Error-trace evolution & `-5` refusal:** side-by-side `Gen 0 → Gen N` rule diff showing an ambiguous code where single-call LLMs guess wrong (`-5`) and evolved Dialect asks a human (`0`).
3. **Live 0-Byte Privacy Receipt + 1-Click FHIR `ConceptMap` Export:** verifiable browser ledger proving `0 bytes` of private data/keys reached Origin A and zero PHI left the browser, finishing with a real downloadable `ConceptMap` JSON/CSV for hospital interface engines.
4. **Community Conformance Validator v1 + SVG Badge:** directly resolves the empty validation `TODO` in Cognizant's repo and ships early enough for real community adoption.

### Mock judging (G3b on 20 Dec and G4 on 3 Jan)
- [ ] Give two different AI models only the Devpost text, screenshots, video transcript, and live links; score all four criteria 1–10 using official rules wording and flag every unsupported or inconsistent claim.
- [ ] Ask one uninvolved human reviewer to score cold from the text and 2-minute video only.
- [ ] Fix the lowest-scoring criterion first; repeat at G4.

---

## 7. Experiments and published evidence (`results.json`)

Every published number uses held-out data the agents never saw, with results on real MIMIC names reported strictly separately from generated data.

| Experiment | What it proves | Published as |
|---|---|---|
| **S1a: Repeat-Code Cache** | Exact alias-table hits (0 LLM tokens) after analyst approval — operational workload savings | Results card + `results.json` |
| **S1b: Unseen-Code Generalization** | True dialect transfer on **strictly disjoint unseen codes** at Hospital B (before vs. after adapting) | Transfer curve + `results.json` |
| **4-Way Fair Baselines** | Lexical vs. Forced-Choice LLM vs. **Abstain-Prompted Single LLM** vs. Dialect Evolved on `+1 / 0 / -5`, wrong-confident/100, and **bootstrap 95% CIs** | Results card + Risk-Coverage chart |
| **S2a vs. S2b: Real MIMIC-IV 269** | Real hospital names (ODbL, strictly held-out): **S2a** (no unit convention → safe abstention) vs. **S2b** (+ site unit convention in `sly_data` → deterministic UCUM recovery) | Separate real-data results card |
| **S3: Lab System Switch (LIS Drift)** | Wrong confident answers per 100 after local codes change overnight + exportable Drift-Diff audit | Results card + sample drift report |
| **Evolution Lineage** | Score progression + concrete symbolic rules mutated from `-5` traces (`Gen 0 → Gen N`) | Visual rule-diff card + published JSONs |
| **Cost Pareto & Egress Privacy** | Score vs. tokens/message Pareto + live **0-Byte Privacy Receipt** JSON + 0-PHI payload preview | Pareto chart + downloadable receipt JSON |

---

## 8. Risks and the cut list

| Risk | Mitigation |
|---|---|
| Single AI call (with abstention prompt) already solves the task | Tightened Gate G2 kill rule on 28 Nov against both forced-choice and abstain-prompted LLM baselines |
| Transfer recovery looks like trivial alias caching | Disentangled S1a (cache) vs. S1b (strictly unseen codes) reporting; G2 requires recovery on unseen codes |
| MIMIC-IV `d_labitems` lacks measurement units | Turned into S2a vs. S2b feature showing honest abstention without unit context and deterministic UCUM recovery with site unit mode |
| ODbL share-alike contamination of MIT network JSON | MIMIC rows are strictly held-out evaluation data only, never embedded into prompts or evolved networks |
| Public no-key proxy drained by bots before judging | Schema-locked to lab-mapping payloads only + per-IP rate limit + hard provider daily spend cap + labelled replay fallback |
| Cross-origin `nsDeploy` / CORS / `bridge/` surprises | Pulled minimal 2-origin Vercel smoke test into Week 2 (P2, by 6 Dec) |
| Overclaiming or doc drift flagged by AI screening | Single `docs/facts.md` + harmonized privacy wording + automated **CI `verify:docs`** on every push |
| Low-memory dev machine (~7.6 GB RAM, no Python) | Pure Node/TypeScript stack; no Synthea/Java; run one heavy process at a time |

**Already cut at Gate G1 (moved to stretch):** Hospital C (`sites/hospital-c`), Scenario S4 (eICU intake), standalone Validator v2 cross-origin harness (replaced by built-in 0-Byte Privacy Receipt), Scenario S5 (non-healthcare Dialect Profile #2).

**Emergency cut order (only if hours slip despite the 12-hr buffer):** (1) optional NANDA Index descriptor, (2) extra polish on "Try your own code" box, (3) S3 Drift-Diff UI animations, (4) interactive Pareto chart → static SVG.

**Never cut:** Stage One two-origin sites + schema-locked no-key proxy, client PHI stripper + Live 0-Byte Privacy Receipt, 1-click FHIR `ConceptMap`/CSV export, Conformance Validator v1 + SVG badge, 4-way S1/S2a/S2b `results.json`, and the 2-minute video.

---

## 9. Open questions (confirm by Wed 25 Nov)

1. Are you 18 or older? *(Challenge eligibility requirement.)*
2. What hard daily spend cap (e.g., $1–$2/day) do you want on the public no-key Vercel proxy during judging?
3. Solo developer or adding a teammate?
4. Do you plan to use Mozilla Otari for any router path? *(If yes, record your Otari email and 1–2 sentences of product feedback for Devpost.)*
