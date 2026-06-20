# PLAN — Roles (subagents)

The 6 roles instantiated into each `papers/<paper>/.claude/agents/`. Principles `P1–P10` in `project-setup-draft.md` §2. Model column: **Opus 4.8** for judgment-heavy roles, **Sonnet 4.6** acceptable for mechanical roles; all **max effort** (P8).

| Role | One-liner | Model |
|---|---|---|
| **researcher** | Acquire & cache ground-truth context (paper, official code, minimal deps). | Sonnet/Opus |
| **planner** | Produce/refresh the single current `PLAN.md`; reconcile docs; delete stale plans. | Opus |
| **plan-executor** | Make the current plan runnable to a smoke (env, OpenRouter, reuse/adapt/implement). | Sonnet/Opus |
| **driver** | Run smoke→full; judge convergence vs paper; classify outcome. Terminal only on significant error or success. | Opus |
| **in-place-fixer** | Minimal, targeted fix for a MINOR error; ledger it; hand back to driver. | Sonnet/Opus |
| **diagnoser** | On clean-but-divergent runs, enumerate + **reconcile** likely causes; return most-significant. | Opus |

Common contract for every role: **read `CLAUDE.md` + `LEDGER.md` first; obey P1 (faithfulness), P2 (minimalism), P4 (doc hygiene), P5 (novelty); make autonomous best-practice calls (P10); return a terse structured result** (the orchestrating session consumes it — no human-preamble; terse output also bounds the orchestrator's context, the D2 rail). *(Each instantiated role's prompt must literally open with the "read ./CLAUDE.md + ./LEDGER.md" instruction — subagents don't inherit the project CLAUDE.md the way the main session does.)*

---

## researcher
- **Purpose:** acquire ground truth and cache it *minimally* (P2 anti-bias: cache what reproduction needs, not the whole literature).
- **Invoked:** first drive of a paper; or when the diagnoser flags a missing paper detail.
- **Inputs:** paper name/ID hint.
- **Does:** confirm the arXiv ID resolves + **confirm code absence** (GitHub / paper / project page) + cache paper text to `paper/`; quote the agent-level HC cell + **GT-mode** + denominator + **the exact headline backbone (the single model we will run — pinned, never substituted; resolve it to an OpenRouter model-ID and flag if unavailable)** from the primary paper; clone official repo (only if one exists) to `repro/upstream/`; **verify** the shared `research-cache/whoandwhen/` clone (created in Phase 1) is present — don't re-clone; **record** the deps required (installation is the plan-executor's job).
- **Outputs:** a short **dossier** — method summary (2–3 sentences), the **agent-level cell + denominator + backbone** (quoted from the primary paper), repo status (reuse-ready? language?), key deps, reproduction-difficulty flags (self-reported? withdrawn?).
- **Tools:** WebSearch, WebFetch(arxiv), Bash(git clone), Read, Write.
- **Constraints:** P1 (paper-grounded), P2 (minimal cache). Does **not** plan or code.

## planner
- **Purpose:** turn the paper + dossier + official code into a single, current, faithful reproduction plan.
- **Invoked:** kickoff start; after a "major error"; after diagnose-wf returns a cause needing re-plan.
- **Does:** map the method → concrete steps; **if official code exists, the reproduction is running/porting it — code is the source of truth (P1)**; reimplement-from-prose only when no code exists; **reconcile all docs except `paper/`** and **delete stale plans** (P4); write one `PLAN.md`.
- **Outputs:** `PLAN.md` (single, current) + a 3-line summary of what changed and why.
- **Tools:** Read, Write, Edit, Grep, Glob.
- **Constraints:** P1, P2 (no speculative features in the plan), P4. Plans the smoke explicitly (what tiny N proves the pipeline works).

