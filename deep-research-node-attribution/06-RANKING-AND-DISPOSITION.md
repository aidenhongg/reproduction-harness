# 06 — Ranking & Disposition: why each source/claim was surfaced, ranked, killed, or omitted

This is the audit trail tying the raw data (`03`/`04`/`05`) to the report (`01`). It answers: *why was each source fetched or dropped? why were these 25 of 88 claims verified? why did some claims die or split? why did some methods become prominent findings and others vanish?*

---

## A. Source disposition — search (30) → fetch (18)

The 5 search agents returned **30 results**. The pipeline ranked each angle's results by relevance, then dropped duplicates and budget-overflow before fetching.

| Disposition | Count | Rule |
|---|---|---|
| **Fetched** | 18 | unique (by normalized URL) + within the 15-source budget (high-relevance bypass the cap) |
| **Duplicate** | 11 | normalized URL already seen in an earlier angle → dropped before fetch |
| **Budget-dropped** | 1 | unique but relevance below cutoff once budget filled → **AgentSHAP (2512.12597)**, the only casualty |

**Why the duplicates happened (and what it tells you):** the anchor papers were found by *multiple* angles, which is a good signal of centrality. **Who&When (2505.00212) was independently surfaced by 4 of 5 angles** (broad, terminology, frontier — kept once, deduped 3×). **AgenTracer (2509.03312)** by 4 angles. That cross-angle agreement is *why* these sit at the center of the report — the fan-out voted with its feet.

**⚠️ A real dedup miss worth knowing (affects how you read "18 sources"):** the URL normalizer keyed on `host + path`, so it treated `arxiv.org/abs/X`, `arxiv.org/pdf/X`, `arxiv.org/html/X`, and `openreview.net/pdf/…` as **distinct**. Three papers were therefore **fetched twice** under different URL forms:

- **2509.08682** (Shapley/PCI) — `html/` (broad angle) **and** `abs/` (frontier angle)
- **2512.06749** (DoVer) — `abs/` (practitioner) **and** `pdf/` (academic)
- **2509.03312 / openreview** (AgenTracer) — `arxiv abs/` (terminology) **and** `openreview pdf/` (academic)

So the **18 fetched "sources" are ~15 distinct papers.** This is benign for coverage (it just produced extra claims for those three papers) but means the "18" overstates distinct papers, and it gave those three papers extra claim-slots in the ranking pool (see §B). The 18 fetched sources, with their distinct-paper status:

| # | Paper | URL form | Found via angle | Claims | Verified claims |
|---|---|---|---|---|---|
| 1 | Shapley/PCI 2509.08682 | html | broad | 5 | **5** |
| 2 | Shapley/PCI 2509.08682 | abs (dup paper) | frontier | 5 | 0 |
| 3 | MS Taxonomy (security blog) | — | broad | 5 | **3** |
| 4 | CausalFlow 2605.25338 | abs | academic | 5 | **2** |
| 5 | AgenTracer (openreview) | pdf | academic | 5 | **4** |
| 6 | AgenTracer 2509.03312 | abs (dup paper) | terminology | 5 | 0 |
| 7 | DoVer 2512.06749 | abs (dup paper) | practitioner | 5 | 0 |
| 8 | DoVer 2512.06749 | pdf | academic | 5 | **4** (1 killed) |
| 9 | A2P 2509.10401 | pdf | academic | 5 | **4** |
| 10 | Who&When 2505.00212 | abs | terminology | 5 | **3** |
| 11 | CHIEF 2602.23701 | html | terminology | 5 | 0 |
| 12 | GraphTracer 2510.10581 | abs | terminology | 4 | 0 |
| 13 | MAST 2503.13657 | abs | terminology | 5 | 0 |
| 14 | AgentDebug 2509.25370 | abs | frontier | 5 | 0 |
| 15 | AgentRx (MS Research blog) | — | practitioner | 5 | 0 |
| 16 | AgentFixer 2603.29848 | html | practitioner | ~5 | 0 |
| 17 | C3 (github EIT-EAST-Lab) | — | academic | 4 | 0 |
| 18 | TRAIL 2505.08638 | — | broad/terminology | 5 | 0 |

