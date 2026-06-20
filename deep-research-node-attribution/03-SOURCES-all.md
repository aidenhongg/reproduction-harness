# 03 — All Sources Reviewed (Search Phase)

Every result returned by the five parallel search agents, grouped by angle, with the disposition the pipeline applied. **30 results → 18 unique sources fetched · 11 cross-angle duplicates · 1 budget-dropped.** "Why surfaced" is the searcher agent's own relevance snippet (verbatim).

## Angle 1 — practitioner/actionable — targeted node repair of existing workflows

### [high] AgentRx: Systematic Debugging for AI Agents (Microsoft Research, 2026)
- URL: https://www.microsoft.com/en-us/research/blog/systematic-debugging-for-ai-agents-introducing-the-agentrx-framework/
- Disposition: **FETCHED** -> 5 claims extracted
- Why surfaced (searcher snippet): The single most actionable node-localization framework. AgentRx pinpoints the FIRST unrecoverable 'critical failure' STEP, attaches one of a grounded NINE-category failure taxonomy (Plan Adherence Failure, Invalid Invocation, Misinterpretation of Tool Output, Intent-Plan Misalignment, Guardrails Triggered, System Failure, etc.), and emits an auditable, evidence-backed violation log. Algorithm = synthesize guarded executable constraints from tool schemas + domain policies, evaluate step-by-step, then an LLM judge labels the critical step. +23.6% failure localization and +22.9% root-cause attribution over prompting baselines on a 115-trajectory benchmark across tau-bench, Flash, and Magentic-One. Open-sourced framework + benchmark. Directly supports the 'node X is faulty, here is the error category -> apply targeted fix (add a guardrail/QC check, fix tool invocation, correct intent-plan)' loop the question targets.

### [high] CausalFlow: Causal Attribution and Counterfactual Repair for LLM Agent Failures (2026)
- URL: https://arxiv.org/abs/2605.25338
- Disposition: **FETCHED** -> 5 claims extracted
- Why surfaced (searcher snippet): The clearest attribution-PLUS-repair method. Models a trace as a chain of dependent steps, computes per-step Causal Responsibility Scores via counterfactual intervention to localize the failure-inducing step, then generates MINIMALLY-edited repairs that flip the final outcome to success, yielding validated contrastive (wrong step, corrected step) pairs usable for both test-time recovery and offline training supervision. Evaluated over math reasoning, code, QA, and medical browsing; reports high minimality and causal-consensus, outperforming heuristic refinement with more localized repairs. Exactly matches core question 3 (causal/counterfactual blame) and 4 (actionable targeted node edit) -- the conservative 'fix only the responsible node' philosophy.

### [high] DoVer: Intervention-Driven Auto Debugging for LLM Multi-Agent Systems (Microsoft, Dec 2025)
- URL: https://arxiv.org/abs/2512.06749
- Disposition: **FETCHED** -> 5 claims extracted
- Why surfaced (searcher snippet): Argues log-only single-step/single-agent attribution is ill-posed (multiple distinct interventions can independently repair the same failure) and instead VALIDATES hypotheses via targeted interventions -- editing a specific agent's message or altering its plan, then replaying. This is counterfactual node-repair as a verification mechanism. On Magentic-One over GAIA/AssistantBench it flips 18-28% of failed trials to success and validates/refutes 30-60% of failure hypotheses; recovers 49% on GSMPlus/AG2. Best evidence that a localized hypothesis ('agent X's plan/message is the fault') can be confirmed by a conservative targeted edit before committing the fix -- core question 3 + 4.

### [high] AgentFixer: From Failure Detection to Fix Recommendations in LLM Agentic Systems (IBM Research, ICSE 2026)
- URL: https://arxiv.org/html/2603.29848
- Disposition: **FETCHED** -> 5 claims extracted
- Why surfaced (searcher snippet): The most explicitly 'fix-recommendation' oriented work. A validation framework with 15 failure-detection tools + 2 root-cause-analysis modules covering input handling, prompt design, and output generation; combines lightweight rule-based checks with LLM-as-a-judge for incident detection, classification, AND repair. Surfaces recurrent classes (planner misalignments, schema violations, brittle prompt dependencies) and feeds diagnostics back into an LLM for self-reflection/prioritization to produce actionable recurring-pattern insights; refined prompting/coding let mid-sized models (Llama 4, Mistral Medium) gain accuracy while holding benchmarks. Directly serves core question 4 -- emit a node-level diagnosis plus the conservative prompt/code fix to apply to an existing workflow.

