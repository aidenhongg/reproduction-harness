# Paper-Reproduction Factory — Master Setup Plan

**Status:** PLAN (nothing built yet). **Scope:** paper reproduction only — FALAT, ECHO, Shapley/PCI.
**This file** = the authoritative overview + locked decisions + build sequence. Detail lives in the `PLAN-*.md` siblings (index below). The original brain-dump is preserved verbatim in the Appendix.

---

## 1. Goal

Build a **reusable reproduction factory**: a small set of root-level *templates* (factories) that instantiate a **self-contained reproduction project per paper**, plus an **autonomous orchestration loop** that drives each project to one of two terminal states:

- **SUCCESS** — the paper's headline number is reproduced (the paper's figure falls inside our run's confidence interval), or
- **FAILURE** — a credible, paper-grounded *cause of divergence* is identified and reported (no novel cause left to try).

Target papers (reproduce one at a time): **FALAT** (pilot), then **ECHO**, then **Shapley/PCI** — all failure-attribution methods evaluated on the **Who&When** benchmark, **no official code found** (reimplement from prose; researcher confirms absence at instantiation). See `PLAN-papers.md`.

## 2. Design constitution — the 10 principles

These are the soft rails. Every factory, role, and workflow references them by number. (Canonical list; elaborated where used.)