*(Note: Who&When was fetched once but found by 4 angles; TRAIL similarly surfaced in two angles. The "found via" column shows the fetch's angle.)*

---

## B. Claim disposition — 88 extracted → 25 verified

The ranker sorted all 88 claims by **importance** (central → supporting → tangential), then **source quality** (all 18 sources were `primary`, so this tiebreaker was inert), and verified the **top 25**.

**The single most important caveat about coverage:** because every source is `primary` and there are **far more than 25 "central" claims** (~50+), the cutoff reduced to *"the first 25 central claims in fetch-completion order."* **Verification coverage was therefore determined as much by which fetch agents finished first as by genuine importance.** Concretely:

- **All 25 verified claims come from just 7 fetch slots / 6 distinct papers:** Shapley-PCI (html, 5), A2P (4), AgenTracer-openreview (4), DoVer-pdf (4, 1 killed), MS-Taxonomy (3), Who&When (3), CausalFlow (2).
- **Eight distinct fetched papers contributed ZERO verified claims** — **CHIEF, GraphTracer, MAST, AgentDebug, AgentRx, AgentFixer, C3, TRAIL** — not because their claims were weak (most were rated `central`/`primary`) but because they lost the fetch-order tiebreak and fell below the top-25 line.

**This is why the report carries CHIEF, AgentDebug, AgentRx, MAST, TRAIL as "fetched, not independently verified here" (⚪) rather than as confirmed findings.** Their accuracy numbers in `01-REPORT.md` §4 are single-source and were *not* put through the 3-vote gauntlet. If you want them at the same confidence as A2P/AgenTracer/DoVer, that's the obvious next batch to verify (see §G).

---

## C. Vote-level rationale — the 1 kill and the 4 splits

24 of 25 confirmed; 1 killed; 4 of the survivors were 2-1 (genuine dissent). Full evidence in `05-VERIFICATION-votes.md`; the rationale:

### ❌ KILLED (0-3) — DoVer repair-rate claim (`2512.06749`)
All three voters refuted — but **the kill is about the *claim's wording*, not DoVer's results.** Voter 2 pinned it: the claim asserted *"a 49% trial flip rate (~27.5% reported on GSMPlus trials in Table 2)"* — **Table 2 shows GSMPlus = 49.0%, not 27.5%**, an internal contradiction. Voters 1 and 3 confirmed the rest of the numbers (18% AssistantBench, 28% GAIA, 30-60% hypothesis validation) *are* in the paper, but the bundled error + single-source marketing-flavored framing sank it. **Implication:** DoVer's method and its real repair rates survive; this particular *sentence* did not. Don't cite the sentence; cite the paper.

### ◐ 2-1 SPLITS (confirmed, but flagged)
1. **AgenTracer "core algorithm is causal/counterfactual"** — dissenting voter 1 accepted the *mechanism* (replay + fault injection, verbatim) but refuted the *characterization*, arguing the paper's "all-at-once" terminology refers to the trained tracer's inference setting, not the annotation pipeline. A framing nuance, not a substantive error → correctly confirmed.
2. **"Automated failure attribution is far from solved" (Who&When 53.5/14.2)** — voter 3 refuted as *"overreached + outdated"*: the numbers are verbatim-correct, but 14.2% is the May-2025 baseline since beaten ~2-3× (A2P, AgenTracer, Shapley/PCI), and the claim slightly conflates best-method ceilings with the separate o1/R1-usability sentence. The report reflects this (the "understates the present ceiling" caveat).
3. **A2P SOTA numbers (47.46/29.31)** — voter 2 refuted on **source quality**: numbers are verbatim from Table 1, but the paper is *"~95% AI-authored"* (DeepScientist) and the "state-of-the-art" framing fails scrutiny (tiny deltas: 29.31 vs 28.57 next-best ≈ one sample on n=58). Confirmed but explicitly down-weighted to *medium* in the report.
4. **A2P "actionable causally-grounded localization"** — voter 2 refuted citing a **fabricated worked example** ("HotelAgent at Step 4 misinterpreted 'tomorrow'") absent from all sources, plus the AI-authorship and the fact that "causal" = single-pass simulation with no real intervention or explanation-correctness eval. (Note: the *genuine* worked example of this kind belongs to the **Shapley/PCI** paper — "WebSurfer failed at step 3… ambiguous API instruction" — which is real.)

**Pattern across the dissents:** the verifiers never disputed *mechanism* descriptions; every refute targeted **provenance** (AI-authored), **recency** (outdated baseline), **framing** (overreach), or **fabricated detail**. That's the adversarial layer doing its job — and it's why the report's confidence ratings track these exact axes.

---

## D. Finding-level — 24 confirmed claims → 16 synthesized findings

Synthesis merged semantic duplicates. The biggest merges (why the report reads as method-centric, not claim-centric):

- **AgenTracer** appeared via 2 papers × multiple claims (openreview + arxiv abs) → merged into ~3 findings (unit, causal mechanism, actionability).
- **Shapley/PCI** contributed 5 verified claims (the html fetch swept its whole claim set) → became the most thoroughly-verified single method (framework, PCI principle, Shapley, accuracy, closed-loop repair all 3-0).
- **DoVer** → 3 surviving findings (the Do-then-Verify method, the ill-posedness argument, the 6%→24% prompt-formatting reproduction) + 1 killed.
- **A2P** → ~3 findings, all carried at *medium* confidence per the §C dissents.
- **Who&When** → the foundational-frame findings (unit + 3 algorithms + difficulty).
- **MS Taxonomy** → the "vocabulary, not a localizer" finding (notably, a verified claim explicitly states the whitepaper provides *no* attribution algorithm — an adversarial counter-search confirmed Microsoft later built a *separate* localizer, AgentRx).

**Why the report's prominence ranking looks the way it does:** prominence ≈ (verified-claim count) × (confidence). Shapley/PCI and AgenTracer rose because the fan-out verified many of their claims at high confidence; A2P is prominent but capped at medium (provenance); CHIEF/AgentDebug/AgentRx are real and relevant but sit in the "fetched, unverified" tier purely due to the §B cutoff.

---

## E. Down-weighting & exclusion decisions (explicit)

| Decision | Applied to | Why |
|---|---|---|
| **Confidence → medium** | A2P numbers & actionability | ~95% AI-authored ("DeepScientist"); tiny deltas; "causal" = simulation not intervention |
| **Do-not-cite numbers** | GraphTracer (2510.10581) | **withdrawn** by authors v2 2025-12-22 (methodology error) — surfaced by a verifier's quote "A newer version… has been withdrawn" |
| **Cite method, verify rates separately** | DoVer repair rates | bundled claim killed for internal contradiction; underlying numbers real |
| **Boundary / low node-relevance** | CausalFlow (2605.25338) | single-agent step-granularity; does not bridge to multi-agent node blame |
| **Adjacent, not drop-in** | C3 (github) | MARL *training* credit assignment, not post-hoc workflow diagnosis |
| **Fetched, unverified here** | CHIEF, AgentDebug, AgentRx, MAST, TRAIL, AgentFixer | §B cutoff — central/primary but below top-25 by fetch-order |

---

## F. Cross-method numerical coincidences (worth your scrutiny)

The Who&When **hand-crafted subset is n=58**, so every "HC" accuracy is some `k/58`. Laid out, two coincidences stand out:

| Reported % | As k/58 | Method(s) | Note |
|---|---|---|---|
| 29.31% | **17/58** | **A2P** (step HC) **and CHIEF** (step HC) | *identical* step-level number from two unrelated methods |
| 46.55% | 27/58 | (your CHIEF reproduction, per project notes) | |
| 50.00% | 29/58 | All-at-Once agent baseline (CHIEF's table) | |
| 51.70% | 30/58 | Binary Search agent (CHIEF's table) | |
| 58.62% | 34/58 | A2P agent HC | |
| 68.40% | ≈39.7/58 | ECHO agent (CHIEF's table) | non-integer → different denom or rounding |
| 77.59% | **45/58** | **CHIEF** agent HC (paper's headline) | |

Two observations a node-attribution researcher should not ignore:

1. **A2P and CHIEF both report exactly 29.31% step-level on HC.** Either a genuine shared ceiling on the 58-case set, or a sign that different methods converge on the same handful of "easy" decisive steps. Given A2P's AI-authorship and CHIEF's single-source status, the coincidence is worth a direct look rather than treating both as independent SOTA points.
2. **The recurring "+18.18%" margin** is claimed by **both AgenTracer and GraphTracer** (and GraphTracer is *withdrawn*). 18.18% = 2/11. Two "-8B" tracers reporting the identical headline margin on the same benchmark suggests shared lineage or a margin-vs-one-specific-baseline artifact — scrutinize before citing either as an independent result.

> **Project tie-in:** the research independently surfaced **CHIEF (2602.23701)** and its **77.59% agent / 29.31% step (HC, w/GT)** — the exact paper and headline your `CHIEF-trial repro` work targets, where your faithful reproduction lands at **46.55% (27/58)**. This report classifies CHIEF's 77.59% as **single-source, self-reported, not independently verified** (it fell below the §B verification cutoff) — consistent with a number that doesn't reproduce. The deep-research run is corroborating evidence that CHIEF's headline is unreplicated in the public record, not just in your harness.

---

## G. What the exclusion filter removed (and the recommended next batch)

**Deliberately excluded by the question** (and respected by the run — none surfaced as findings): pure ProcessBench-style PRM step-scoring; pure textual-gradient/prompt-optimization (TextGrad, DSPy); from-scratch workflow generators (AFlow, ADAS, GPTSwarm-as-generator). The scope agent encoded these exclusions into all 5 angle queries, which is why the 30 results contain essentially none of that literature.

**Highest-value next verification batch** (to lift the ⚪ methods to the same confidence as the core):
1. **AgentDebug (2509.25370)** — its module+error-type taxonomy is the best fit for your four fix-types; verify the +24%/+26% numbers.
2. **CHIEF (2602.23701)** — verify the 77.59% directly given §F and your reproduction.
3. **AgentRx (Microsoft 2026)** — Microsoft's *actual* localizer (the April-2025 taxonomy is not one); verify +23.6%/+22.9%.
4. **MAST (2503.13657)** & **TRAIL (2505.08638)** — the failure-category vocabulary + the span-level localization benchmark, both named in your original brief.
5. **Agent-as-a-Judge** — never fetched; would need a fresh search angle.
