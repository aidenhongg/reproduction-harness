# PLAN — Workflows, sessions & rails

The 3 workflow playbooks + kickoff + master, plus the rails-as-context (P3/P5/P7) and perms. All orchestration is **agent-reasoned from MD**, never deterministic guardrail code (P3). Principles in `project-setup-draft.md` §2.

**Invariant (D1):** there is **one** orchestrating context; roles never invoke other roles — every arrow below is that context reading a returned classification and dispatching the next leaf role.

---

## Workflow: `plan-execute`
**Purpose:** produce a runnable, faithful reproduction at smoke quality.
**Steps:** (1) **planner** → reconcile all docs except `paper/`, delete stale plans, write one current `PLAN.md` (P4). (2) **plan-executor** → make it run to smoke (env, OpenRouter via the **shared root `.env`** at `../../.env`, reuse/adapt/implement per plan).
**Exit:** smoke entrypoint runs → enter `driver-wf`. Hard blocker → escalate to kickoff with the crisp error.
**Entry:** kickoff start; `driver-wf` `major_error`; diagnose-wf structural cause.

## Workflow: `driver-wf`
**Purpose:** drive experiments and route on outcome. **Hands off to roles; never decides via code (P3).**
**Steps:**
1. **driver (smoke)** — tiny N, validate the pipeline runs end-to-end and outputs parse. Smoke failure that's contained → `minor_error`; smoke failure that's structural → `major_error`.
2. **driver (full)** — run the paper's exact eval set under the cost ceiling; compute point estimate + CI; classify.
3. **Branch table:**

| Driver classification | Route |
|---|---|
| `minor_error` | → **in-place-fixer** → back to **driver (re-run the failed stage)** |
| `major_error` | → **plan-execute** (re-plan) → **driver-wf** |
| `divergence` (clean, paper ∉ CI) | → **diagnose-wf** |
| `weak_pass` (paper ∈ CI but hugging an edge / CI unusually wide) | → **light diagnose-wf**; SUCCESS only if it surfaces no fidelity cause |
| `converged` (paper sits comfortably inside the CI) | → write `REPORT.md`, **report SUCCESS up** |

4. **Loop guard (P5):** before any fixer/diagnoser re-entry, check `LEDGER.md`. If the cycle produced **no novel error/cause** (only repeats), stop → **report FAILURE up** with the recurring blocker. (This is the soft anti-infinite-loop rail — judgment, not a counter.)

## Workflow: `diagnose-wf`
**Purpose:** explain a `divergence`, or vet a `weak_pass` (light mode).
**Steps:** (1) **diagnoser** enumerates likely causes (sequentially, in its own context) and **reconciles** authoritative-source ↔ impl ↔ run-evidence ↔ ledger. (2) Rank; keep causes grounded in the authoritative source only — code if it exists, else paper (P1).
**Branch:**

| Result | Route |
|---|---|
| contained cause | → **in-place-fixer** → **driver-wf** |
| structural cause | → **plan-execute** → **driver-wf** |
| no credible cause, from a **weak_pass** | → **clear to SUCCESS** (the edge pass stands) |
| no novel/credible cause, from a **divergence** | → **report FAILURE up** with the most-significant cause noted (P5) |

Diagnoser returns **only the most-significant 1–2 cause(s)** to act on (per the draft).

---

## Session: `kickoff` (one per paper — a phase boundary, not a spawned process)
0. **Rebuild state from durable docs** — at this phase boundary the orchestrator reads only `CLAUDE.md` / `PLAN.md` / `LEDGER.md` / `REPORT.md` to (re)derive per-paper state; it doesn't rely on prior in-context memory (one context throughout, D1). This is the context rail (D2): bounds context use without any token budgeting (P8 = cost, not context).
1. (first drive only) **researcher** → dossier + cache + clone (instantiation step 1); pin the exact cell + denominator + backbone from the primary paper.
2. **plan-execute** → initial reproduction to smoke.
3. **driver-wf** → follow the branch table until terminal.
4. Terminal: **SUCCESS** (`REPORT.md` written, central pass) or **FAILURE** (`REPORT.md` with the most-significant cause). Append the outcome to the master one-line log. The project is preserved either way (P9).

## Session: `master` (thin queue loop)
- Holds the paper queue per the canonical order in `project-setup-draft.md` §7: `falat → echo → shapley`.
- Enters each paper's **kickoff** phase in turn (rebuilding state from durable docs, not spawning a child); on each terminal drive, **stop or move to the next** (per the draft).
- Never deletes prior projects (P9). The only state it keeps across papers is a one-line-per-drive outcome log — everything else is reconstructed from each project's durable docs.
- Implementation: a top-level orchestrating session (Opus 4.8, max effort) that iterates the queue and runs each kickoff, dispatching role subagents via the Agent tool. Subagents return **terse** structured results to bound the orchestrator's context. No separate processes; no deterministic decision logic beyond "next in queue" (P3).

