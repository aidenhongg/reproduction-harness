# 01 — Report: Node-Level Error Attribution in Agentic LLM Workflows (SOTA 2025→2026)

## The question

Survey the most recent SOTA methods for **attributing/localizing errors to specific NODES** in *existing* agentic LLM workflows and multi-agent systems — where a "node" is a discrete workflow-graph component (node / agent / module / block / component / tool-call step), **not** a pure chain-of-thought reasoning step unless the method bridges step-signal up to node/agent blame. Four sub-questions:

1. Leading methods/benchmarks for automated failure attribution — for each: attribution **unit**, **algorithm** (all-at-once / step-by-step / binary-search / causal-counterfactual), **inputs** (trace/log/graph), reported **accuracy**.
2. **Terminology** deep-dive: node vs block vs chunk vs span vs module vs agent vs step — which map to workflow-graph-node-level (relevant) vs token/reasoning-step (less relevant).
3. **Causal/counterfactual** approaches that pinpoint the responsible node (credit/blame, ablation, intervention, counterfactual replay).
4. **Actionable** localization — "node X is faulty, error category Y" → enabling a conservative targeted fix (QC loop, HITL gate, missing tool call, prompt revision) on the existing workflow.

**Excluded/down-weighted:** pure CoT step-verification with no node mapping (ProcessBench-style PRMs); pure textual-gradient/prompt-optimization (TextGrad, DSPy); from-scratch workflow generators (AFlow, ADAS, GPTSwarm-as-generator).

---

## Bottom line

The field crystallized in **May 2025** around **Who&When**, which fixed the vocabulary the whole subfield now uses: the target is a **(responsible agent, decisive error step)** pair, where the *decisive error step* is **the earliest mistake whose correction flips failure→success**. That "earliest-decisive" definition is the most important single concept for conservative optimization — it is *inherently FP-averse*: it says fix **one** node, not every node that merely looks wrong.

The 2025→2026 frontier is a wave of **causal/counterfactual** methods that bridge step-signal up to node/agent blame. The strongest are interventional: **AgenTracer** (real counterfactual replay + fault injection), **DoVer** (intervention-as-verification), **CHIEF** (hierarchical causal graph), and the **Shapley + Performance-Causal-Inversion** framework; **A2P** does the same logic *simulated* inside one LLM pass. The finding that should most reshape your approach: **DoVer argues single-node attribution is ill-posed** — multiple distinct edits can fix the same failure — so **apply the candidate fix and verify the outcome delta** rather than trusting the blame label. That is exactly the false-positive guard you want.

**Confidence up front:** only **Who&When is peer-reviewed**. Everything below it is 2025–2026 arXiv preprints with self-reported, not-replicated numbers; **A2P is ~95% AI-authored**; **GraphTracer is withdrawn**. Treat all accuracy figures as directional. (§8.)

---

## 1. The canonical frame — Who&When (the unit + the three algorithms)

**Who&When** — *Which Agent Causes Task Failures and When? On Automated Failure Attribution of LLM Multi-Agent Systems* — arXiv **2505.00212**, ICML 2025 **Spotlight** (Penn State + Duke + DeepMind). This paper defined the task and the vocabulary. 127 annotated LLM-MAS failure logs, each labeled with **(i) the failure-responsible agent** and **(ii) the decisive error step** (= "the earliest mistake whose correction would change failure into success"). Granularity is **conversation-turn / message**, not token.

It originated the three reference algorithms your taxonomy comes from:

| Algorithm | Mechanism | Trade-off |
|---|---|---|
| **All-at-Once** | whole trace, single query → name agent + step | cheap; full context; weak at precise step localization |
| **Step-by-Step** | walk the trace turn-by-turn, judge at each step | granular; myopic; error-accumulating |
| **Binary Search** | recursively halve the trace, narrow to the faulty region | efficient localization; log(n) queries |

**Result that motivates everything after:** best baseline = **53.5% agent / 14.2% step**; some methods below random; o1 and DeepSeek-R1 "fail to achieve practical usability." Useful nuance from the paper: *Step-by-Step best for the agent, Binary Search best for the step; combining methods and supplying ground-truth intermediate-step info both help.* Code/data: `github.com/ag2ai/Agents_Failure_Attribution`.