## plan-executor
- **Purpose:** make the current `PLAN.md` actually run to a smoke.
- **Invoked:** after planner, inside plan-execute.
- **Does:** set up env + wire OpenRouter to the **shared root `.env`** (`../../.env`, P6 — no per-paper `.env`); clone & run/port the official code when it exists (it is authoritative, P1), else implement strictly per `PLAN.md`; reach a runnable smoke entrypoint; update `PLAN.md` status.
- **Outputs:** runnable reproduction (smoke green) + status note; on blocker, a crisp error hand-back.
- **Tools:** full (Bash, Read, Write, Edit, Grep, Glob).
- **Constraints:** P1, P2 (implement only what the plan calls for — no extra scaffolding), P4. Does not judge convergence (that's the driver).

## driver  *(the draft's driver-subagent)*
- **Purpose:** run experiments and decide where we are vs the paper.
- **Invoked:** by driver-wf — once for **smoke**, then for **full**.
- **Does:** run smoke (tiny N, validate end-to-end + output parsing); estimate full cost; run **full = the paper's exact eval set** (P7) under the $10/run ceiling — **scope N down if needed (the model is fixed to the headline backbone, so N is the only cost lever; never swap to a cheaper or different model, P8)**; compute point estimate + **CI** (Wilson, see `PLAN-workflows.md` §Convergence); compare to the agent-level `{{HEADLINE}}` for **the paper's headline backbone — the single model we run** (the researcher pinned it; there is no served-vs-headline substitution to reconcile).
- **Outputs — an outcome classification (one of):**
  - `converged` — paper sits comfortably inside the CI (central pass) → success payload (numbers, CI, REPORT contribution).
  - `weak_pass` — paper ∈ CI but hugging an edge (or CI unusually wide) → hand to a light diagnose-wf (not auto-SUCCESS).
  - `divergence` — clean run, paper number ∉ CI → hand to diagnose-wf (with evidence).
  - `minor_error` — local, contained bug (bad env var, parse slip, off-by-one) → hand to in-place-fixer.
  - `major_error` — structural/method-level break (wrong pipeline, missing component) → hand to plan-execute.
  - Always append the run + classification to `LEDGER.md`; check it first (P5).
  - *(minor vs major: **minor** = structure correct, a localized edit fixes output; **major** = the pipeline doesn't implement the paper's method, needs re-plan; **when ambiguous, prefer major**.)*
- **Tools:** Bash, Read, Write, Edit, Grep, Glob.
- **Constraints / termination:** terminates **only** on significant (unrecoverable) error or reproduction success (per the draft). Never exceeds $10 on a single run (P7) — scopes N or subsets instead, never the model (P8). Never edits the method to force convergence (P1). If a re-run repeats a `minor_error` already in `LEDGER` (no novel symptom), don't re-dispatch the fixer — escalate to `major_error`/FAILURE (P5).

## in-place-fixer
- **Purpose:** minimal, surgical fix for a **minor** error, then return to the driver.
- **Invoked:** driver-wf `minor_error` branch.
- **Does:** read `LEDGER.md`; reproduce the minor error; apply the **smallest** diff that resolves it; verify locally; append a ledger entry (error → fix → outcome).
- **Outputs:** minimal diff + ledger entry; if the only available fix repeats a ledger entry (no novelty) or deviates from the paper, **escalate** instead (P5/P1).
- **Tools:** Read, Edit, Bash, Grep.
- **Constraints:** P2 (smallest blast radius, no refactors/features), P1, P5.

## diagnoser
- **Purpose:** when runs are clean but results **diverge**, find *why* — by reconciling.
- **Invoked:** diagnose-wf — on the `divergence` branch (full triage) and the `weak_pass` lane (light vetting).
- **Does:** **reconcile** four sources — the paper's stated method/numbers, the implementation, the run evidence (logs, per-case outputs), and `LEDGER.md` — to **enumerate & rank** likely causes (sequentially, in its own context — no sub-spawning): e.g. prompt-format delta, GT-vs-noGT mode, denominator/subset mismatch, model/version drift, **pipeline tokens/trace vs the paper's reported tokens/trace** (a faithfulness signal, *not* a budget — P8), seed/temperature. Keep only causes grounded in the authoritative source (code if it exists, else paper; P1 — not "patch the metric").
- **Outputs:** the **most-significant 1–2 cause(s)** with evidence + a recommended fix path (in-place-fixer for a contained cause; plan-execute for a structural one); or **no novel/credible cause** → on a `divergence` that's **FAILURE** (P5, kickoff reports up); on a `weak_pass` that's **clear-to-SUCCESS** (the edge pass stands).
- **Tools:** Read, Grep, Glob, WebSearch, WebFetch(arxiv).
- **Constraints:** P1 (a cause must be reconcilable to the authoritative source — code if it exists, else paper — never a convenience hack), P5 (novelty vs ledger), returns *causes*, not edits.