| #       | Principle                                      | Essence                                                                                                                                                                                                                                                                                                                    |
| ------- | ---------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **P1**  | **Source of truth: author's code first, else the paper**                      | If the paper's **official author code exists, that code is the SOLE source of truth** — reproduce by running/porting it; the paper is only context for understanding it, and code-vs-prose conflicts resolve to the code. If **no official code exists**, the **paper is the source of truth** — reimplement strictly from its prose. Never deviate from whichever is authoritative to improve numbers (faithfulness firewall). *(Our three targets have no code found in the dossier → paper authoritative; researcher confirms absence at instantiation.)*                                                                                         |
| **P2**  | **Minimalism / no feature drift**              | Build the least that reproduces the result. Explicitly forbid Claude's habitual additions — token budgeting, forced JSON schemas, retry frameworks, config systems, caching layers, new CLI flags — unless the **paper** requires them. Every component must trace to a paper requirement or a concrete reproduction need. |
| **P3**  | **Soft rails, not hard rails**                 | All control logic (stop/continue, smoke vs full, cost ceiling, convergence) lives in **natural-language context the agent reasons over** — never in brittle deterministic guardrail code (no budget enforcers, no schema validators that hard-fail).                                                                       |
| **P4**  | **Doc reconciliation, single source of truth** | At each planning step, reconcile all docs against current state and **delete stale plans**. Durable docs only: the authoritative source — official code if it exists, else the paper (immutable), `CLAUDE.md` (rules), the **current** `PLAN.md`, `LEDGER.md` (fixes), `REPORT.md` (results). Everything else is ephemeral.                                                     |
| **P5**  | **Ledger + novelty stop**                      | `LEDGER.md` is an append-only, concise record of every fix/diagnosis + outcome. Every fixer/diagnoser reads it first. **Hard stop: a loop that produces no _novel_ error/cause (only ledger repeats) is stuck → escalate/fail.**                                                                                           |
| **P6**  | **OpenRouter + .env**                          | All *reproduction-pipeline* model calls go through OpenRouter using the key in the **single root `.env`** (`existing-optim/.env`, gitignored) — **every paper project reads that same root `.env`** by relative path (`../../.env`), exactly like the shared Who&When clone; **no per-paper `.env`.** Distinct from the Claude Code orchestration layer.                                                                                                                                                                        |
| **P7**  | **Cost & stat-sig as context**                 | Smoke-test first (tiny N, ~free). Estimate cost before any full run. **Never launch a single run > $10** — scope N down to stay under. **Success = paper's number ∈ our run's CI** — but a wide-CI/edge "weak pass" is diagnosed, not auto-accepted (see `PLAN-workflows.md`). Pick N large enough that the CI can meaningfully include/exclude the paper's figure. All judgment, not enforced code.                  |
| **P8**  | **Models & effort**                            | Orchestration agents: **Opus 4.8 / Sonnet 4.6 at max effort**. Reproduction-pipeline model: **exactly the model the paper uses for its headline number** (the researcher pins the single headline backbone), via OpenRouter — **never substitute a different/cheaper model or experiment across backbones** (that's a divergence, not a reproduction). **Ignore token-usage budgeting entirely.**                                                                                                                                       |
| **P9**  | **Preserve everything**                        | Never delete a paper project, even on failure — it is the record. (P4's deletion applies to *stale plans within* a project, not projects.)                                                                                                                                                                                 |
| **P10** | **Autonomy**                                   | Make carefully-reasoned best-practice decisions without asking; prefer idiomatic patterns; escalate only on genuine ambiguity or significant error.                                                                                                                                                                        |

## 3. Architecture at a glance

Three Claude Code primitives map to the draft's three nouns:

- **session** → an orchestrating Claude Code context (Opus 4.8) that follows playbooks and dispatches subagents via the Agent tool — **not** a `claude -p` process. *Master* and *kickoff* are sessions in this sense.
- **subagent (role)** → a `.claude/agents/<role>.md` agent, invoked via the Agent tool inside a session.
- **workflow** → an **MD playbook** (`workflows/<name>.md`) a session follows to orchestrate roles. **Not** deterministic Workflow-tool JS (that would violate P3).

```
master session (thin queue loop; persists only a 1-line outcome log)
  └── kickoff session  [one per paper drive — a FRESH context; rebuilds state from durable docs]
        ├── researcher                    (first drive only: cache paper+code+deps)
        ├── workflow: plan-execute        (planner → plan-executor)
        └── workflow: driver-wf
              ├── driver-subagent: smoke → full → classifies the run
              └── route on the driver's classification:
                    minor error → in-place-fixer → driver-subagent
                    major error → plan-execute → driver-wf
                    divergence  → workflow: diagnose-wf → (fix path) → driver-wf
                    weak pass   → light diagnose-wf → SUCCESS only if no fidelity cause ↑
                    converged   → REPORT success ↑
              (loop guard: no novel error/cause vs LEDGER → FAILURE ↑)
```

**Decision D1 — substrate = built-in Claude Code subagents (the Agent/Task tool), not `claude -p`.** Roles are `.claude/agents/*.md` subagents dispatched by an orchestrating session that follows the MD workflow playbooks. *Master* and *kickoff* are orchestrating sessions/contexts, not spawned processes: the master sequences the paper queue and runs each kickoff loop, dispatching single-purpose role subagents via the Agent tool. To stay within built-in nesting limits, the orchestrator dispatches role subagents directly rather than nesting agents-within-agents. Rationale: idiomatic Claude Code; keeps orchestration agent-reasoned (P3); no brittle process-spawning. `claude -p` is avoided unless a specific need appears; the deterministic Workflow tool stays available for bounded fan-out inside a role, but is not the control loop. **In short:** there is exactly **one** orchestrating context; *master* and *kickoff* are named phases it moves through, *workflows* are MD playbooks it reads, and the only sub-invocations are leaf role-subagents (which don't dispatch further); "fresh context per paper" is a reset/rebuild from durable docs, not a spawned child.

## 4. Directory layout (target)

```
existing-optim/
  CLAUDE.md                     # repo-level persona + pointer to factories (setup-guidelines appended)
  project-setup-draft.md        # THIS master plan
  PLAN-factories.md  PLAN-roles.md  PLAN-workflows.md  PLAN-papers.md
  .env.example                  # committed template — copy to .env and add your OpenRouter key
  .env                          # SINGLE shared OpenRouter key — every paper project reads THIS (gitignored)
  .gitignore                    # .env (shared root), papers/*/runs/, __pycache__, large caches
  factories/
    CLAUDE-factory.md
    SUBAGENT-factory.md
    WORKFLOW-factory.md
  research-cache/               # shared, cache-once context
    whoandwhen/                 # the shared benchmark (cloned once)
    <paper>/                    # per-paper extracts
  papers/
    falat/                      # self-contained reproduction project (PILOT) — reads the shared root .env
      CLAUDE.md  LEDGER.md  REPORT.md  PLAN.md
      .claude/{agents/*.md, settings.local.json}
      workflows/{plan-execute.md, driver-wf.md, diagnose-wf.md}
      paper/                    # cached paper text (no official repo exists → reimplement)
      repro/                    # reproduction code (reimplemented from prose)
      runs/                     # smoke + full outputs, logs (gitignored)
    echo/   shapley/            # same shape, instantiated from factories
```

## 5. Build sequence (phases)

- **Phase 0 — Decisions locked.** This plan + the `PLAN-*.md` set. *(current)*
- **Phase 1 — Scaffold factories.** Write `factories/*.md`; append setup-guidelines to repo `CLAUDE.md`; create `research-cache/` (for the shared Who&When clone + per-paper extracts); add the root `.gitignore`, the committed **`.env.example`** template, **and the single root `.env`** (the shared OpenRouter key every paper project reads — created by copying `.env.example`; gitignored). *(`.env.example` + `.gitignore` already exist as of plan revision — the only manual step is pasting a real key into `.env`.)* *(The stray `_agentracer_extract.txt` is an excluded method — leave it out of the shared cache.)*
- **Phase 2 — Pilot on FALAT (end-to-end).** researcher dossier → instantiate `papers/falat/` from factories → run one kickoff session → drive to SUCCESS/FAILURE → REPORT. (If FALAT can't be sourced/gated, pilot the next paper instead — see `PLAN-papers.md`.) **This validates the factory before scaling.**
- **Phase 3 — Generalize.** Instantiate + drive ECHO, then Shapley/PCI (only paper-specific bits change; factory frozen unless the pilot exposes a gap).
- **Phase 4 — Master loop.** Master session iterates the paper queue; on each terminal drive, stop or launch the next kickoff. Projects (incl. failures) are preserved (P9).

**Why pilot-first, why FALAT:** pilot on one paper end-to-end so factory fixes happen once, not three times. FALAT is your priority target and the newest/most-relevant method; it's prompt-based (dependency-guided search), and piloting it stands up the OpenRouter wiring early. Shapley/PCI goes last — its bespoke CDC-MAS causal-discovery reimplementation is the highest method-risk and benefits from a battle-tested factory.

## 6. Deliverables index (the plan set)

| File | Contains |
|---|---|
| `project-setup-draft.md` | **(this)** overview, the 10 principles, architecture, layout, phases, decisions |
| `PLAN-factories.md` | full spec + skeletons for `CLAUDE-factory.md`, `SUBAGENT-factory.md`, `WORKFLOW-factory.md`; instantiation procedure |
| `PLAN-roles.md` | the 6 roles (researcher, planner, plan-executor, driver, in-place-fixer, diagnoser): purpose, I/O, tools, model, constraints, termination |
| `PLAN-workflows.md` | the 3 workflows + kickoff + master state machine; the rails-as-context (P3/P5/P7) operationalized; perms |
| `PLAN-papers.md` | target papers (IDs, repos, headline numbers, deps), shared Who&When dependency, per-paper instantiation checklist, reproduction targets + success rule |

## 7. Open decisions / assumptions (flag to flip)

- **D1 substrate** = built-in Claude Code subagents (Agent/Task tool), not `claude -p` (see §3).
- **D2 context rail** = fresh **context** per paper — the orchestrator re-derives state from the durable docs (CLAUDE.md/PLAN.md/LEDGER.md/REPORT.md) rather than carrying one long-lived context forward (a logical reset, not necessarily a new OS process); subagents return terse results (detail in `PLAN-workflows.md`). Soft rail, not token budgeting (P8 = cost, not context).
- **Paper order (canonical)** = **FALAT → ECHO → Shapley** (FALAT pilot — your priority target; Shapley last — hardest). This is the single source of truth for order; other files mirror it.
- **Papers identified** (dossier; researcher verifies primary-paper specifics at instantiation): FALAT 2606.00765, ECHO 2510.04886, Shapley/PCI 2509.08682. **No official code found → reimplement from prose** (researcher confirms absence; raises method-risk; P1 firewall central). Targets + verify-at-instantiation notes in `PLAN-papers.md`.
- **Repo CLAUDE.md** = the repo-level `existing-optim/CLAUDE.md` (currently 2 lines) gets the setup-guidelines appended in Phase 1; the *per-paper* `CLAUDE.md` is what the factory generates.

---

## Appendix A — Original requirements (verbatim brain-dump)

> 1. Papers to reproduce one by one - FALAT, ECHO, Shapely
> 2. Download and cache as context any research on any appropriate dependencies that we need.
>
> add CLAUDE-factory.md - Veteran AI researcher, continuously refer back to original paper source (if code of paper exists, either reuse or refer back to it), workflow guide, etc.
> 	SETUP-guidelines to CLAUDE.md
>
> add SUBAGENT-factory.md - Defining in-place-fixer, planner, researcher, plan-executor, and driver roles as well as diagnoser (diagnoser focuses on reconciling )
> 	Driver-subagent must terminate in significant error or paper reproduction success
> add WORKFLOW-factory.md
> 	1. Diagnose workflow (research, diagnose for all likely causes)
> 	2. plan-execute - reconciles any documentation except paper source and deletes old plans)
> 	3. Driver-wf (Drive and hand off to in-place-fixer or return success / failure signal)
>
> For each paper,
> 1. Copy over the dependencies and research.
> 2. Define CLAUDE.md
> 3. Define the roles.
> 4. Define the workflows
> 5. master session
>
> Things to consider for drivers only:
> 	Smoke tests, statistical sig, API buget
> 	Avoiding bias by being appropriately minimal with docs
> 	Do not launch any runs individually abqove $10 in cost - always scope below, return success if reported number is within that CI
> Things to consider for all subagents and workflows:
> 	Reconciling docs appropriately and dont let old planning docs persist
> 	Preventing feature drift (Claude adds unnecessary features like token budgeting, json schema forcing, etc.)
> 	Keep concise ledger of all past fixes that any fix / diagnose wf must check against - failure condition is no NOVEL errors produce
> 	Use Openrouter always - use .env
> 	Keep things minimal wherever possible, no additional unnecessary bells and whistles
> 	Go with carefully reasoned best recommendations autonomously. Generally go with best or idiomatic practices or patterns.
>
> kickoff session:
> 1. Plan-execute initial paper rep, driver-wf
> 2. driver-wf - Smoke test to driver-subagent, then full to driver-subagent for properly budgeted sufficiently statistically significant signal
> 	1. minor errors -> in-place-fixer -> driver-subagent
> 	2. major errors -> plan, execute, driver-wf again
> 	3. No errors but paper divergence -> diagnose-wf
> 	4. Paper convergence -> report success up the chain
> 3. diagnose-wf no causes reported -> report failure
> 	1. Only return most significant cause(s) to fix
>
> Master session launches kickoff session
> 	On each recorded successful drive, either stop or do another kickoff session
>
> Also consider appropriate perms and carefully removing inappropriate addons that may consume context (only deny perms and addons you KNOW FOR SURE will be unnecessary)
> Maintain past projects even if failed so I can see what happened
> Do not use brittle, deterministic guardrails anywhere in orchestration and dont consider token budgeting - write things like statsig / API budgeting / other rails into context instead
> All agents should be opus 4.8 / sonnet 4.6 on max effort
> Ignore token usage budgeting