---

## 2. Terminology deep-dive — what actually counts as a "node" (Q2)

The jargon is messier than the underlying reality. **The subfield has effectively converged on one unit — the (agent, step) pair** — and the terminology differences are granularity choices *within* that, not rival ontologies:

| Term | Paper | What it actually means | Node-level relevant? |
|---|---|---|---|
| **agent** + **decisive error step** | Who&When (2505.00212) | agent = MAS participant; step = conversation turn | ✅ reference definition |
| **(agent, step) pair** | A2P (2509.10401) | step literally pre-formatted `Step {idx} - Agent_Name` | ✅ yes |
| **agent** + **decisive (i\*,t\*)** | AgenTracer (2509.03312) | earliest agent-step pair whose correction flips outcome | ✅ yes |
| **agent-step-context triple** `(a,s,c)` | Shapley/PCI (2509.08682) | node-set V of triples in a performance causal graph | ✅ yes (richest unit) |
| **Subtask Node / Agent Node → (agent, timestep)** | CHIEF (2602.23701) | two-level hierarchy: task-phase node + atomic agent-instance node | ✅ yes (explicit graph) |
| **graph node** (Info-Dependency Graph) | GraphTracer (2510.10581) | node = an agent output in a dependency graph; root-cause vs symptom nodes | ✅ yes — but ⚠️ **withdrawn** |
| **module** | AgentDebug (2509.25370) | one of {memory, reflection, planning, action, system} | ✅ yes (coarsest, most actionable) |
| **span** (OpenTelemetry) | TRAIL (2505.08638) | a tool-call / LLM-call span; nests hierarchically → a graph node | ✅ yes (observability unit) |
| **failure mode × MAS stage** | MAST (2503.13657) | a labeled stage of the pipeline (pre/exec/post), tied to an agent role | ◐ stage-level, maps to a node |
| **step** (typed action+obs) | CausalFlow (2605.25338) | REASONING / TOOL_CALL / … step; **single-agent**, no agent bridge | ◐ partial — closest to the "step-only" bucket you down-weighted |

**Takeaway:** there is no real "node vs block vs chunk" schism to navigate. **Adopt Who&When's (agent, decisive-step) unit.** The only term to treat warily is bare **"step"** in single-agent papers (CausalFlow), which is the reasoning-step granularity you wanted excluded — useful as a *technique* exemplar, not a node mapping. **"span"** (TRAIL) is just the observability name for a node-level call.

---

## 3. The 2025→2026 causal / counterfactual SOTA (Q1 + Q3)

Grouped by technique. ⚠️ flags are confidence caveats (full list §8).

### Counterfactual *simulation* (in-context, single pass)
**A2P — Abduct, Act, Predict** · arXiv **2509.10401** (Sep 2025). Operationalizes Pearl's SCM inside **one LLM forward pass**: **Abduct** the latent root cause → **Act** by defining a minimal `do()`-intervention → **Predict** by simulating the next 3–5 turns counterfactually to test whether that one correction averts failure (separating *decisive* from *incidental* errors). Enhancement of All-at-Once (keeps full context). Output: earliest (agent, step) + root-cause explanation.
- Reported step-level SOTA: **47.46% (algo-gen) / 29.31% (hand-crafted)**; agent 65.4% / 58.6%. Ablation: removing the `Step {idx}` numbering collapses step-acc 47.46→17.78 (−29.68pp) — structural identifiers are load-bearing.
- ⚠️ "causal" here is *prompt-scaffolded simulation, no real re-execution*; PDF is **~95% AI-authored ("DeepScientist")** — distrust the numbers, the method logic is sound. (2 of its verified claims drew 2-1 dissents on exactly these grounds.)