---

## Rails as context (operationalizing P3 / P5 / P7)

These are written into `CLAUDE.md` + the driver role as **guidance the agent reasons over**, not enforced code.

### Smoke vs full (P7)
- **Smoke** = ~5 cases. Goal: prove the pipeline runs end-to-end and outputs parse. Not a statistical claim. Cost ≈ free.
- **Full** = **the paper's exact evaluation set** (Who&When hand-crafted, N=58 agent-level). Faithful (P1); cost depends on the method's **calls-per-case** (see §Cost — *not* one call per case for ECHO/Shapley/FALAT). The researcher confirms the exact denominator so our CI compares like-for-like.

### Cost ceiling (P7, soft)
- Estimate full-run cost *before* launching: **cases × calls-per-case × tokens/call × price**. Measure calls-per-case at smoke (ensemble size / search breadth / coalition count) — ~6–20×+ for these methods, not 1 (see `PLAN-papers.md` §Cost).
- **Never exceed $10 on a single run.** The model is **fixed to the paper's headline backbone** (P8), so the **only** cost lever is **N** — scope it down to a smaller subset of the eval set; **never switch to a cheaper or different model.** Say so in the run note — don't silently truncate.
- No hard budget enforcer in code (P3); the driver holds the ceiling as a rule and reasons about it.
- Track per-paper **cumulative** spend loosely; if it crosses several× the single-run ceiling without converging, treat it as a novelty-stop signal (P5).

### Convergence / success (P7) — agent-level only
- Score **agent-level** accuracy only (step-level is out of scope). It's a binomial proportion (k/N); report **point estimate + Wilson 95% CI** (half-width ≈11–12.5pp across our target cells, asymmetric, widest near p=0.5 — compute per cell).
- **SUCCESS (central pass)** = the paper's agent-level % ∈ our CI **and sits comfortably inside it** (not hugging an edge). CI-inclusion alone is necessary, not sufficient: the interval is wide, so inclusion is lenient and a non-rejection isn't agreement.
- **Weak pass** = paper ∈ CI but hugging an edge (or the CI is unusually wide). Don't auto-accept — route to a light `diagnose-wf`; keep SUCCESS only if no fidelity cause surfaces. `REPORT.md` states where the paper value sits within our CI and the strongest paper value the CI could still exclude.
- The researcher pins the exact agent-level **cell + denominator + backbone** from the primary paper at instantiation (some targets aren't `k/58`), so the comparison is like-for-like.
- **Underpowered guard:** the model is fixed to the headline backbone (P8), so **N is the only cost lever**; if cost forces N so low the CI can't meaningfully exclude a plausible alternative, report **indeterminate (underpowered)** — not SUCCESS. (Soft judgment, no fixed threshold — P3.)

### Ledger & novelty (P5)
- `LEDGER.md`: append-only, one concise entry per fix/diagnosis — `{symptom → hypothesis → change → outcome}`.
- Every fixer/diagnoser reads it first; **a cycle with no novel entry = stuck → FAILURE**.

### Faithfulness firewall (P1) — applies to every fix
- A change that improves numbers by **deviating from the authoritative source** (official code if it exists, else the paper) is a *divergence to diagnose*, not a fix to keep. Distinguish "bug in our reproduction of that source's method" (fix it) from "that method, faithfully reproduced, gives a different number" (report it).

---

## Permissions (per-paper `.claude/settings.local.json`)

Scope to what reproduction needs; deny only what's surely unnecessary (per the draft — don't over-restrict).

**Allow:**
- `Bash` (git, python/uv/pip, running the reproduction pipeline)
- `Read`, `Write`, `Edit`, `Grep`, `Glob`
- `WebSearch`, `WebFetch(domain:arxiv.org)`, `WebFetch(domain:github.com)`, `WebFetch(domain:raw.githubusercontent.com)`
- `Agent` (roles)

**Keep out of git (not a perm, but enforce via `.gitignore`):** the **root `.env`** (shared OpenRouter key), `runs/`, caches.

**Don't add (P2 anti-drift):** MCP servers or extra skills not needed for reproduction — they consume context for no benefit. (Heavy skills like `deep-research`/`browse` are repo-level *plan-time* tools, not per-project standing perms; targeted `WebSearch`/`WebFetch(arxiv)` cover the researcher's need.) Add only when a concrete need appears.

**Models (P8):** orchestration Opus 4.8 / Sonnet 4.6 max effort; pipeline model = **the paper's exact headline backbone** via OpenRouter (the shared root `.env`) — never substituted or experimented with. Token budgeting ignored.
