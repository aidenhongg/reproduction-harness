# 04 — All 88 Extracted Claims (Fetch Phase)

Every falsifiable claim the 18 fetch agents extracted, grouped by source, with supporting quote, the agent's importance rating, and verification disposition. The 25 highest-ranked claims (importance, then source quality) went to adversarial verification (**24 confirmed, 1 killed**); the other 63 were not individually verified (below the top-25 budget) — absence of verification is NOT refutation.

## Automatic Failure Attribution and Critical Step Prediction for Multi-Agent Systems Based on Causal Inference
- URL: https://arxiv.org/html/2509.08682
- Found via: broad/primary — canonical named methods & benchmarks  ·  Quality: primary  ·  Published: 2025-09-10  ·  Claims: 5

### [central] — VERIFIED -> CONFIRMED (vote 3-0)
> The paper introduces a failure attribution framework for multi-agent systems (MAS) with two modules — SBSLocator (agent-level 'Who') and CPIdentifier (step-level 'When') — grounded in multi-granularity causal inference, attributing failures to agent-step-context triples rather than pure reasoning steps.

Quote: "our approach encompasses innovations at two levels. At the agent level, we design the SBSLocator module...At the step level, the CPIdentifier module"

### [central] — VERIFIED -> CONFIRMED (vote 3-0)
> The core causal mechanism is the Performance Causal Inversion (PCI) principle: data-dependency edges observed in execution logs must be reversed in direction to build a correct performance causal graph, because information flow runs opposite to performance causation.

Quote: "we propose the Performance Causal Inversion (PCI) principle: to construct an accurate performance causal graph, the data dependencies observed from the execution logs must be reversed in direction."

### [central] — VERIFIED -> CONFIRMED (vote 3-0)
> Agent-level blame ('Who') is assigned using Shapley values from cooperative game theory, computing each agent's marginal contribution across coalition combinations to measure its true system importance — a causal/credit-assignment approach to node attribution.

Quote: "we introduce Shapley values from game theory, treating each agent as a participant in a 'cooperative game' and evaluating its true system importance by calculating its marginal contributions in different coalition combinations."

### [central] — VERIFIED -> CONFIRMED (vote 3-0)
> On the Who&When benchmark the framework reaches agent-level accuracy of 48.5% / 56.8% (Algorithmic / Hand-Crafted subsets) and step-level accuracy of 18.2% on the Hand-Crafted subset, a >159% relative improvement over the best baseline (7.02% from Step-by-Step); on TRAIL it reaches 44.6% step-level on GAIA and 14.3% on SWE-Bench.

Quote: "our Step-Level Accuracy reaches 18.2%, achieving a relative improvement of over 159% compared to the best baseline (7.02% from Step-by-Step)"

### [central] — VERIFIED -> CONFIRMED (vote 3-0)
> The method produces actionable localization: a precise root-cause report feeds an optimization agent whose causal-guided repairs raised end-to-end task success from 15.4% to 37.8% (+22.4 points), exceeding a random-repair control (18.3%), demonstrating closed-loop conservative node-level fixing of an existing workflow.

Quote: "Repairs guided by our causal analysis boosted the overall task success rate from 15.4% to 37.8%—an absolute improvement of 22.4 percentage points."

## DoVer: Intervention-Driven Auto Debugging for LLM Multi-Agent Systems (Microsoft, Dec 2025)
- URL: https://arxiv.org/abs/2512.06749
- Found via: practitioner/actionable — targeted node repair of existing workflows  ·  Quality: primary  ·  Published: 2025-12-07  ·  Claims: 5

### [central] — NOT SELECTED for verification (ranked below top-25 budget)
> DoVer is an intervention-driven debugging framework that augments LLM-generated failure hypotheses with active verification via targeted counterfactual interventions (editing messages, altering plans) on the interaction trace, rather than relying on log-only attribution. This is a counterfactual/intervention attribution technique applied to multi-agent traces.

Quote: "we introduce DoVer, an intervention-driven debugging framework, which augments hypothesis generation with active verification through targeted interventions (e.g., editing messages, altering plans)."

### [central] — NOT SELECTED for verification (ranked below top-25 budget)
> DoVer's attribution unit is a specific agent AND step within a multi-agent system, explicitly bridging step-level signals up to agent-level blame (the relevant node/agent granularity). It frames the prevailing log-based localization paradigm as attributing errors to a specific agent and step.

Quote: "The prevailing practice is to leverage LLMs for log-based failure localization, attributing errors to a specific agent and step."

### [central] — NOT SELECTED for verification (ranked below top-25 budget)
> DoVer critiques single-node attribution as ill-posed: it finds that multiple distinct interventions can independently repair the same failed task, undermining the assumption of a single faulty agent/step. This is a direct methodological challenge to all-at-once/step-by-step single-node attribution approaches (e.g., Who&When).

Quote: "single-step or single-agent attribution is often ill-posed, as we find that multiple distinct interventions can independently repair the failed task."

### [central] — NOT SELECTED for verification (ranked below top-25 budget)
> DoVer is evaluated by outcome (does the intervention resolve the failure or make quantifiable progress) rather than attribution accuracy; on GAIA/AssistantBench within Magnetic-One it flips 18-28% of failed trials into successes, achieves up to 16% milestone progress, and validates or refutes 30-60% of failure hypotheses; on GSMPlus with AG2 it recovers 49% of failed trials.

Quote: "DoVer flips 18-28% of failed trials into successes, achieves up to 16% milestone progress, and validates or refutes 30-60% of failure hypotheses. DoVer also performs effectively on a different dataset (GSMPlus) and agent framework (AG2), where it recovers 49% of failed trials."

### [supporting] — NOT SELECTED for verification (ranked below top-25 budget)
> DoVer produces actionable, applied localization: it does not merely label a faulty node but actively intervenes on the existing workflow trace (e.g., editing the offending message or altering the plan) and verifies whether that repair resolves the failure, demonstrating intervention as a practical reliability-improvement mechanism for existing agentic systems.

Quote: "These results highlight intervention as a practical mechanism for improving reliability in agentic systems and open opportunities for more robust, scalable debugging methods for LLM-based multi-agent systems."

## CausalFlow: Causal Attribution and Counterfactual Repair for LLM Agent Failures (2026)
- URL: https://arxiv.org/abs/2605.25338
- Found via: academic/technical — causal & counterfactual blame assignment  ·  Quality: primary  ·  Published: 2026-05-25  ·  Claims: 5

### [central] — VERIFIED -> CONFIRMED (vote 3-0)
> CausalFlow performs failure attribution by computing 'Causal Responsibility Scores' (CRS) via step-level counterfactual intervention to identify the specific failure-inducing step within a trace. This is a causal/counterfactual (interventional) attribution algorithm, directly relevant to counterfactual blame-assignment approaches.

Quote: "CausalFlow models execution traces as sequential chains of dependent steps and computes Causal Responsibility Scores(CRS) via step-level counterfactual intervention to identify failure-inducing steps."

### [central] — VERIFIED -> CONFIRMED (vote 3-0)
> The attribution UNIT is the individual STEP within an execution trace (modeled as sequential chains of dependent steps), not a discrete workflow-graph node/agent/module/tool-call component. The method does not explicitly map step-level blame up to agent/node-level blocks, making it primarily step-granularity rather than node-granularity.

Quote: "CausalFlow models execution traces as sequential chains of dependent steps"

### [supporting] — NOT SELECTED for verification (ranked below top-25 budget)
> CausalFlow produces ACTIONABLE, localized repairs: for each identified faulty step it generates a minimally-edited correction that flips the final outcome to success, yielding contrastive (wrong step, corrected step) pairs usable for targeted test-time repair with minimal behavioral drift.