### Counterfactual *replay* (real re-execution) + trained tracer
**AgenTracer** · arXiv **2509.03312** (Sep 2025). The most engineering-credible of the wave. Builds training data (**TracerTraj**, 2000+ trajectory-error pairs) on **real** frameworks (MetaGPT, AutoGen, OpenHands, MaAS) two ways: **counterfactual replay** (replace action `a_t` with an oracle-corrected `a'_t`, re-simulate downstream) and **programmed fault injection** (corrupt a step in a *successful* run; that point is *by definition* the decisive error). Trains an **8B tracer** via multi-granular RL (GRPO) with agent-level + Gaussian-kernel step-proximity rewards.
- **Beats Gemini-2.5-Pro & Claude-4-Sonnet by ≤18.18%** on Who&When (off-the-shelf SOTA reasoners sit <10% step-level).
- **Closes the loop:** feedback (agent + step + short reason) yields **4.8–14.2% gains** repairing live MetaGPT/MaAS/OWL — and **beats Self-Refine and CRITIC, which can *degrade* performance.** That contrast is direct empirical support for targeted-over-blanket fixing.

### Game-theoretic blame + causal-graph correction
**Shapley + Performance Causal Inversion (PCI)** · arXiv **2509.08682** (Sep 2025; AAAI 2026). Two levels: **SBSLocator** assigns *agent* blame via **Shapley values** (marginal contribution across agent coalitions, Monte-Carlo) × counterfactual outcome; **CPIdentifier / CDC-MAS** does step-level causal discovery (do-calculus). Signature idea — **PCI: reverse the data-dependency edges** from the log to get the *performance*-causal graph, because info flows downstream but failure-causation flows from the upstream agent that fed bad data. Ablating Shapley drops step-acc 11.7pp.
- Reported: agent 48.5%/56.8%, **step 36.2% (algo) / 18.2% (HC)**; on **TRAIL** 44.6% GAIA / 14.3% SWE-Bench.
- **Closed-loop repair: 15.4% → 37.8% task success** (+22.4pp) vs 18.3% random-repair control. ⚠️ GPT-4o does diagnosis *and* repair *and* eval (circularity), N=50, no CIs. Its illustrative fix — *"WebSurfer failed at step 3 because its API instruction was ambiguous" → refine "search for Oscar-winning films" to "…2023 Oscar-winning films"* — is the cleanest worked example of node-localization → targeted prompt edit in the corpus.

### Hierarchical causal graph
**CHIEF — Causal HIErarchical Failure attribution** · arXiv **2602.23701** (Feb 2026). Argues flat logs blur "responsibility boundaries"; converts trajectories (via OTAR — Observation-Thought-Action-Result parsing) into a **hierarchical causal graph**, then (1) Hierarchical Oracle-Guided Backtracking top-down, (2) **Counterfactual Attribution** via progressive causal screening that separates true root causes from propagated symptoms (filters by causal scope, dependency nature, deviation-aware reversibility). Unit = (agent, timestep).
- Reported on Who&When-w/GT: **77.59% agent / 29.31% step (hand-crafted)**, 76.80% / 52.00% (algo-gen); beats All-at-Once (50.00%), Binary Search (51.70%), ECHO (68.40%) at agent-level. ⚠️ single-source self-report — see `06` §F for the `x/58` coincidence with A2P and the project-relevant reproduction note.

### Intervention-as-evaluation (the paradigm shift)
**DoVer — Do-then-Verify** · arXiv **2512.06749** (Microsoft + CAS; Dec 2025, v3 Feb 2026). See §6 — its central argument reframes the whole problem.

### Pure step-level counterfactual (single-agent — boundary case)
**CausalFlow** · arXiv **2605.25338** (2026). Computes per-step **Causal Responsibility Scores** via counterfactual intervention (replace a step, re-execute, did the verifier flip?), then emits **minimally-edited repairs** that flip failure→success (validated (wrong-step, corrected-step) contrastive pairs, scored by minimality). Excellent exemplar of the counterfactual-replay + minimal-repair pattern — but explicitly scoped to **single-agent sequential traces**; it does **not** bridge step-CRS up to multi-agent node blame, so it's the boundary of relevance.

---

## 4. Comparison table

