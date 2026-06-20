# Deep Research — Node-Level Error Attribution in Agentic LLM Workflows

**Date:** 2026-06-19 · **Topic:** SOTA methods (2025→2026) for attributing/localizing failures to specific **nodes** (agent / module / step / span / graph-node) in *existing* agentic LLM workflows and multi-agent systems, to support conservative node-level fixes (QC loops, HITL gates, missing tool calls, prompt revisions).

This folder is the full archive of a `deep-research` multi-agent workflow run: **100 agents, ~3.4M tokens, 944 tool calls, ~23 min** (run `wf_56d0a8ee-1fa`, task `w48ktoy05`). Nothing is summarized away — every source reviewed, every extracted claim, and every adversarial verification vote is preserved below.

---

## The research question (verbatim)

> Survey the most recent state-of-the-art (prioritize 2025, then 2024) methods for ATTRIBUTING and LOCALIZING errors/failures to specific NODES in existing agentic LLM workflows and multi-agent systems… [node = a discrete workflow-graph component: node/agent/module/block/component/tool-call step; NOT a pure chain-of-thought reasoning step unless the method bridges step-level signals up to node/agent-level blame]. Cover (1) leading methods/benchmarks, (2) terminology deep-dive, (3) causal/counterfactual attribution, (4) actionable localization enabling targeted conservative fixes. Exclude pure ProcessBench-style PRM step-scoring, pure textual-gradient/prompt-optimization (TextGrad/DSPy), and from-scratch workflow generators (AFlow/ADAS/GPTSwarm).

Full question + the 4 sub-questions: see `01-REPORT.md`.

---

## Headline answer (TL;DR)

The field crystallized in **May 2025** around the **Who&When** benchmark (ICML 2025 Spotlight), which fixed the attribution unit the whole subfield now uses: a **(failure-responsible agent, decisive error step)** pair, where the *decisive error step* = **the earliest mistake whose correction flips failure→success**. Who&When also originated the three reference algorithms — **All-at-Once / Step-by-Step / Binary Search** — and showed the task is hard (best baseline ~53.5% agent / ~14.2% step).

The 2025→2026 frontier is a wave of **causal / counterfactual** methods that bridge step-signal up to node/agent blame: **A2P** (Pearl SCM in one pass), **AgenTracer** (counterfactual replay + fault injection → trained 8B tracer), a **Shapley + Performance-Causal-Inversion** framework, **CHIEF** (hierarchical causal graph), and **DoVer** (Microsoft — intervention-as-verification). The single most important *mindset* finding for conservative optimization: **DoVer argues single-node attribution is ill-posed** (multiple distinct edits can fix the same failure), so you should **apply the candidate fix as an intervention and verify the outcome delta** rather than trusting the blame label — the direct antidote to false-positive "fixes."

> **Note for this project:** the research independently surfaced **CHIEF** (arXiv 2602.23701) and its headline **77.59% agent-level / 29.31% step-level on Who&When hand-crafted** — the same paper and number your `CHIEF-trial repro` work has been probing (your reproduction lands at 46.55% agent-level). See `06-RANKING-AND-DISPOSITION.md` §"Cross-method numerical coincidences" for the `x/58` analysis (A2P and CHIEF *both* report 29.31% step-level = 17/58).

---

## File guide

| File | What it is | Authored / Generated |
|---|---|---|
| `00-INDEX.md` | This file — overview, headline, navigation, stats | hand |
| `01-REPORT.md` | The full synthesized report: methods grouped by unit & technique, **comparison table**, four-fix-type mapping, synthesis | hand |
| `02-METHODOLOGY-AND-PROVENANCE.md` | How the workflow ran (5 phases, agent math, verification rule), how the data was extracted, limitations, reproduction | hand |
| `03-SOURCES-all.md` | **Every** search result (30) grouped by angle, with relevance, snippet, and disposition (fetched / duplicate / budget-dropped) | generated |
| `04-CLAIMS-all-extracted.md` | **All 88** extracted claims grouped by source, with quote, importance, and verification disposition | generated |
| `05-VERIFICATION-votes.md` | **All 25 claims × 3 votes** with each voter's refute/uphold, confidence, counter-source, and evidence | generated |
| `06-RANKING-AND-DISPOSITION.md` | The analysis: **why** each source/claim was ranked, surfaced, down-weighted, killed, or omitted | hand |
| `_data/*.json` | Machine-readable: scope, search, fetch, verify outputs + the final synthesized result | extracted |

**Suggested reading order:** `00` → `01` → `06` (the analysis), then dip into `03`/`04`/`05` for raw evidence, `02` for provenance.

---

## Methods surfaced (with verification status)