Quote: "For these steps, we generate minimally edited repairs that flip the final outcome to success, producing validated contrastive pairs of the form (wrong step, corrected step)."

### [supporting] — NOT SELECTED for verification (ranked below top-25 budget)
> CausalFlow was evaluated across four benchmarks (mathematical reasoning, code generation, question answering, and medical browsing) and reports that causal attribution outperforms heuristic refinement in complex retrieval settings while producing more localized repairs, measured by minimality and causal-consensus scores rather than a single attribution-accuracy number.

Quote: "Across four benchmarks spanning mathematical reasoning, code generation, question answering, and medical browsing, CausalFlow converts failed executions into validated minimal repairs with high minimality and causal-consensus scores, and demonstrates that causal attribution is necessary for reliable improvement across diverse agent tasks, outperforming heuristic refinement in complex retrieval settings while producing more localized repairs throughout."

### [tangential] — NOT SELECTED for verification (ranked below top-25 budget)
> The input to CausalFlow is failed agent execution traces (logs of multi-step tasks involving reasoning, tool use, and environment interaction), which the method treats as containing structured signals about where execution broke down.

Quote: "While such failures are typically logged or retried heuristically, they contain structured signals about where execution broke down."

## AgenTracer: Who Is Inducing Failure in the LLM Agentic Systems? (2025)
- URL: https://openreview.net/pdf/4ad6b1217a99a5f8e7a76d23157ebf94d0e328d6.pdf
- Found via: academic/technical — causal & counterfactual blame assignment  ·  Quality: primary  ·  Published: 2025-09-03  ·  Claims: 5

### [central] — VERIFIED -> CONFIRMED (vote 3-0)
> AgenTracer attributes failure at the agent/step level: it localizes both the responsible AGENT and the 'decisive error' step within a multi-agent execution trace, defined as the earliest agent-step pair (i*, t*) whose correction flips the system from failure to success. This is true node/agent-level blame attribution (not pure CoT step-scoring), directly relevant to localizing failures to a workflow node.

Quote: "Pinpointing the specific agent or step responsible for an error within long execution traces defines the task of agentic system failure attribution. ... the earliest agent-step pair whose correction is sufficient to steer the system from failure to success — formally (i*, t*) = arg min_{(i,t)∈C(τ)} t"

### [central] — VERIFIED -> CONFIRMED (vote 2-1)
> AgenTracer's core attribution algorithm is causal/counterfactual: an analyzer agent performs counterfactual replay (intervening at a trajectory step by replacing the original action a_t with a minimally-invasive corrected action a'_t and re-simulating subsequent steps) plus programmed fault injection (corrupting a step in a successful trajectory to induce failure, labeling that point as the decisive error). This is explicitly a counterfactual/intervention approach rather than all-at-once or plain step-by-step prompting.

Quote: "we propose AgenTracer, the first automated framework for annotating failed multi-agent trajectories via counterfactual replay and programmed fault injection, producing the curated dataset TracerTraj"

### [central] — VERIFIED -> CONFIRMED (vote 3-0)
> Off-the-shelf state-of-the-art reasoning LLMs are strikingly poor at multi-agent failure attribution, with accuracy generally below 10%; AgenTracer-8B (a lightweight model trained with multi-granular reinforcement learning, GRPO) outperforms giant proprietary models Gemini-2.5-Pro and Claude-4-Sonnet by up to 18.18% on the Who&When benchmark (reported at roughly 69.10% agent-level / 20.68% step-level accuracy).

Quote: "Current state-of-the-art reasoning LLMs, however, remain strikingly inadequate for this challenge, with accuracy generally below 10%. ... On the Who&When benchmark, AgenTracer-8B outperforms giant proprietary LLMs like Gemini-2.5-Pro and Claude-4-Sonnet by up to 18.18%, setting a new standard in LLM agentic failure attribution."

### [central] — VERIFIED -> CONFIRMED (vote 3-0)
> AgenTracer produces ACTIONABLE localization that enables conservative repair of existing multi-agent systems: by delivering targeted feedback identifying the faulty agent/step, it yields 4.8-14.2% performance gains on off-the-shelf systems MetaGPT and MaAS (e.g., +14.21% on MaAS+MATH-500), supporting self-correcting/self-evolving workflows.

Quote: "AgenTracer-8B delivers actionable feedback to off-the-shelf multi-agent systems like MetaGPT and MaAS with 4.8-14.2% performance gains, empowering self-correcting and self-evolving agentic AI."

### [supporting] — NOT SELECTED for verification (ranked below top-25 budget)
> AgenTracer is trained on TracerTraj, an automatically-annotated dataset of over 2,000 high-fidelity trajectory-error pairs spanning six multi-agent systems across coding, math, and general agentic-task domains, using multi-granular RL with both agent-level accuracy rewards and a Gaussian-kernel step-level reward that incentivizes temporal proximity to the true decisive error step.

Quote: "Over 2,000 high-fidelity annotated trajectory-error pairs ... Gaussian kernel reward that incentivizes temporal proximity to the true decisive error step"

## AgenTracer: Who Is Inducing Failure in the LLM Agentic Systems?
- URL: https://arxiv.org/abs/2509.03312
- Found via: terminology disambiguation — node vs span vs module vs step granularity  ·  Quality: primary  ·  Published: 2025-09-03  ·  Claims: 5

### [central] — NOT SELECTED for verification (ranked below top-25 budget)
> AgenTracer performs failure attribution at the agent-OR-step level, framing the task as pinpointing the specific agent or step responsible for an error within long execution traces of multi-agent systems (i.e., it bridges step-level signals up to agent/node-level blame).

Quote: "Pinpointing the specific agent or step responsible for an error within long execution traces defines the task of agentic system failure attribution."

### [supporting] — NOT SELECTED for verification (ranked below top-25 budget)
> Current state-of-the-art reasoning LLMs are inadequate at agentic failure attribution, achieving accuracy generally below 10% on this task.

Quote: "Current state-of-the-art reasoning LLMs, however, remain strikingly inadequate for this challenge, with accuracy generally below 10%."

### [central] — NOT SELECTED for verification (ranked below top-25 budget)
> AgenTracer uses a counterfactual/causal attribution technique: it annotates failed multi-agent trajectories via counterfactual replay and programmed fault injection, producing a curated dataset called TracerTraj, and trains an 8B failure tracer (AgenTracer-8B) with multi-granular reinforcement learning.

Quote: "we propose AgenTracer, the first automated framework for annotating failed multi-agent trajectories via counterfactual replay and programmed fault injection, producing the curated dataset TracerTraj. Leveraging this resource, we develop AgenTracer-8B, a lightweight failure tracer trained with multi-granular reinforcement learning, capable of efficiently diagnosing errors in verbose multi-agent interactions."

### [central] — NOT SELECTED for verification (ranked below top-25 budget)
> On the Who&When benchmark, the lightweight AgenTracer-8B outperforms large proprietary LLMs (Gemini-2.5-Pro and Claude-4-Sonnet) by up to 18.18% on failure attribution.

Quote: "On the Who&When benchmark, AgenTracer-8B outperforms giant proprietary LLMs like Gemini-2.5-Pro and Claude-4-Sonnet by up to 18.18%, setting a new standard in LLM agentic failure attribution."

### [central] — NOT SELECTED for verification (ranked below top-25 budget)
> AgenTracer-8B produces actionable localization feedback that, when applied to off-the-shelf multi-agent systems (MetaGPT and MaAS), yields 4.8-14.2% downstream performance gains, supporting conservative correction of an existing workflow.