| Method | Year | Unit / "node" | Algorithm class | Inputs | Reported accuracy *(self-reported unless noted)* | Actionability | Status |
|---|---|---|---|---|---|---|---|
| **Who&When** | May'25 | agent + decisive step | All-at-Once / Step-by-Step / Binary Search | failure log (msgs) | 53.5% agent / 14.2% step (best baseline) | defines unit; no repair | ✅ peer-reviewed (ICML Spotlight) |
| **A2P** | Sep'25 | (agent, step) + root cause | Pearl SCM `do()`, in-context sim, 1 pass | full trace | 47.46/29.31% step; 65.4/58.6 agent | High — agent+step+root-cause "for targeted improvements"; +25% tokens; 1 CLI flag | ⚠️ **~95% AI-authored** |
| **AgenTracer** | Sep'25 | agent + decisive (i\*,t\*) | **real counterfactual replay** + fault injection; trained 8B | real MAS traces | ~69% agent / 20.68% step (HC+GT); +18.18% vs Gemini-2.5-Pro | High — feedback → 4.8–14.2% live gains; **beats Self-Refine/CRITIC** | preprint, self-reported |
| **Shapley + PCI** | Sep'25 | agent-step-context triple | **Shapley** + Performance-Causal-Inversion + causal discovery | exec logs | 48.5/56.8 agent; 36.2/18.2 step; TRAIL 44.6 GAIA | High — closed loop **15.4→37.8%** | preprint, self-reported |
| **CHIEF** | Feb'26 | (agent, timestep), hierarchical | hierarchical causal graph + counterfactual screening | trajectory → causal graph | **77.59% agent / 29.31% step** (HC, w/GT) | localizes root vs symptom node | ⚠️ self-reported; see `06`§F |
| **DoVer** | Dec'25 | agent/step hypothesis, intervention-validated | hypothesize→edit→re-run→differential eval | trace + **live system** (in-situ replay) | rejects accuracy metric → outcome-based *(repair-rate claim KILLED in verify)* | **Highest fidelity** — the targeted edit *is* the test | preprint; verify-killed numbers |
| **AgentDebug** | Sep'25 | **module** (mem/reflect/plan/action/sys), 17 error types | root-module attribution + corrective feedback | annotated trajectories (AgentErrorBench) | +24% all-correct, +17% step; up to +26% task success | **High** — module+error-type → pick the fix | ⚪ fetched, not top-25 verified |
| **AgentRx** (MS) | 2026 | **Critical Failure Step** + 9-cat taxonomy | guarded constraints from tool schemas, step-by-step + LLM judge | failed trajectory + schemas + policy | +23.6% localization / +22.9% root-cause (115-traj) | High localization; **no auto-repair** (diagnose only) | ⚪ fetched, not top-25 |
| **MAST** | Mar'25 | failure mode × MAS stage (14 modes / 3 cats) | empirical taxonomy + o1 LLM-judge annotator | exec traces (MAST-Data 1600+) | annotator κ=0.77 vs human (κ=0.88) | category vocabulary; case fixes +9.4%, up to +15.6% | ⚪ fetched, not top-25 |
| **TRAIL** | May'25 | OpenTelemetry **span** | trace error-localization benchmark (3-cat taxonomy) | 148 traces / 1987 spans / 841 errors | best (Gemini-2.5-Pro) ~11% joint | benchmark, not a method | ⚪ fetched, not top-25 |
| **GraphTracer** | Oct'25 | **graph node** (Info-Dependency Graph) | graph-guided tracing; root vs symptom node | IDG over agent outputs | claimed +18.18% | — | ⚠️ **WITHDRAWN** (v2 2025-12-22) |
| **C3** | 2026 | per-decision (MARL) | fixed-context counterfactual replay + leave-one-out | RL episodes | beats MAPPO/MAGRPO | adjacent — MARL *training* credit, not post-hoc diagnosis | ⚪ fetched (adjacent) |
| **AgentSHAP** | '25/26 | tool/component | Monte-Carlo Shapley ablation over tool subsets | agent runs | — | per-component importance | ⚪ budget-dropped (search only) |
| **CausalFlow** | 2026 | step (**single-agent**) | step-level counterfactual CRS + minimal repair | single-agent trace | minimality/causal-consensus (no single acc #) | minimal corrective edit per step | ✅ verified; low node-relevance |
| **MS Taxonomy of Failure Modes** | Apr'25 | failure *types* (safety/security × novel/existing) | none (vocabulary) | n/a | n/a | complementary fix-categorization; **no localizer** | ✅ verified |

---

## 5. Actionability → your four fix types (Q4)

You want attribution that ends in *"node X is faulty, here's the category"* so you can choose a conservative fix. The mapping, by how directly each method hands you that:

- **A2P** gives the cleanest consumable output — **(agent, step, latent root-cause category)** — which routes the fix:
  - root cause = *missing information* → **add a tool call**
  - root cause = *misinterpretation / instruction-following* → **revise that node's prompt**
  - root cause = *unvalidated output propagated downstream* → **add a QC / self-check loop**
  - root cause = *needs judgment the model can't supply* → **insert a HITL gate**
- **AgentDebug** is arguably *better fit for your taxonomy*: its **module + error-type** label (e.g., "Reflection / constraint-ignorance", "Action / parameter-error") maps almost 1:1 to "add a self-check on Reflection", "fix the Action schema", "add the missing tool call". (Fetched but unverified here — verify its numbers directly.)
- **AgenTracer** is the strongest *evidence* that targeted node feedback beats blanket self-correction (its Self-Refine/CRITIC-degrade contrast).
- **Shapley/PCI** is the one to reach for when **blame is genuinely shared** across agents — Shapley resists "blame the last agent that touched it."
- **DoVer / CausalFlow** are the closest to *truly minimal in-situ edits* (edit one message/plan/step, re-run).
- **MAST / AgentRx / MS Taxonomy** supply the **error-category vocabulary** (14 modes / 9 categories / safety-security axes) that names *what kind* of fix — pair them with any localizer above.

---

## 6. The finding that should reshape your approach — attribution is ill-posed

**DoVer's central claim** (*confidence high, 3-0*): **single-step / single-agent attribution is ill-posed** — because **multiple distinct interventions can independently repair the same failure**, and ground-truth blame labels are uncertain (Who&When labels are "uncertain" on ~half of hand-crafted cases). So DoVer **stops optimizing attribution accuracy** and instead measures **whether an intervention resolves the failure / makes quantifiable progress**.

Mechanically (4 stages): (1) segment the trace into trials, (2) **hypothesize** a faulty agent/step, (3) generate a **testable targeted edit** (modified sub-agent instructions, or a plan update), (4) **execute it in-situ** with partial replay (all pre-intervention steps preserved) and **differentially evaluate** vs the original.

**Why this matters for your task specifically:** your goal is conservative node optimization where *false-negatives are OK but false-positive worsening is not.* DoVer is the formalization of that exact risk posture — **don't trust the blame signal; apply the candidate fix behind a verification gate and keep it only if the outcome delta is positive.** Your "include a QC loop / HITL gate" instinct and DoVer's "Do-then-Verify" are the same idea: **never commit a node edit you haven't counterfactually re-run.** It also gives independent support to AgenTracer's "blanket self-correction can degrade" result: across 26 WW-GAIA cases, Self-Refine and CRITIC flipped **0%** of failures while in-situ targeted intervention recovered 17.6%.

⚠️ **Integrity note:** DoVer's *specific* repair-rate claim was **KILLED 0-3** in verification — but not because the paper lacks the numbers. The paper *does* report 18% (AssistantBench) / 28% (GAIA) / 49% (GSMPlus); the *extracted claim* was killed for bundling them with an internally contradictory "~27.5% on GSMPlus" parenthetical (Table 2 says 49.0%). **Cite DoVer's method freely; verify its rates against the paper directly, not via that claim.**

---

## 7. A free, transferable lever worth banking now

Discovered independently by **both A2P and DoVer**: prefixing the trace with **explicit step indices** (`Step {idx} - Agent_Name:`) plus a **one-line reminder of the annotation guidance** moves GPT-4o step-attribution from **6% → 24%** on Who&When-HandCrafted — zero model changes. A2P's ablation corroborates: removing that numbering alone costs it 29.68pp of step-accuracy. **Before building anything elaborate, format your traces this way** — it's the cheapest accuracy available, and it directly de-risks every downstream attribution call. (DoVer also shows label-quality dominates: "certain" cases hit 44% GPT-4o / 53% GPT-5 vs 24% / 7% on "uncertain" ones.)

---

## 8. Confidence & caveats

- **Peer-review:** only **Who&When (2505.00212)** is peer-reviewed. A2P, AgenTracer, Shapley/PCI, CHIEF, CausalFlow, DoVer, AgentDebug, AgentFixer are **2025–2026 arXiv preprints**; numbers are **self-reported, single-source, not independently replicated**.
- **A2P (2509.10401):** *"Discovered and authored by DeepScientist"* footer (~95% AI-authored) — specifically undercuts its empirical figures, not its method description.
- **GraphTracer (2510.10581): WITHDRAWN** by authors (v2, 2025-12-22) for a methodology error. Do not cite its numbers.
- **DoVer repair rates:** the bundled claim was verify-killed (internal contradiction); the underlying 18/28/49% are in the paper but should be read directly.
- **Two senses of "causal":** A2P *simulates* counterfactuals in one pass (no re-execution); **AgenTracer / DoVer / CHIEF / CausalFlow** do **real** replay/intervention. Only the latter are genuinely interventional.
- **"Conservative node-level fix" is largely the research framing, not the papers':** AgenTracer's and Shapley/PCI's "repairs" are reflective-feedback loops or full solution-regeneration + re-execution, not verified minimal edits. **DoVer** is the closest to a single-location minimal edit.
- **Self-evaluation circularity:** Shapley/PCI's closed-loop repair uses GPT-4o for diagnosis, repair, and success-judging on N=50 with no CIs.
- **Recency vs cutoff:** CHIEF (2602.23701), CausalFlow (2605.25338), AgentRx, AgentFixer (2603.29848) postdate the assistant's Jan-2026 cutoff; surfaced via live search, not cross-checkable against later literature.

---

## 9. Gaps & open questions

- **No independent head-to-head.** A2P vs AgenTracer vs Shapley/PCI vs CHIEF are all self-reported on differing subsets/settings — the real ranking is unestablished. Extract the *principles*, don't bet on one preprint's leaderboard.
- **Accuracy-vs-outcome schism (unresolved).** DoVer says attribution accuracy is the wrong metric; the others optimize exactly that. The field hasn't reconciled accuracy-based leaderboards with outcome-based (failure-resolved) evaluation.
- **Minimality / blast-radius unmeasured.** No method is evaluated on how *minimal* its fix is vs full regeneration — yet minimality is the core of your "conservative" requirement.
- **Named-but-under-surfaced here:** Agent-as-a-Judge (never fetched), TRAIL / MAST / AgentDebug / AgentRx (fetched, not verified), plus ECHO, RAFFLES (2509.06822), Aegis (2509.14295), FALAT — cited inside the verified papers but not independently checked in this batch.

---

## 10. Synthesis — what this means for conservative node-level optimization

A clear assembly rather than a single tool:

1. **Adopt the unit** — target the **(agent, decisive-error-step)** pair: the *earliest* node whose fix flips the outcome. This is the built-in FP-guard against "fixing" downstream symptoms.
2. **Localize with A2P-style output** (agent + step + **root-cause category**) — and consider **AgentDebug's module+error-type** scheme, which maps most directly to your four fix types.
3. **Gate every fix with DoVer-style Do-then-Verify** — apply the minimal edit in-situ, replay, keep only on positive outcome delta. This is your explicit defense against FP-worsening edits; AgenTracer's "blanket self-correction degrades, targeted feedback gains" result is the empirical backing.
4. **Use Shapley** only when blame is genuinely shared across agents — otherwise it's overhead.
5. **Bank the free win first** — step-index + annotator-guidance formatting (6%→24%) before any model-side effort.

The mindset shift to carry forward: **attribution is a hypothesis, not a verdict.** The newest, most credible work (DoVer, AgenTracer) treats the located node as something to *test a fix against*, not to trust — precisely the conservative, false-positive-averse stance your node-level optimization demands.