| Method | arXiv / src | Node unit | Technique | In verified top-25? |
|---|---|---|---|---|
| **Who&When** (benchmark) | 2505.00212 | agent + decisive step | All-at-Once / Step-by-Step / Binary Search | ✅ confirmed |
| **A2P** (Abduct-Act-Predict) | 2509.10401 | (agent, step) + root cause | Pearl SCM `do()`, in-context counterfactual, 1 pass | ✅ confirmed (2 split votes) |
| **AgenTracer** (8B tracer) | 2509.03312 | agent + decisive step | real counterfactual replay + fault injection | ✅ confirmed |
| **Shapley + PCI** | 2509.08682 | agent-step-context triple | Shapley blame + Performance-Causal-Inversion | ✅ confirmed |
| **DoVer** (Do-then-Verify) | 2512.06749 | agent/step hypothesis, intervention-validated | hypothesize→edit→re-run→differential eval | ✅ confirmed (1 claim killed) |
| **CausalFlow** | 2605.25338 | step (single-agent) | step-level counterfactual CRS | ✅ confirmed |
| **MS Taxonomy of Failure Modes** | MS blog Apr'25 | failure *types* (no localizer) | taxonomy only | ✅ confirmed |
| **AgentDebug** | 2509.25370 | module (memory/reflection/planning/action/system) | root-module attribution + corrective feedback | ⚪ fetched, not in top-25 |
| **AgentRx** (Microsoft) | MS blog 2026 | Critical Failure Step + 9-cat taxonomy | guarded constraints, step-by-step | ⚪ fetched, not in top-25 |
| **CHIEF** | 2602.23701 | (agent, timestep), hierarchical | causal hierarchical graph + counterfactual screening | ⚪ fetched, not in top-25 |
| **MAST** | 2503.13657 | failure mode × MAS stage | empirical taxonomy + LLM-judge annotator | ⚪ fetched, not in top-25 |
| **TRAIL** | 2505.08638 | OpenTelemetry **span** | trace error localization benchmark | ⚪ fetched, not in top-25 |
| **GraphTracer** | 2510.10581 | graph node (Info-Dependency Graph) | graph-guided tracing | ⚠️ **WITHDRAWN by authors** (methodology error) |
| **C3** | github EIT-EAST-Lab | per-decision (MARL) | fixed-context counterfactual replay + leave-one-out | ⚪ fetched (adjacent: MARL training) |
| **AgentSHAP** | 2512.12597 | tool/component | Monte-Carlo Shapley ablation | ⚪ budget-dropped (search only) |

⚪ = the source was fetched and its claims extracted (`04-CLAIMS-all-extracted.md`) but none of its claims landed in the 25 adversarially-verified set — so its accuracy figures are **single-source, not independently checked here**. See `06` for why.

---

## Stats

| | |
|---|---|
| Search angles | 5 |
| Search results returned | 30 (→ 18 fetched, 11 duplicates, 1 budget-dropped) |
| Sources fetched & extracted | 18 |
| Claims extracted | 88 |
| Claims adversarially verified | 25 (top-ranked) |
| → confirmed / killed | 24 / 1 |
| Findings after synthesis | 16 |
| Agents | 100 (1 scope + 5 search + 18 fetch + 75 verify + 1 synthesize) |
| Subagent tokens | ~3,403,275 |
| Tool calls | 944 |
| Wall-clock | ~23.1 min |

---

## ⚠️ Confidence banner — read before citing any number

- **Only Who&When (2505.00212) is peer-reviewed** (ICML 2025 Spotlight). Every node-level method below it (A2P, AgenTracer, Shapley/PCI, CHIEF, CausalFlow, DoVer) is a **2025–2026 arXiv preprint with self-reported, single-source, not-independently-replicated** numbers.
- **A2P (2509.10401)** carries a *"Discovered and authored by DeepScientist"* footer (≈95% AI-authored) — its method description is fine, but treat its 47.46% / 29.31% figures with suspicion.
- **GraphTracer (2510.10581) was WITHDRAWN** by its authors (v2, 2025-12-22) for a methodology error — do **not** cite its numbers.
- **DoVer's specific repair-rate claim was KILLED** in verification (0-3) — not because the paper lacks the numbers (18% AssistantBench / 28% GAIA / 49% GSMPlus *are* in the paper) but because the extracted claim bundled them with an internally contradictory "~27.5%" parenthetical. Cite DoVer's *method*, verify its rates against the paper directly.
- "**Causal**" is used in two senses: **A2P** *simulates* counterfactuals inside one LLM pass (no real re-execution); **AgenTracer / DoVer / CHIEF / CausalFlow** perform **real replay/intervention**. Only the latter are genuinely interventional.

Full caveats: `01-REPORT.md` §Confidence and `06-RANKING-AND-DISPOSITION.md`.