Quote: "AgenTracer-8B delivers actionable feedback to off-the-shelf multi-agent systems like MetaGPT and MaAS with 4.8-14.2% performance gains, empowering self-correcting and self-evolving agentic AI."

## Automatic Failure Attribution and Critical Step Prediction for Multi-Agent Systems Based on Causal Inference
- URL: https://arxiv.org/abs/2509.08682
- Found via: recent frontier sweep — newest 2025 work beyond named anchors  ·  Quality: primary  ·  Published: 2025-09-10  ·  Claims: 5

### [central] — NOT SELECTED for verification (ranked below top-25 budget)
> This paper (arXiv:2509.08682, Ma et al., Zhejiang Normal University + HKUST, submitted 10 Sep 2025, AAAI 2026) introduces a causal-inference failure-attribution framework for multi-agent systems that operates at TWO node-level granularities: agent-level ('Who', via the SBSLocator module) and step-level ('When', via the CPIdentifier module), where a node is formally a (agent, step, context) triple in a performance causal graph. This directly answers the research question's demand for node/agent-level attribution rather than pure reasoning-step scoring.

Quote: "we introduce the first failure attribution framework for MAS grounded in multi-granularity causal inference... the node set V = {vi = (ai, si, ci)} represents agent-step-context triples... To address the "Who" problem, we designed the SBSLocator module... After identifying the bottleneck agent, the CPIdentifier module focuses on precisely locating the key error step in that agent's execution trajectory."

### [central] — NOT SELECTED for verification (ranked below top-25 budget)
> The core algorithm is causal/counterfactual, NOT all-at-once or step-by-step correlation. For agent blame it applies a 'Performance Causal Inversion' principle (reversing data-flow edges from execution logs so edges point cause-to-effect) plus Shapley values for marginal contribution, and a counterfactual 'Integrated Bottleneck Score' BSj = phi_j * (Y_cf - Y_original) that asks 'if this agent performed normally, would the task succeed?'. For critical-step localization it uses CDC-MAS, a four-stage causal discovery process (Feature Engineering -> Temporal Skeleton Discovery -> Confounding-Aware Edge Orientation -> Causal Effect/ACE Ranking) with do-calculus counterfactual step interventions.

Quote: "(1) a performance causal inversion principle, which correctly models performance dependencies by reversing the data flow in execution logs, combined with Shapley values to accurately assign agent-level blame; (2) a novel causal discovery algorithm, CDC-MAS, that robustly identifies critical failure steps... The algorithm's process can be summarized as: (I) Feature Engineering -> (II) Temporal Skeleton Discovery -> (III) Confounding-Aware Edge Orientation -> (IV) Causal Effect Ranking."

### [central] — NOT SELECTED for verification (ranked below top-25 budget)
> On the Who&When benchmark the method reaches step-level accuracy of 36.2% (Algo) / 18.2% (Hand-crafted) and agent-level 48.5% / 56.8%, outperforming the All-at-Once (12.50/5.26 step), Step-by-Step (25.51/7.02 step), and Binary Search (23.98/6.90 step) LLM-as-Judge baselines; on the Hand-crafted subset this is a >159% relative step-level improvement over the best baseline. On TRAIL it reaches 44.6% step-level on GAIA and 14.3% on SWE-Bench. These are concrete, comparable benchmark numbers for the comparison table.

Quote: "Method ... Step Acc. (%) (Algo/Hand) ... All-at-Once ... 12.50 / 5.26 ... Step-by-Step ... 25.51 / 7.02 ... Binary Search ... 23.98 / 6.90 ... Ours ... 36.2 / 18.2 ... Step-Level Accuracy reaches 18.2%, achieving a relative improvement of over 159% compared to the best baseline (7.02% from Step-by-Step) ... On the TRAIL benchmark, our framework achieves a step-level accuracy of 44.6% on GAIA and 14.3% on SWE Bench."

### [central] — NOT SELECTED for verification (ranked below top-25 budget)
> The framework produces ACTIONABLE node-level localization: it emits a precise root-cause report naming the faulty agent, the faulty step, and the error category, then feeds it into a closed-loop optimization that auto-generates a targeted fix to that node's prompt/instruction. Repairs guided by the causal diagnosis raised overall task success from 15.4% to 37.8% (a +22.4-point absolute gain), far above a random-repair control (18.3%) and validated via counterfactual simulation. This is exactly the 'identify node X + error category -> conservative targeted fix' capability the research question targets.

Quote: "our framework generated a precise root cause report, such as: "The WebSurfer agent failed at step 3 because its API call instruction was ambiguous." ... it would refine the vague instruction "search for Oscar-winning films" into the specific "search for 2023 Oscar-winning films," directly addressing the identified problem ... Repairs guided by our causal analysis boosted the overall task success rate from 15.4% to 37.8%--an absolute improvement of 22.4 percentage points."

### [supporting] — NOT SELECTED for verification (ranked below top-25 budget)
> The paper frames existing correlation-based attribution (including the named All-at-Once / Step-by-Step / Binary Search strategies used on Who&When and TRAIL) as fundamentally limited, reporting that state-of-the-art methods score below ~15-20% at root-cause-step localization (17.1% on Who&When, 18.3% joint on TRAIL), because they confuse downstream 'symptom' nodes (e.g. the last agent to error) with the upstream causal 'pathogen' node. This motivates the causal paradigm and quantifies the baseline landscape for the survey.

Quote: "on challenging benchmarks like Who&When, state-of-the-art methods achieve less than 15% accuracy in locating the root-cause step of a failure ... existing best methods achieve only 17.1% accuracy ... TRAIL benchmark... top-performing models achieve a joint accuracy as low as 18.3% ... baselines often misattribute the failure to downstream "symptoms"... whereas our method, through performance causal inversion, successfully traces the issue back to its upstream "pathogen.""

## Which Agent Causes Task Failures and When? On Automated Failure Attribution of LLM Multi-Agent Systems (Who&When, ICML 2025 Spotlight)
- URL: https://arxiv.org/abs/2505.00212
- Found via: terminology disambiguation — node vs span vs module vs step granularity  ·  Quality: primary  ·  Published: 2025-04-30  ·  Claims: 5

### [central] — VERIFIED -> CONFIRMED (vote 3-0)
> The Who&When work attributes failures to two units in a multi-agent trace: the 'failure-responsible agent' (which agent caused the failure) and the 'decisive error step' (the specific step where the failure occurred) — establishing agent-level/step-level (node-level) blame rather than pure token granularity.

Quote: "The dataset comprises "extensive failure logs from 127 LLM multi-agent systems with fine-grained annotations linking failures to specific agents and decisive error steps.""

### [central] — VERIFIED -> CONFIRMED (vote 3-0)
> The paper develops and evaluates three distinct automated failure-attribution algorithms — All-at-Once (whole-trace single query), Step-by-Step (incremental sequential analysis), and Binary Search (recursive divide-and-conquer over the trace) — which directly map to the algorithm taxonomy in the research question.

Quote: "All-at-Once: "The model is provided with the entire task execution trace and asked to identify which agent caused the failure and at which step." ... Binary Search: "The model divides the execution trace into halves, queries whether failure occurred in each segment, and recursively narrows down to identify the responsible step and agent.""

### [central] — VERIFIED -> CONFIRMED (vote 2-1)
> Automated failure attribution is currently far from solved: even SOTA reasoning models reach only about 53.5% accuracy at identifying the responsible agent and just 14.2% at identifying the decisive error step, with OpenAI o1 and DeepSeek R1 failing to reach practical usability.