### [high] AgentDebug: Where LLM Agents Fail and How They Can Learn From Failures (2025)
- URL: https://arxiv.org/abs/2509.25370
- Disposition: **FETCHED** -> 5 claims extracted
- Why surfaced (searcher snippet): Decomposes a trajectory into decision steps and attributes at three granularities -- Step, Step+Module, and Step+Module+Error-Type -- over a 5-MODULE / 17-error-type taxonomy (Memory, Reflection, Planning, Action, System; e.g. retrieval failure, constraint ignorance, invalid action, parameter error). Isolates the minimal root-cause set and feeds CORRECTIVE FEEDBACK back to the responsible state/action for self-correction. Reports +24% all-correct accuracy, +17% step accuracy, and up to +26% downstream task-success improvement. The module+error-type label is exactly the actionable signal needed to choose a targeted node fix (add self-check on Reflection, fix Action format, add missing tool call). Bridges step-level signals up to module/agent blame.

### [high] AgenTracer: Who Is Inducing Failure in the LLM Agentic Systems? (2025)
- URL: https://arxiv.org/abs/2509.03312
- Disposition: **FETCHED** -> 5 claims extracted
- Why surfaced (searcher snippet): Builds annotated failure data via COUNTERFACTUAL REPLAY (replace an agent's action with oracle guidance to find the decisive error step) plus programmatic fault injection, yielding TracerTraj. Trains AgenTracer-8B (Qwen3-8B + RL with a multi-granular reward emphasizing BOTH step-level and AGENT-level attribution), beating Gemini-2.5-Pro and Claude-4-Sonnet by up to 18.18% on the Who&When benchmark. A lightweight, deployable attributor that outputs the responsible agent+step -- the localization half of a node-repair loop -- and the counterfactual-replay annotation method itself is the causal mechanism for assigning node blame (core questions 1 + 3).

## Angle 2 — academic/technical — causal & counterfactual blame assignment

### [high] CausalFlow: Causal Attribution and Counterfactual Repair for LLM Agent Failures (2026)
- URL: https://arxiv.org/abs/2605.25338
- Disposition: DUPLICATE - already fetched via an earlier angle (deduped to one fetch)
- Why surfaced (searcher snippet): The single best match for the causal/counterfactual angle. An interventional framework that computes Causal Responsibility Scores (CRS) via step-level counterfactual intervention to pinpoint the failure-inducing step, then converts the failed trace into a MINIMAL counterfactual repair: a validated (wrong step, corrected step) contrastive pair with a high minimality score (the smallest edit that flips failure->success). Inputs are structured execution traces (chains of dependent steps). Evaluated on 4 benchmarks (math reasoning, code gen, QA, medical browsing), beating heuristic refinement with 'more localized repairs.' Directly actionable: it not only localizes the culprit node but emits the conservative corrective edit for that node. Attribution unit is step-level (maps to a workflow node when a node = a trace step).

### [high] AgenTracer: Who Is Inducing Failure in the LLM Agentic Systems? (2025)
- URL: https://openreview.net/pdf/4ad6b1217a99a5f8e7a76d23157ebf94d0e328d6.pdf
- Disposition: **FETCHED** -> 5 claims extracted
- Why surfaced (searcher snippet): Core counterfactual-replay method. Builds annotated failure trajectories by (a) COUNTERFACTUAL REPLAY — systematically replacing an agent's action with an oracle/correct alternative and re-simulating to see if the outcome flips — and (b) programmatic FAULT INJECTION into successful traces to create synthetic failures with known root causes. Produces the TracerTraj dataset (2000+ trajectory-error pairs) and trains a lightweight attributor (AgenTracer-8B, Qwen3-8B + GRPO with a multi-granular reward: format + agent-level correctness + step-level proximity). Attributes at BOTH agent-level and step-level. On Who&When it reaches ~20.7% step-level accuracy on the handcrafted subset vs ~12% for Claude-4-Sonnet (~1.71x). Actionable: its feedback yields 4.8-14.2% real gains in MetaGPT/MaAS. The clearest demonstration that counterfactual replay beats LLM-judge classification.

### [high] DoVer: Intervention-Driven Auto Debugging for LLM Multi-Agent Systems (2025/26)
- URL: https://arxiv.org/pdf/2512.06749
- Disposition: **FETCHED** -> 5 claims extracted
- Why surfaced (searcher snippet): The most actionable intervention-based pipeline for systems with explicit orchestrator/sub-agent topology. Two-phase hypothesis-then-validate design: first generates LOG-BASED attribution hypotheses, then VALIDATES each via targeted INTERVENTION — using framework-level checkpoint + replay infrastructure to roll back to a suspected step, modify it, and re-run to confirm causality. Segments traces into planning-execution trials. Requires orchestrator/sub-agent structure + checkpoint/replay infra as inputs (heavier than judge-only methods but more rigorous). Reports gains on GAIA, AssistantBench, AutoGen tasks. Attributes to steps/decision points within the orchestration flow and supports targeted fixes at the confirmed faulty point — strong fit for conservative node-level repair of an existing workflow.

### [high] Abduct, Act, Predict (A2P): Scaffolding Causal Inference for Automated Failure Attribution in Multi-Agent Systems (2025)
- URL: https://arxiv.org/pdf/2509.10401
- Disposition: **FETCHED** -> 5 claims extracted
- Why surfaced (searcher snippet): Reframes failure attribution from LLM pattern-recognition into a structured 3-stage CAUSAL INFERENCE task over the execution trace: Abduct (hypothesize which step caused failure) -> Act (intervene by modifying the suspected step's output) -> Predict (forecast whether that intervention resolves the failure). This Pearl-style abduction-intervention-prediction scaffold is exactly the counterfactual reasoning class requested, applied at step-level within a multi-agent trace and validated against Who&When-style benchmarks. Actionable because the 'Act' stage's chosen intervention is itself the candidate fix. (arXiv 2509 = Sept 2025, not 2024.)

### [medium] C3: Contextual Counterfactual Credit Assignment for Multi-Agent RL in LLM Collaboration (2026)
- URL: https://github.com/EIT-EAST-Lab/C3
- Disposition: **FETCHED** -> 4 claims extracted
- Why surfaced (searcher snippet): Closest rigorous CREDIT/BLAME-assignment method using fixed-context counterfactual replay. C3 converts diffused episode-level rewards into PER-DECISION causal advantages by: anchoring transcript-derived context, substituting context-matched alternative actions, and executing FIXED-CONTINUATION REPLAYS plus a leave-one-out (ablation) baseline. This isolates how much each agent decision causally contributed, solving the problem that consequential upstream steps and trivial downstream tokens otherwise get equal credit. Oriented toward MARL training (beats MAPPO/MAGRPO) rather than post-hoc node diagnosis, so it is adjacent rather than a drop-in attributor — but its fixed-context replay + leave-one-out machinery is directly transferable to node-level blame in an existing workflow.

### [medium] AgentSHAP: Interpreting LLM Agent Tool Importance with Monte Carlo Shapley Value Estimation (2025/26); cf. SHARP & Shapley-Coop
- URL: https://arxiv.org/abs/2512.12597
- Disposition: BUDGET-DROPPED - not fetched (15-source budget filled; relevance `medium` below cutoff)
- Why surfaced (searcher snippet): Represents the ablation/coalitional family for node-level attribution. AgentSHAP uses Monte Carlo Shapley estimation to measure each TOOL/component's marginal contribution by testing the agent under different tool subsets (i.e., ablation over coalitions) — a principled, faithful per-component importance score where duplicated or sabotaging components get near-zero credit. Companion works extend Shapley credit to per-agent blame: SHARP (Shapley Credit-based Optimization, arXiv 2602.08335) and Shapley-Coop (arXiv 2506.07388) decompose a global outcome signal into per-agent rewards/blame. Shapley/leave-one-out is the canonical causal alternative to LLM-judge attribution; Monte Carlo (a few hundred samples) makes the NP-hard computation tractable. Maps cleanly to tool-call-step and agent-node granularity.

## Angle 3 — recent frontier sweep — newest 2025 work beyond named anchors

### [high] Which Agent Causes Task Failures and When? On Automated Failure Attribution of LLM Multi-Agent Systems (Who&When, ICML 2025 Spotlight)
- URL: https://arxiv.org/abs/2505.00212
- Disposition: **FETCHED** -> 5 claims extracted
- Why surfaced (searcher snippet): THE foundational anchor for the original question. Defines the new task of 'automated failure attribution' and releases the Who&When benchmark: failure logs from 127 LLM multi-agent systems, hand-annotated with the failure-responsible AGENT and the decisive error STEP. Attribution UNIT = agent + step (workflow-component level, not pure CoT). Proposes and compares three algorithms directly relevant to the taxonomy axis the user wants: all-at-once, step-by-step, and binary-search. Reported ceiling is low (best ~53.5% agent-level, ~14.2% step-level), establishing the difficulty and the baseline every later 2025 paper is measured against.

### [high] AgenTracer: Who Is Inducing Failure in the LLM Agentic Systems?
- URL: https://arxiv.org/abs/2509.03312
- Disposition: DUPLICATE - already fetched via an earlier angle (deduped to one fetch)
- Why surfaced (searcher snippet): Newest-frontier (Sep 2025) SOTA that the user's named anchors don't cover. First automated framework to annotate failed multi-agent trajectories via COUNTERFACTUAL REPLAY + programmed fault injection (dataset TracerTraj), directly addressing the causal/counterfactual node-blame angle (core question 3). Ships AgenTracer-8B, a lightweight tracer trained with multi-granular RL that beats giant reasoning LLMs (which sit below ~10% on this task) and, crucially for actionability (core question 4), feeds corrective signals back into off-the-shelf systems like MetaGPT and MaAS for 4.8-14.2% task gains and +18.18% attribution improvement. Attributes to responsible AGENT and STEP.

### [high] Where LLM Agents Fail and How They Can Learn From Failures (AgentDebug / AgentErrorTaxonomy)
- URL: https://arxiv.org/abs/2509.25370
- Disposition: DUPLICATE - already fetched via an earlier angle (deduped to one fetch)
- Why surfaced (searcher snippet): Most ACTIONABLE node-level result (core question 4). Derives the AgentErrorTaxonomy by attributing failures to ROOT MODULES — memory, reflection, planning, action — across hundreds of trajectories, exactly the workflow-node granularity the user wants (module/block, not token). AgentDebug isolates the root-cause failure, names its error category, and emits corrective feedback that the existing agent re-runs with, reporting +24% all-correct accuracy over the strongest baseline. This is the 'node X is faulty, here is the category, here is the fix' loop the user is targeting for conservative edits to an existing workflow.

### [high] Why Do Multi-Agent LLM Systems Fail? (MAST failure taxonomy, Cemri et al. 2025)
- URL: https://arxiv.org/abs/2503.13657
- Disposition: **FETCHED** -> 5 claims extracted
- Why surfaced (searcher snippet): The canonical failure-mode TAXONOMY anchor (core question 1 + the error-category vocabulary for question 4). Empirically grounded over 7 MAS frameworks / 200+ tasks / 6 annotators (κ=0.88), yielding 14 failure modes in 3 families: system-design issues, inter-agent misalignment, and task-verification failures — many of which map to a specific faulty NODE/agent and suggest the targeted fix (e.g., add a verification/QC node, fix routing, add a missing check). MAST-Data (1600+ annotated traces) and an LLM-as-judge auto-annotator make it the labeling backbone reused by downstream attribution papers.

### [high] TRAIL: Trace Reasoning and Agentic Issue Localization (Patronus AI)
- URL: https://arxiv.org/abs/2505.08638
- Disposition: **FETCHED** -> 5 claims extracted
- Why surfaced (searcher snippet): Directly named anchor and a key error-LOCALIZATION benchmark. 148 human-annotated single- and multi-agent execution traces with 841 errors under a formal taxonomy spanning Reasoning, Planning & Coordination, and System Execution (incl. resource exhaustion, infinite loops, context-handling failures). Localizes errors to positions within the trace and labels the category, bridging step-level signals to component-level blame. Shows even long-context frontier models are weak at trace debugging (best Gemini-2.5-pro ~11%), reinforcing why specialized node-attribution methods are needed.

### [medium] Automatic Failure Attribution and Critical Step Prediction for Multi-Agent Systems Based on Causal Inference
- URL: https://arxiv.org/abs/2509.08682
- Disposition: **FETCHED** -> 5 claims extracted
- Why surfaced (searcher snippet): Covers the causal/counterfactual technique axis (core question 3) with concrete machinery: a 'performance causal inversion' principle that reverses data flow in execution logs, Shapley values for agent-level blame assignment, and a causal-discovery algorithm (CDC-MAS) for the critical failure STEP under non-stationary interaction data. Reports up to ~36% step-level accuracy on Who&When (vs <15% prior) and pairs with counterfactual-repair follow-ups (CausalFlow) that turn the located node into a minimal fix for ~22% task-success gains — strong support for conservative, node-targeted optimization of an existing workflow.

## Angle 4 — broad/primary — canonical named methods & benchmarks

### [high] Which Agent Causes Task Failures and When? On Automated Failure Attribution of LLM Multi-Agent Systems (Who&When, ICML 2025 Spotlight)
- URL: https://arxiv.org/abs/2505.00212
- Disposition: DUPLICATE - already fetched via an earlier angle (deduped to one fetch)
- Why surfaced (searcher snippet): THE canonical primary paper that defines the task. Introduces the Who&When benchmark: failure logs from 127 LLM multi-agent systems, each annotated with (i) the failure-responsible AGENT and (ii) the decisive error STEP (earliest mistake whose correction flips fail->success). Evaluates the three reference ALGORITHMS the user asked about: All-at-Once (whole log, one pass), Step-by-Step (sequential), and Binary Search (split-in-half). Best method = 53.5% agent-level accuracy but only 14.2% step-level; some below random; even o1/R1 underperform. Attribution unit = agent + step. Inputs = full execution trace/log. Code at github.com/ag2ai/Agents_Failure_Attribution. This is the central anchor for the comparison table.

### [high] Why Do Multi-Agent LLM Systems Fail? (MAST taxonomy, NeurIPS 2025 D&B)
- URL: https://arxiv.org/abs/2503.13657
- Disposition: DUPLICATE - already fetched via an earlier angle (deduped to one fetch)
- Why surfaced (searcher snippet): Source of the MAST (Multi-Agent System Failure Taxonomy): 14 failure modes in 3 categories -- (1) system/specification design, (2) inter-agent misalignment, (3) task verification. Built from 150 hand-annotated traces (inter-annotator kappa=0.88), with MAST-Data of 1600+ annotated traces across 7 MAS frameworks (e.g., AutoGen, ChatDev, MetaGPT). Ships an LLM-as-a-Judge annotator with high human agreement. Provides the ERROR-CATEGORY vocabulary that pairs with node localization to produce 'node X failed because of category Y' -> directly supports choosing the conservative fix (add verification node, fix orchestration, add tool-call). Attribution unit = trace/step labeled by failure mode.

### [high] TRAIL: Trace Reasoning and Agentic Issue Localization
- URL: https://arxiv.org/abs/2505.08638
- Disposition: DUPLICATE - already fetched via an earlier angle (deduped to one fetch)
- Why surfaced (searcher snippet): Patronus AI benchmark for error LOCALIZATION in agent traces. 148 annotated traces (118 GAIA + 30 SWE-Bench) = 1,987 OpenTelemetry SPANS, 841 errors under a formal taxonomy of Reasoning, Planning, and Execution failures. Attribution unit = the SPAN (an OpenTelemetry span = a discrete tool-call / LLM-call step in the workflow graph -- a 'node'-level unit, NOT pure CoT tokens). Task = given a long trace, output which span has which error category. Shows SOTA long-context models fail: best (Gemini-2.5-Pro) only ~11%. Key for the terminology map: 'span' = node/step-level instrumentation unit.

### [high] AgenTracer: Who Is Inducing Failure in the LLM Agentic Systems?
- URL: https://arxiv.org/abs/2509.03312
- Disposition: DUPLICATE - already fetched via an earlier angle (deduped to one fetch)
- Why surfaced (searcher snippet): Newest (Sept 2025) SOTA on Who&When and a strong CAUSAL/counterfactual method. Auto-annotates failed multi-agent trajectories via (a) COUNTERFACTUAL REPLAY -- systematically replace an agent's action with oracle guidance to find the decisive error step -- and (b) programmatic fault injection (corrupt successful traces) to synthesize training data (TracerTraj). Trains AgenTracer-8B, a lightweight failure tracer via multi-granular RL; beats Gemini-2.5-Pro and Claude-4-Sonnet by up to +18.18% on Who&When. Attribution unit = responsible agent + decisive step. Directly demonstrates counterfactual node-blame and feeds actionable self-correction signals.

### [high] Automatic Failure Attribution and Critical Step Prediction for Multi-Agent Systems Based on Causal Inference
- URL: https://arxiv.org/html/2509.08682
- Disposition: **FETCHED** -> 5 claims extracted
- Why surfaced (searcher snippet): Explicitly CAUSAL approach addressing the correlation-based limits of All-at-Once/Step-by-Step/Binary-Search. Integrates Shapley values (game-theoretic fair responsibility allocation) + causal inference ('performance causal inversion') to predict the CRITICAL STEP. Reports up to 36.2% step-level accuracy (vs 14.2% baseline) AND -- most relevant for actionability -- the generated optimizations boost overall task success by an average of +22.4%, i.e., it closes the loop from attribution to a targeted node fix. Attribution unit = agent + critical step on the Who&When-style trace.

### [medium] Microsoft: Taxonomy of Failure Modes in Agentic AI Systems (whitepaper + June 2026 update)
- URL: https://www.microsoft.com/en-us/security/blog/2025/04/24/new-whitepaper-outlines-the-taxonomy-of-failure-modes-in-ai-agents/
- Disposition: **FETCHED** -> 5 claims extracted
- Why surfaced (searcher snippet): Microsoft AI Red Team taxonomy mapping agentic failures on two axes: novel vs existing, and safety vs security. Novel categories include agent injection, agent impersonation, agent flow/communication manipulation, and multi-agent jailbreaks -- i.e., failures localized to the inter-agent communication flow (node/edge-level in the workflow graph). June 4 2026 update adds 7 modes (tool abuse, excessive agency, goal misalignment, feedback-loop poisoning, etc.). Useful for the failure-CATEGORY half of node-level attribution and for security-flavored fixes (insert a guard/HITL gate), though it is a taxonomy not an automated localizer.

## Angle 5 — terminology disambiguation — node vs span vs module vs step granularity

### [high] Which Agent Causes Task Failures and When? On Automated Failure Attribution of LLM Multi-Agent Systems (Who&When, ICML 2025 Spotlight)
- URL: https://arxiv.org/abs/2505.00212
- Disposition: DUPLICATE - already fetched via an earlier angle (deduped to one fetch)
- Why surfaced (searcher snippet): The foundational terminology-defining paper for core question 2. It cleanly separates TWO distinct attribution units and reports them independently: the failure-responsible AGENT (workflow-node-level: 'Who', 53.5% best accuracy) vs. the decisive error STEP (step-level: 'When', only 14.2%). Defines 'decisive error step' = the earliest mistake whose correction flips failure->success, and explicitly maps step-level signals up to agent-level blame. The 127-trace Who&When benchmark with three algorithms (all-at-once, step-by-step, binary-search) is the canonical reference for the node-vs-step granularity split.

### [high] TRAIL: Trace Reasoning and Agentic Issue Localization
- URL: https://arxiv.org/abs/2505.08638
- Disposition: DUPLICATE - already fetched via an earlier angle (deduped to one fetch)
- Why surfaced (searcher snippet): Cleanest source for the 'span' vs 'step' vs 'agent' terminology distinction. TRAIL adopts OpenTelemetry/OpenInference SPANS as the atomic attribution unit (1,987 spans across 148 traces; 575 erroneous) and requires localizing each error to a precise SPAN ID plus an error category from a 3-class taxonomy (Reasoning, Planning/Coordination, System Execution). Spans nest hierarchically so a tool-call/LLM-call span maps to a workflow-graph node — directly bridges observability-trace granularity to actionable node+category output. Covers BOTH single- and multi-agent traces (GAIA, SWE-Bench); best model only 11% joint accuracy.

### [high] GraphTracer: Graph-Guided Failure Tracing in LLM Agents for Robust Multi-Turn Deep Search
- URL: https://arxiv.org/abs/2510.10581
- Disposition: **FETCHED** -> 4 claims extracted
- Why surfaced (searcher snippet): Explicitly redefines the attribution UNIT as a graph NODE rather than a temporal step. Builds Information Dependency Graphs (IDGs) capturing how agents reference prior outputs, then distinguishes source-level accuracy (identifying root-cause NODES) from path-level accuracy (failure-propagation paths) — the sharpest contrast in the literature between graph-node-level attribution and pure step/temporal-order granularity. Uses counterfactual analysis on the IDG to separate symptom nodes from root-cause nodes; GraphTracer-8B is +18.18% over SOTA. Directly supports node-level blame with causal/counterfactual technique.

### [high] AgenTracer: Who Is Inducing Failure in the LLM Agentic Systems?
- URL: https://arxiv.org/abs/2509.03312
- Disposition: DUPLICATE - already fetched via an earlier angle (deduped to one fetch)
- Why surfaced (searcher snippet): The canonical step->node BRIDGING method: AgenTracer-8B is trained with MULTI-GRANULAR reinforcement learning that JOINTLY optimizes step-level and agent-level (node-level) attribution accuracy, explicitly tying step signals to agent blame. Defines the decisive error step as the earliest agent-step PAIR whose correction flips the outcome (counterfactual replay + programmatic fault injection on the TracerTraj dataset). Shows annotating at agent-step granularity yields up to +14.2% in self-correcting systems — relevant to actionable, node-targeted fixes.

### [high] Why Do Multi-Agent LLM Systems Fail? (MAST / MASFT taxonomy)
- URL: https://arxiv.org/abs/2503.13657
- Disposition: DUPLICATE - already fetched via an earlier angle (deduped to one fetch)
- Why surfaced (searcher snippet): Defines the failure-MODE unit and how a mode 'spans' inter-agent conversation STAGES rather than a single node. 14 failure modes in 3 categories (specification/design 41.8%, inter-agent misalignment 36.9%, verification 21.3%) over 1600+ annotated traces (kappa=0.88). Key for terminology disambiguation: the attribution unit is a labeled span/stage of the MAS pipeline mapped to agent-role and conversation phase, distinct from a single graph node or token-step — and provides the error-category vocabulary needed for actionable, per-node fix recommendations (add verification, fix specification, etc.).

### [medium] From Flat Logs to Causal Graphs: Hierarchical Failure Attribution for LLM-based Multi-Agent Systems (CHIEF)
- URL: https://arxiv.org/html/2602.23701
- Disposition: **FETCHED** -> 5 claims extracted
- Why surfaced (searcher snippet): Most explicit treatment of the granularity-bridging problem: argues flat-sequence logs blur 'responsibility boundaries,' then transforms trajectories into a HIERARCHICAL causal graph with oracle-guided backtracking and progressive counterfactual screening to separate true root causes from propagated symptoms. Reports BOTH agent-level (node) and step-level accuracy on Who&When, beating eight baselines — a direct example of mapping fine step signals up to coarse node/agent blame via an explicit hierarchy, the core of the step-vs-node bridging question.