Quote: "**Agent identification:** 53.5% accuracy ... **Error step identification:** 14.2% accuracy ... State-of-the-art models like OpenAI o1 and DeepSeek R1 "fail to achieve practical usability.""

### [supporting] — NOT SELECTED for verification (ranked below top-25 budget)
> The Who&When benchmark is a purpose-built dataset of failure logs from 127 LLM multi-agent systems with fine-grained annotations linking each failure to a specific agent and step, providing the inputs (execution traces/logs plus ground-truth labels) for evaluating attribution methods.

Quote: ""extensive failure logs from 127 LLM multi-agent systems with fine-grained annotations linking failures to specific agents and decisive error steps.""

### [supporting] — NOT SELECTED for verification (ranked below top-25 budget)
> No single attribution algorithm dominates both targets, and supplying ground-truth intermediate-step information improves performance — suggesting hybrid/causal signals help node-level localization.

Quote: "Step-by-Step performs best for identifying the failure-responsible agent, while Binary Search is most efficient for pinpointing the error step. Additionally, combining multiple methods improves accuracy, and ground-truth information about intermediate steps enhances performance across all approaches."

## From Flat Logs to Causal Graphs: Hierarchical Failure Attribution for LLM-based Multi-Agent Systems (CHIEF)
- URL: https://arxiv.org/html/2602.23701
- Found via: terminology disambiguation — node vs span vs module vs step granularity  ·  Quality: primary  ·  Published: 2026-02-27  ·  Claims: 5

### [central] — NOT SELECTED for verification (ranked below top-25 budget)
> CHIEF attributes failures to a node/agent granularity in a hierarchical graph, defining a Subtask Node as a high-level task-phase abstraction and an Agent Node as an atomic agent-instance execution unit, with the final attribution targeting an (agent, timestep) pair as the root cause — exactly the workflow-graph-NODE-level attribution unit the research question targets.

Quote: "Subtask Node Sk∈𝒦sub is defined as a high-level logical abstraction representing a distinct task phase. ... Agent Node a∈𝒦agt is defined as an atomic execution unit representing a specific agent instance within a subtask."

### [central] — NOT SELECTED for verification (ranked below top-25 budget)
> CHIEF is a three-stage causal-hierarchical attribution method (CHIEF = Causal HIErarchical Failure attribution): (1) build a Hierarchical Causal Graph from flat trajectories, (2) Hierarchical Oracle-Guided Backtracking to find error candidates top-down, (3) Counterfactual Attribution to separate root causes from propagated symptoms — i.e., a graph-based divide-and-conquer algorithm, not all-at-once or linear step-by-step scanning.

Quote: "To address these obstacles, we propose CHIEF, a Causal HIErarchical Failure attribution framework for LLM-based MASs. ... First, we construct a Hierarchical Causal Graph... Second, we propose Hierarchical Oracle-Guided Backtracking... Finally, we implement Counterfactual Attribution via a progressive causal screening strategy."

### [central] — NOT SELECTED for verification (ranked below top-25 budget)
> CHIEF uses an explicit counterfactual/causal screening step to distinguish a node that is the true root cause from a node that merely exhibits a propagated downstream symptom, filtering by causal scope, dependency nature, and a deviation-aware reversibility check — a counterfactual blame-assignment approach directly relevant to causal node attribution.

Quote: "Finally, it implements Counterfactual Attribution via a progressive causal screening strategy. By filtering responsibility through causal scope and dependency nature, and applying a deviation-aware check for reversibility, we rigorously distinguish true root causes from propagated symptoms."

### [central] — NOT SELECTED for verification (ranked below top-25 budget)
> On the Who&When benchmark with ground truth, CHIEF reports 77.59% agent-level accuracy (29.31% step-level) on the hand-crafted subset and 76.80% agent-level (52.00% step-level) on the algorithm-generated subset, outperforming all-at-once (50.00%), binary search (51.70%), and ECHO (68.40%) agent-level baselines.

Quote: "It delivers 77.59% agent-level and 29.31% step-level accuracy on the hand-crafted subset, alongside 76.80% and 52.00% on the algorithm-generated subset."

### [supporting] — NOT SELECTED for verification (ranked below top-25 budget)
> The paper's motivating thesis is that prior failure-attribution methods are limited because they treat multi-agent trajectories as flat linear sequences and ignore the data-dependency and agent-interaction structure, which CHIEF converts from flat logs into a structured causal graph via OTAR (Observation-Thought-Action-Result) parsing — establishing the node-vs-step granularity distinction central to the terminology deep-dive.

Quote: "More critically, current approaches are limited by treating MAS trajectories as flat sequences, ignoring the inherent structural complexity illustrated in Fig. 1. Unlike simple linear texts, these logs represent a dense intertwining of agents' observations, thoughts, actions, and results, interconnected by rigorous data dependencies and agent interactions."

## GraphTracer: Graph-Guided Failure Tracing in LLM Agents for Robust Multi-Turn Deep Search
- URL: https://arxiv.org/abs/2510.10581
- Found via: terminology disambiguation — node vs span vs module vs step granularity  ·  Quality: primary  ·  Published: 2025-10-12  ·  Claims: 4

### [central] — NOT SELECTED for verification (ranked below top-25 budget)
> GraphTracer attributes failures using Information Dependency Graphs (IDGs) that capture how agents reference and build on prior outputs, localizing root causes by tracing through dependency structures rather than temporal/sequential order — placing it at the agent/node level with graph-based propagation (NOT all-at-once or step-by-step temporal scan).

Quote: "constructs Information Dependency Graphs (IDGs) to explicitly capture how agents reference and build on prior outputs ... GraphTracer localizes root causes by tracing through these dependency structures instead of relying on temporal sequences"

### [central] — NOT SELECTED for verification (ranked below top-25 budget)
> GraphTracer-8B reports up to 18.18% higher attribution accuracy than state-of-the-art models on the Who&When automated-failure-attribution benchmark, and yields 4.8% to 14.2% downstream performance improvements when deployed in multi-agent frameworks (i.e., it claims actionable, fix-enabling localization of critical nodes).

Quote: "GraphTracer-8B achieves up to 18.18% higher attribution accuracy compared to state-of-the-art models ... 4.8% to 14.2% performance improvements in deployed multi-agent frameworks"

### [supporting] — NOT SELECTED for verification (ranked below top-25 budget)
> The method uses graph-aware synthetic data generation to target 'critical nodes' and create realistic failure scenarios for training the attribution model, indicating it explicitly models workflow-graph nodes (agents) as the attribution unit.

Quote: "graph-aware synthetic data generation to target critical nodes, creating realistic failure scenarios"

### [central] — NOT SELECTED for verification (ranked below top-25 budget)
> RELIABILITY CAVEAT: The paper was withdrawn by the authors (v2, December 22, 2025) due to a fundamental methodology error that invalidates the main results — so its reported accuracy numbers should NOT be cited as established state-of-the-art and any comparison table entry must be flagged as withdrawn/retracted.

Quote: "A newer version of this paper has been withdrawn by Heng Zhang"

## Why Do Multi-Agent LLM Systems Fail? (MAST / MASFT taxonomy)
- URL: https://arxiv.org/abs/2503.13657
- Found via: terminology disambiguation — node vs span vs module vs step granularity  ·  Quality: primary  ·  Published: 2025-03-17 (arXiv v1; v3 2025-10-26; NeurIPS 2025 Datasets & Benchmarks Track)  ·  Claims: 5

### [central] — NOT SELECTED for verification (ranked below top-25 budget)
> MAST is an empirically grounded taxonomy of 14 fine-grained failure modes clustered into 3 categories (System Design Issues 44.2%, Inter-Agent Misalignment 32.3%, Task Verification 23.5%), and the failure modes are mapped to MAS execution STAGES (Pre-Execution, Execution, Post-Execution) where their root causes commonly emerge — i.e., the attribution unit is the agent-interaction/conversation-stage level, not chain-of-thought reasoning steps.

Quote: "MAST, illustrated in Figure 1, identifies 14 fine-grained failure modes, which we map to MAS execution stages (Pre-Execution, Execution, and Post-Execution) where their root causes commonly emerge. These modes are organized into 3 overarching categories... (i) system design issues, (ii) inter-agent misalignment, and (iii) task verification."

### [central] — NOT SELECTED for verification (ranked below top-25 budget)
> MAST is operationalized as a scalable automated annotator (LLM-as-a-Judge using OpenAI's o1, given the execution trace + MAST definitions + few-shot examples in an all-at-once classification), achieving accuracy 0.94, recall 0.77, precision 0.833, F1 0.80, and Cohen's kappa 0.77 against human experts; human inter-annotator agreement was kappa=0.88 and out-of-domain generalization kappa=0.79.

Quote: "The LLM annotator achieves high agreement with human experts (accuracy 94%, Cohen's Kappa of 0.77; Table 2)... o1 (few shot) 0.94 0.77 0.833 0.80 0.77 ... validated by high inter-annotator agreement (kappa = 0.88)... yields a strong Cohen's Kappa score of 0.79."

### [central] — NOT SELECTED for verification (ranked below top-25 budget)
> MAST produces ACTIONABLE node-level localization: it pinpoints a specific faulty agent AND its error category (e.g., ChatDev's CPO agent exhibiting Failure Mode 1.2 Disobey Role Specification), and a targeted conservative fix to that node (refining role specifications / a workflow adjustment giving the CEO final say) yielded a +9.4% task-success increase with the same LLM, with intervention case studies reaching up to 15.6% max improvement via prompt and topology changes.

Quote: "the CPO agent in ChatDev can exhibit 'Failure Mode 1.2 - Disobey Role Specification' by terminating conversation without the CEO agent's consensus. We demonstrate that a straightforward system workflow adjustment ensuring the CEO had the final say contributed to a +9.4% increase in overall task success rate... we apply MAS system workflow and prompt changes respectively... we achieve max improvements of 15.6%."

### [supporting] — NOT SELECTED for verification (ranked below top-25 budget)
> MAST positions itself as the failure-TAXONOMY complement to per-agent/per-step failure-attribution work, explicitly contrasting with the Who&When dataset and MAS debugger (Zhang et al.), which attributes failures to particular agents and error steps — confirming the terminology distinction between agent/stage-level taxonomic blame (MAST) versus item-level agent+step attribution.

Quote: "current work by Zhang et al. [42] present the Who&When dataset and MAS debugger, which focuses on summarizing failures for specific task items by attributing them to particular agents and error steps. Thus, MAST-Data and MAST represent, to our knowledge, the first empirically derived, comprehensive dataset and taxonomy focused specifically on MAS failures."

### [supporting] — NOT SELECTED for verification (ranked below top-25 budget)
> MAST-Data is a corpus of 1642 annotated execution traces (each averaging over 15,000 lines) drawn from 7 named open-source MAS frameworks — ChatDev, MetaGPT, HyperAgent, AppWorld, AG2/AutoGen (MathChat), Magentic-One, and OpenManus/Manus — across coding, math, and general-agent tasks and four model families (GPT-4, Claude 3, Qwen2.5, CodeLlama), with observed failure rates of 41% to 86.7%.

Quote: "we introduce MAST-Data, a comprehensive, high-quality collection of 1642 annotated execution traces... analysis reveals 41% to 86.7% failure rate on 7 state-of-the-art (SOTA) open-source MAS... ChatDev... MetaGPT... HyperAgent... AppWorld... AG2 (MathChat)... Magentic-One... OpenManus."

## DoVer: Intervention-Driven Auto Debugging for LLM Multi-Agent Systems (2025/26)
- URL: https://arxiv.org/pdf/2512.06749
- Found via: academic/technical — causal & counterfactual blame assignment  ·  Quality: primary  ·  Published: 2026-01-31 (arXiv:2512.06749v3 [cs.AI]; v3 metadata dated Feb 3, 2026)  ·  Claims: 5

### [central] — VERIFIED -> CONFIRMED (vote 3-0)
> DoVer is an intervention-driven (Do-then-Verify) auto-debugging pipeline that, unlike pure log-based attribution, treats failure attribution as a hypothesis to be tested: for each 'trial' it hypothesizes a faulty step or agent, turns that into a targeted edit (modified orchestrator instructions to sub-agents, or plan updates), and re-executes/replays the trajectory in-situ to verify whether the hypothesis is supported or refuted. This makes its attribution unit the agent/step within a trial, validated causally via counterfactual replay.

Quote: "DoVer consists of four stages: (1) trial segmentation to break an execution log into trials, (2) hypothesis generation to hypothesize a failure step or agent, (3) intervention generation to synthesize a testable change, and (4) intervention execution with differential evaluation against the original run using task success and a progress score. ... we treat attribution as a hypothesis to be tested. We apply a targeted edit at the implicated location (message, plan, tool call), and rerun the system, judging success by milestone/utility gains."

### [central] — VERIFIED -> CONFIRMED (vote 3-0)
> DoVer argues that single-step or single-agent failure attribution is fundamentally ill-posed/ambiguous because multiple distinct interventions can independently repair the same failed task, and ground-truth attribution labels are uncertain; it therefore replaces attribution-accuracy evaluation with outcome-oriented metrics (whether the failure is resolved). Intervention execution yields an explicit counterfactual trace that preserves all pre-intervention steps.

Quote: "single-step or single-agent attribution is often ill-posed, as we find that multiple distinct interventions can independently repair the failed task ... All steps are preserved before the intervened step and execute the intervention Ii, yielding a counterfactual trace ~I = {1, 2, ..., i-1, ~i}. Note that one intervention creates one new trace, verifying the failure attribution of each trial."

### [central] — VERIFIED -> CONFIRMED (vote 3-0)
> In a reproduction of the Who&When (WW) log-based single-agent/single-step attribution benchmark, two minimal prompt refinements (adding explicit step indices to the failure log, and embedding a concise reminder of annotator guidance) raise GPT-4o step-attribution accuracy on the WW-HandCrafted category from 6% to 24% under the All-at-Once prompt; ground-truth uncertainty is a major limiter, with certain cases reaching 44% (GPT-4o) / 53% (GPT-5) versus 24% / 7% on uncertain cases.

Quote: "on the Hand Crafted category of WW (WW-HC) with the setting of including ground-truth answers and adopting the All-at-Once prompt (a single LLM call over the full session log), step attribution accuracy for GPT-4o increases from 6% to 24%. ... For the 14 uncertain cases, average step attribution accuracy is 24% for GPT-4o and 7% for GPT-5. In contrast, for the remaining certain 15 cases, average accuracy increases to 44% for GPT-4o and 53% for GPT-5."

### [central] — VERIFIED -> KILLED (vote 0-3)
> DoVer's intervention-based repair produces measurable outcome improvements: within the Magnetic-One agent framework it flips 18% (AssistantBench) and 28% (GAIA) of previously failed trials into successes and validates or refutes 30-60% of failure hypotheses, and it generalizes to a different framework (AG2) and dataset (GSMPlus) with a 49% trial flip rate (~27.5% reported on GSMPlus trials in Table 2).

Quote: "DoVer recovers 18% and 28% of failures on datasets from AssistantBench Yoran et al. (2024) and GAIA Mialon et al. (2024), respectively. It further enables the validation or refutation of 30-60% of failure hypotheses, depending on task complexity. ... DoVer again performs effectively, achieving a 49% flip rate on failure cases."

### [supporting] — NOT SELECTED for verification (ranked below top-25 budget)
> In-situ node-level intervention substantially outperforms end-of-trace self-improvement baselines: across all 26 failed WW-GAIA cases, Self-Refine-style and CRITIC-style baselines flip 0% of failures into success, whereas DoVer recovers 17.6% of trials, because targeted corrections at the failure point are needed rather than refining the final answer over a long noisy trajectory.

Quote: "Across all 26 failed WW-GAIA cases, neither baseline is able to flip any failure into success (0% recovery), whereas DoVer recovers 17.6% of trials (Table 2). ... In contrast, DoVer performs in-situ interventions at potential failure points, enabling timely and targeted corrections that are essential in multi-agent settings."

## Where LLM Agents Fail and How They Can Learn From Failures (AgentDebug / AgentErrorTaxonomy)
- URL: https://arxiv.org/abs/2509.25370
- Found via: recent frontier sweep — newest 2025 work beyond named anchors  ·  Quality: primary  ·  Published: 2025-09-29  ·  Claims: 5

### [central] — NOT SELECTED for verification (ranked below top-25 budget)
> AgentDebug attributes agent failures at the MODULE level via the AgentErrorTaxonomy, a modular classification spanning five components: memory, reflection, planning, action, and system-level operations. This is node/component-level (not pure CoT-step) attribution directly relevant to localizing blame to a discrete workflow component.

Quote: "we introduce the AgentErrorTaxonomy, a modular classification of failure modes spanning memory, reflection, planning, action, and system-level operations."

### [central] — NOT SELECTED for verification (ranked below top-25 budget)
> The paper introduces AgentErrorBench, claimed as the first dataset of systematically annotated failure trajectories drawn from three agent environments (ALFWorld, GAIA, WebShop), grounding error-attribution evaluation in real agent rollouts.

Quote: "we construct AgentErrorBench, the first dataset of systematically annotated failure trajectories from ALFWorld, GAIA, and WebShop, grounding error analysis in real-world agent rollouts."

### [central] — NOT SELECTED for verification (ranked below top-25 budget)
> On AgentErrorBench, AgentDebug reports 24% higher all-correct accuracy and 17% higher step accuracy than the strongest baseline — a concrete, checkable failure-attribution accuracy result.

Quote: "Experiments on AgentErrorBench show that AgentDebug achieves 24% higher all-correct accuracy and 17% higher step accuracy compared to the strongest baseline."

### [central] — NOT SELECTED for verification (ranked below top-25 budget)
> AgentDebug is an actionable-localization method: it isolates a root-cause failure and emits targeted corrective feedback that lets the agent recover and iteratively improve, yielding up to 26% relative improvement in task success across ALFWorld, GAIA, and WebShop — i.e., it supports conservative, targeted node-level fixes to an existing workflow rather than regenerating it.

Quote: "the targeted feedback generated by AgentDebug enables LLM agents to iteratively recover from failures, yielding up to 26% relative improvements in task success across ALFWorld, GAIA, and WebShop."

### [supporting] — NOT SELECTED for verification (ranked below top-25 budget)
> The method is framed around single root-cause attribution under cascading failure: a single root-cause error propagates through subsequent decisions, and AgentDebug isolates that root cause — consistent with first/earliest-critical-step localization mapped up to a module rather than all-at-once multi-error labeling.

Quote: "where a single root-cause error propagates through subsequent decisions, leading to task failure ... AgentDebug, a debugging framework that isolates root-cause failures and provides corrective feedback"

## AgentRx: Systematic Debugging for AI Agents (Microsoft Research, 2026)
- URL: https://www.microsoft.com/en-us/research/blog/systematic-debugging-for-ai-agents-introducing-the-agentrx-framework/
- Found via: practitioner/actionable — targeted node repair of existing workflows  ·  Quality: primary  ·  Published: 2026-03-12  ·  Claims: 5

### [central] — NOT SELECTED for verification (ranked below top-25 budget)
> AgentRx (Microsoft Research, paper 'AgentRx: Diagnosing AI Agent Failures from Execution Trajectories') localizes failures to the 'Critical Failure Step' — the first unrecoverable error step in an agent's execution trajectory — and assigns a root-cause category, making it a step/action-level failure-attribution method for compound LLM systems.

Quote: "identify the **Critical Failure Step** and **root-cause category** ... the first unrecoverable error"

### [central] — NOT SELECTED for verification (ranked below top-25 budget)
> AgentRx uses a step-by-step (not all-at-once) algorithm: it synthesizes 'guarded constraints' from tool schemas and domain policies and evaluates each constraint only when its guard condition applies along the trajectory, distinguishing it from holistic LLM-as-judge attribution.

Quote: "Given a failed trajectory, tool schemas, and domain policy, AgentRx synthesizes guarded constraints, evaluates them step-by-step ... checking each constraint only when its _guard condition_ applies."

### [central] — NOT SELECTED for verification (ranked below top-25 budget)
> On 115 manually annotated failed trajectories across three complex domains, AgentRx reports +23.6% absolute improvement in failure-localization accuracy and +22.9% in root-cause attribution over LLM-based prompting baselines.

Quote: "**+23.6% absolute improvement** in failure localization accuracy ... **+22.9% improvement** in root-cause attribution ... 115 manually annotated failed trajectories across three complex domains"

### [central] — NOT SELECTED for verification (ranked below top-25 budget)
> AgentRx applies to multi-agent systems (Magentic-One) as well as single-agent tool-use (tau-bench), and explicitly handles trajectories containing multiple errors by pinpointing the first unrecoverable one.

Quote: "In multi-agent systems like Magentic-One, trajectories often contain multiple errors."

### [supporting] — NOT SELECTED for verification (ranked below top-25 budget)
> AgentRx grounds its attribution in a fixed nine-category failure taxonomy (Plan Adherence Failure, Invention of New Information, Invalid Invocation, Misinterpretation of Tool Output, Intent-Plan Misalignment, Under-specified User Intent, Intent Not Supported, Guardrails Triggered, System Failure), giving an actionable error category per localized step — but the blog does not describe any automated repair/remediation step.

Quote: "a grounded nine-category failure taxonomy"

## Abduct, Act, Predict (A2P): Scaffolding Causal Inference for Automated Failure Attribution in Multi-Agent Systems (2025)
- URL: https://arxiv.org/pdf/2509.10401
- Found via: academic/technical — causal & counterfactual blame assignment  ·  Quality: primary  ·  Published: 2025-09-23 (arXiv:2509.10401v2, submitted 23 Sep 2025; v2 dated 24 Sep 2025)  ·  Claims: 5

### [central] — VERIFIED -> CONFIRMED (vote 3-0)
> A2P attributes failures to a specific (agent, step) pair in a multi-agent conversation log — i.e., it localizes blame to a discrete agent/component (agent-level accuracy) AND the exact decisive turn (step-level accuracy), directly bridging step-level signals up to agent/node-level blame. The 'step' unit is a conversation turn pre-prefixed with the agent name (Step {idx} - Agent_Name).

Quote: "This entire three-step process is executed for each potential error the model considers, and it ultimately outputs the earliest agent-step pair that satisfies this causal chain. ... the entire conversation log is pre-processed to prefix each turn with an explicit, formatted identifier like Step {idx} - Agent_Name:. ... Agent-Level Accuracy measures the percentage of correctly predicted failure-responsible agents ... Step-Level Accuracy quantifies the percentage of correctly identified decisive error steps"

### [central] — VERIFIED -> CONFIRMED (vote 3-0)
> A2P is a counterfactual/causal-intervention attribution algorithm operationalizing Pearl's structural causal model: it (1) Abducts hidden root causes behind an agent's action, (2) Acts by defining a minimal corrective intervention via the do()-operator, and (3) Predicts by simulating the counterfactual trajectory (3-5 turns) to verify whether correcting that single action would have averted the failure — distinguishing decisive errors from incidental ones. It runs within a single inference pass as an enhancement of the All-at-Once method (retaining full-context access).

Quote: "A2P guides an LLM through a formal, three-step counterfactual reasoning process within a single inference pass, operationalizing the logic of Pearl's structural causal model hierarchy ... (1) Abduct, inferring hidden factors ... (2) Act, defining a minimal, concrete corrective intervention; and (3) Predict, simulating the subsequent counterfactual trajectory to verify if the intervention would have resolved the overall task failure ... Our approach is implemented as an enhancement to the All-at-Once method, thereby retaining its key advantage of having access to the complete conversational context."

### [central] — VERIFIED -> CONFIRMED (vote 2-1)
> On the Who&When benchmark, A2P achieves state-of-the-art step-level accuracy of 47.46% on the Algorithm-Generated subset (vs 16.67% all_at_once, 27.78% step_by_step, 28.57% binary_search) and 29.31% on the Hand-Crafted subset (vs 12.07% all_at_once). Agent-level accuracy is 65.40% (Algorithm-Generated) and 58.62% (Hand-Crafted). These are 2.85x and 2.43x step-accuracy gains over the all_at_once baseline.

Quote: "On the Algorithm-Generated dataset, A2P achieves 47.46% step-level accuracy, a 2.85x improvement over the 16.67% of the baseline. On the more complex Hand-Crafted dataset, it achieves 29.31% step accuracy, a 2.43x improvement over the baseline's 12.07%. ... our enhanced A2P Scaffolding with step numbering achieves 47.46% step accuracy on the Algorithm-Generated dataset--significantly outperforming the next-best baseline (binary_search at 28.57%) ... The agent-level accuracy of 65.40% on Algorithm-Generated and 58.62% on Hand-Crafted"

### [central] — VERIFIED -> CONFIRMED (vote 2-1)
> A2P produces actionable, causally-grounded node-level localization (identifies the failure-responsible agent + decisive step + an explanation of the latent root cause, e.g. 'HotelAgent at Step 4' misinterpreted 'tomorrow'), explicitly intended to enable targeted fixes rather than broad system changes, at a modest ~25% processing-time/token overhead and backward-compatible via a single -causal_reasoning command-line flag.

Quote: "A2P Scaffolding addresses a fundamental bottleneck in multi-agent system development by providing accurate, automated identification of failure-responsible agents and decisive error steps with causally grounded explanations, enabling developers to perform targeted improvements rather than broad system modifications ... while its backward-compatible implementation and modest 25% processing overhead make it immediately deployable"

### [supporting] — NOT SELECTED for verification (ranked below top-25 budget)
> Ablations show the causal components and structural anchoring are each necessary: removing Abduction drops step accuracy by 6.35pp (Alg-Gen) / 8.62pp (Hand-Crafted); removing Prediction drops it 7.14pp / 12.07pp; and removing the explicit 'Step {idx} -' numbering causes a catastrophic 29.68pp collapse (47.46% to 17.78%) while leaving agent accuracy nearly unchanged — indicating step-level (node-level) localization depends critically on explicit per-turn structural identifiers.

Quote: "The removal of explicit step numbering--simply removing the "Step {idx} - " prefixes--causes a catastrophic 29.68 percentage point collapse in step-level accuracy (from 47.46% to 17.78%) while leaving agent accuracy relatively unchanged. ... The Abduction step ... contributes 6.35 percentage points on Algorithm-Generated and 8.62 percentage points on Hand-Crafted ... Its [Prediction] removal causes degradation of 7.14 percentage points on Algorithm-Generated and a substantial 12.07 percentage points on Hand-Crafted"

## AgentFixer: From Failure Detection to Fix Recommendations in LLM Agentic Systems (IBM Research, ICSE 2026)
- URL: https://arxiv.org/html/2603.29848
- Found via: practitioner/actionable — targeted node repair of existing workflows  ·  Quality: primary  ·  Published: 2026 (1st International Workshop on Agentic Engineering, ICSE 2026; workshop date April 14, 2026)  ·  Claims: 5

### [central] — NOT SELECTED for verification (ranked below top-25 budget)
> AgentFixer attributes failures at the granularity of individual LLM calls within agents (and aggregates up to the affected-agent level), making it node/agent-level attribution rather than pure CoT-step verification. Each diagnostic names the affected agents along with the root cause.

Quote: "For each log, each tool returned the root cause of a failure, if exists, along with a description of the affected agents, categorized failure severity (minor, moderate and critical) and remediation recommendation."

### [central] — NOT SELECTED for verification (ranked below top-25 budget)
> The method is a parallel/all-at-once validation suite of fifteen rule-based and LLM-based tools spanning three pipeline stages (Prompt analysis, Input validation, Output validation) that operate on the prompts, inputs, and outputs of each call — not a step-by-step or binary-search localizer.

Quote: "we built a comprehensive agentic-system validation suite of fifteen tools spanning three stages: Prompt analysis: tools for contradiction detection, coverage gaps, and edge-case handling in system prompts. Input validation: schema and format checks to ensure well-formed requests before execution. Output validation: tools for factual consistency, syntactic correctness of code, anomaly detection, and reasoning–action alignment."

### [central] — NOT SELECTED for verification (ranked below top-25 budget)
> AgentFixer adds a counterfactual-style trace-comparison tool that contrasts successful vs. failed trajectories to localize the failure; in their evaluation it improved failure-location and root-cause explanations for 12 of 26 pairs (46.2%).

Quote: "We observed that for 12 pairs (46.2%), the comparison method provided better failure-location and root-cause explanations."

### [central] — NOT SELECTED for verification (ranked below top-25 budget)
> AgentFixer produces actionable, targeted node-level fix recommendations (e.g., identifying an incorrect input field name and the corrected value), enabling a conservative repair to the existing workflow; validation-informed refinement lifted pass@3 by roughly 3-7 points across three models (GPT-4o 47%->50%, LLaMA 4 38%->42%, Mistral 35%->42%).

Quote: "root cause tool has detected the following issue...incorrect field names in JSON inputs, specifically the use of 'applications' instead of 'available_apps'"

### [supporting] — NOT SELECTED for verification (ranked below top-25 budget)
> The work is a 2026 primary research paper from IBM Research, presented at the 1st International Workshop on Agentic Engineering co-located with ICSE 2026, evaluated on 1,940 LLM calls across gpt-4o, mistral, and llama-4.

Quote: "1st International Workshop on Agentic Engineering; April 14, 2026; Rio de Janeiro, Brazil"

## Microsoft: Taxonomy of Failure Modes in Agentic AI Systems (whitepaper + June 2026 update)
- URL: https://www.microsoft.com/en-us/security/blog/2025/04/24/new-whitepaper-outlines-the-taxonomy-of-failure-modes-in-ai-agents/
- Found via: broad/primary — canonical named methods & benchmarks  ·  Quality: primary  ·  Published: 2025-04-24  ·  Claims: 5

### [central] — VERIFIED -> CONFIRMED (vote 3-0)
> Microsoft's Taxonomy of Failure Modes in Agentic AI Systems is a primary institutional whitepaper from the Microsoft AI Red Team (published April 24, 2025), categorizing agentic failures along two axes: safety vs. security, and novel vs. existing — it is a failure-mode TAXONOMY, not a failure-attribution/localization algorithm.

Quote: "This is a primary source institutional whitepaper from Microsoft AI Red Team... Security failures are those that result in core security impacts, namely a loss of confidentiality, availability, or integrity"

### [central] — VERIFIED -> CONFIRMED (vote 3-0)
> The taxonomy explicitly identifies a category of NOVEL failure modes unique to multi-agent systems, including failures in the communication flow between agents — establishing inter-agent/node-level failure categories even though it does not provide an algorithm to localize a given failure to a specific node.

Quote: "Novel failure modes are unique to agentic AI and have not been observed in non-agentic generative AI systems... failures that occur in the communication flow between agents within a multiagent system"

### [central] — VERIFIED -> CONFIRMED (vote 3-0)
> The whitepaper does NOT provide a framework or algorithm for attributing/localizing an observed failure to a specific agent, node, module, or component in a multi-agent workflow — it taxonomizes failure TYPES rather than performing per-node blame assignment.

Quote: "The document does not provide granular attribution to specific agents, modules, or components within multi-agent systems. While it mentions multi-agent communication failures exist, it offers no framework for localizing errors to particular nodes."

### [supporting] — NOT SELECTED for verification (ranked below top-25 budget)
> Memory poisoning is named as a specific novel failure mode, illustrated by a case study walking through how an attacker corrupts an agent's memory and uses it as a pivot point to exfiltrate data through the autonomous agent.

Quote: "We take a common agentic AI feature of memory and we walk through how an cyberattacker could corrupt an agent's memory and use that as a pivot point to exfiltrate data."

### [tangential] — NOT SELECTED for verification (ranked below top-25 budget)
> The page references later (June 2026) Microsoft posts (an AutoJack exploit and an ROI study) but these are separate articles, not updates to the taxonomy itself, so the taxonomy content remains the April 2025 whitepaper.

Quote: "June 2026 references: Two related posts appear (AutoJack exploit, ROI study) but these are separate articles, not taxonomy updates"

## TRAIL: Trace Reasoning and Agentic Issue Localization
- URL: https://arxiv.org/abs/2505.08638
- Found via: terminology disambiguation — node vs span vs module vs step granularity  ·  Quality: primary  ·  Published: 2025-05-13 (v1); last revised 2025-06-23 (v3)  ·  Claims: 5

### [central] — NOT SELECTED for verification (ranked below top-25 budget)
> TRAIL localizes errors to individual OpenTelemetry/OpenInference spans in agentic traces (each LLM call or tool call is one span), making span the node-level unit of attribution; annotators mark span ID, error category, evidence, description, and impact per span.

Quote: "Annotators evaluated each LLM and tool span in sequence, marking span ID, error category, evidence, description, and impact ... all traces are collected via `opentelemetry` ... specifically, its most widely adopted open-source derivative compatible with agents, the `openinference` standard"

### [central] — NOT SELECTED for verification (ranked below top-25 budget)
> TRAIL defines a formal three-category error taxonomy for agentic systems: Reasoning Errors (e.g., hallucinations, tool output misinterpretation, tool selection error, formatting errors, instruction non-compliance), System Execution Errors (e.g., incorrect tool definition, rate limiting, authentication/service errors, resource exhaustion, timeout), and Planning & Coordination Errors (context handling failures, resource abuse, goal deviation, task orchestration errors).

Quote: "Reasoning Errors: Hallucinations ... Tool Selection Error ... Formatting Errors ... Instruction Non-compliance; System Execution Errors: Incorrect Tool Definition ... Rate Limiting (429) ... Resource Exhaustion ... Timeout Issues; Planning and Coordination Errors: Context Handling Failures ... Goal Deviation ... Task Orchestration Errors"

### [central] — NOT SELECTED for verification (ranked below top-25 budget)
> The benchmark evaluates models on JOINT error detection and localization, scored by Category F1, Location Accuracy, and Joint Accuracy — i.e., a model must predict both the error category AND its location (span) in the trace to be correct.

Quote: "Category F1 ... Location Accuracy ... Joint Accuracy ... predicting error categories and their location"

### [central] — NOT SELECTED for verification (ranked below top-25 budget)
> State-of-the-art LLMs perform very poorly at this trace-debugging / error-localization task: the best model, Gemini-2.5-pro, scored only 11% on TRAIL overall (18.3% joint accuracy on the GAIA split, 5.0% on the SWE-Bench-Lite split).

Quote: "The best Gemini-2.5-pro model scoring a mere 11% on TRAIL ... gemini-2.5-pro shows "0.183" and "0.050" joint accuracy respectively"

### [supporting] — NOT SELECTED for verification (ranked below top-25 budget)
> TRAIL consists of 148 large human-annotated traces totaling 1987 OpenTelemetry spans (575 of which exhibit at least one error), curated from single- and multi-agent systems on two environments: GAIA (open-world information retrieval) and SWE-Bench-Lite (software-engineering / GitHub issue resolution).

Quote: "148 large human-annotated traces ... totaling 1987 open telemetry spans, of which 575 exhibit at least one error ... curate traces from both single and multi-agent systems ... SWE-Bench Lite ... GAIA benchmark"

## C3: Contextual Counterfactual Credit Assignment for Multi-Agent RL in LLM Collaboration (2026)
- URL: https://github.com/EIT-EAST-Lab/C3
- Found via: academic/technical — causal & counterfactual blame assignment  ·  Quality: primary  ·  Published: 2026 (arXiv 2603.06859)  ·  Claims: 4

### [supporting] — NOT SELECTED for verification (ranked below top-25 budget)
> C3 assigns per-agent causal credit in multi-agent LLM collaboration using fixed-context counterfactual replay plus a leave-one-out baseline, attributing to the AGENT as the unit (a workflow-graph-node-level granularity, not reasoning-step granularity).

Quote: "Terminal-only feedback in multi-agent LLM collaboration diffuses credit across an entire trajectory. C3 freezes transcript-derived context and estimates local causal credit with fixed-context replay plus a leave-one-out baseline, outperforming MAPPO and MAGRPO while improving fidelity, variance, and inter-agent influence under matched budgets."

### [supporting] — NOT SELECTED for verification (ranked below top-25 budget)
> C3's attribution algorithm is explicitly counterfactual/causal: it performs protocol-level replay with fixed-context alternatives and leave-one-out credit assignment to isolate each agent's contribution from interaction transcripts.

Quote: "Protocol-level replay, fixed-context alternatives, and leave-one-out credit assignment."

### [tangential] — NOT SELECTED for verification (ranked below top-25 budget)
> C3 is a reinforcement-learning TRAINING/optimization method (baselines MAPPO, MAGRPO, SFT on a math task), not a post-hoc failure-attribution or error-localization tool — the README contains no mention of failure attribution, error localization, or diagnosing which agent caused a failure, and it runs full training sweeps rather than diagnosing existing workflows.

Quote: "C3 reaches superior performance given matched training token budgets ... Running full training sweeps"

### [tangential] — NOT SELECTED for verification (ranked below top-25 budget)
> C3 is published as arXiv 2603.06859 by Yanjun Chen et al. (EIT-EAST-Lab), Apache-2.0 licensed, with a project page, situating it as 2026 primary research on counterfactual credit assignment.

Quote: "Authors: Yanjun Chen et al.; arXiv ID: 2603.06859; Paper URL: https://arxiv.org/abs/2603.06859; Project Page: https://eit-east-lab.github.io/C3/; License: Apache-2.0"

