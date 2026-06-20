# PLAN — Factories

Specs + skeletons for the three root-level templates in `factories/`. A factory is a **template**; instantiation = copy into `papers/<paper>/`, fill `{{PLACEHOLDERS}}`, delete inapplicable lines. Principles `P1–P10` are defined in `project-setup-draft.md` §2.

**Placeholders** (filled by the researcher dossier at instantiation): `{{PAPER_TITLE}}`, `{{ARXIV_ID}}`, `{{REPO_URL_OR_NONE}}`, `{{PIPELINE_MODEL}}` (**the exact model the paper uses for its headline number** — the researcher pins the single headline backbone; not a menu of backbones to experiment across — served via OpenRouter), `{{EVAL_SET}}` (e.g., "Who&When hand-crafted, N=58"), `{{HEADLINE}}` (the agent-level number to reproduce + denominator), `{{OPENROUTER_MODEL_ID}}` (concrete OpenRouter slug for **the headline backbone** — researcher-resolved; an unavailable headline backbone is an instantiation blocker, never a substitution), `{{KEY_DEPS}}`.

---

## 1. `CLAUDE-factory.md` → `papers/<paper>/CLAUDE.md`

The per-paper project's house rules + persona + setup guide. Skeleton:

```markdown
# {{PAPER_TITLE}} — Reproduction

You are a veteran AI researcher + AI engineer reproducing this paper's headline result.

## Source of truth (P1)
- Official code: {{REPO_URL_OR_NONE}}. **If it exists, the code is the SOLE source of truth** — clone to `repro/upstream/`, reproduce by running/porting it; the paper is only context for reading the code. Code-vs-prose conflicts resolve to the **code**.
- Paper: {{ARXIV_ID}} — cached at `paper/`. Authoritative **only when no official code exists** → then reimplement strictly from its prose, re-reading before any method decision.
- *(At instantiation: if {{REPO_URL_OR_NONE}} = "none", the paper is authoritative; else the code is.)*
- Target to reproduce: agent-level {{HEADLINE}} on {{EVAL_SET}} (step-level out of scope). SUCCESS = a central pass (the number sits comfortably inside our CI); a weak/edge pass is diagnosed, not auto-accepted (P7).

## How we work (the rails — all soft, P3)
- **Faithfulness firewall (P1):** never change the method to improve numbers. A "fix" that deviates from the authoritative source (official code if it exists, else the paper) is a divergence to diagnose, not a fix to keep.
- **Minimalism (P2):** build the least that reproduces the result. DO NOT add (unless the paper requires it): token budgeting, forced JSON schemas, retry/backoff frameworks, config systems, caching layers, new CLI flags, abstraction layers. Each added piece must trace to the paper or a concrete run need.
- **Docs (P4):** durable docs are this file, the current `PLAN.md`, `LEDGER.md`, `REPORT.md`, and `paper/`. Reconcile on every plan; delete stale plans.
- **Ledger (P5):** read `LEDGER.md` before any fix/diagnosis; append every attempt + outcome; a loop with no NOVEL error/cause is stuck → escalate.
- **Models (P8):** orchestration = Opus 4.8 / Sonnet 4.6, max effort (`effort: max`); pipeline model = **the paper's exact headline backbone via OpenRouter — never substituted or experimented across backbones.** Ignore token budgeting.

## Setup guidelines
- **OpenRouter + shared root `.env` (P6):** all pipeline model calls use OpenRouter; the key lives in the **single root `.env`** shared by every paper project — read it by relative path (`../../.env`, like the shared Who&When clone); **never create a per-paper `.env`.** Pipeline model = {{PIPELINE_MODEL}} — **exactly the paper's headline backbone; never substituted or experimented with (P8).**
- **Deps:** {{KEY_DEPS}}. Reference the shared benchmark at `../../research-cache/whoandwhen/` (read-only, relative path) — **do not** symlink (OneDrive/Windows footgun), copy, or re-download.
- **Runs:** smoke output + full output + logs go in `runs/` (gitignored). Smoke first, always (P7).
- **Workflows:** follow the playbooks in `workflows/` (plan-execute, driver-wf, diagnose-wf).

## Reproduction-specific notes
{{ per-paper gotchas the researcher/planner discover — e.g., prompt formatting, GT-vs-noGT setting, denominator quirks }}
```

**Repo-level `CLAUDE.md` (existing 2-liner):** in Phase 1, append a short "Setup guidelines" block pointing at `factories/` and the 10 principles — so any session opened at the repo root inherits the constitution. Keep it short (P2).

---

## 2. `SUBAGENT-factory.md` → `papers/<paper>/.claude/agents/*.md`

A catalog of the 6 role templates. Each instantiated file is a standard Claude Code subagent definition (frontmatter `name`, `description`, `tools`, `model` + the role prompt). Full per-role specs are in `PLAN-roles.md`; this factory holds their **template bodies**. Skeleton of one entry:

```markdown
---
name: driver
description: Runs smoke→full experiments, judges convergence vs the paper, classifies outcome. Terminates only on significant error or reproduction success.
tools: Bash, Read, Write, Edit, Grep, Glob
model: opus   # Opus 4.8, max effort (judgment-heavy)
---
<role prompt — see PLAN-roles.md §driver. Must literally open with "read ./CLAUDE.md + ./LEDGER.md first" (subagents don't inherit the project CLAUDE.md), then reference paper/, the rails, etc.>
```

The factory ships all six (`researcher`, `planner`, `plan-executor`, `driver`, `in-place-fixer`, `diagnoser`). Instantiation copies them unchanged except for project-path references; paper-specific behavior comes from the project's `CLAUDE.md`, not from editing the role. (Tools vary per role — see `PLAN-roles.md`; e.g. researcher/diagnoser also get WebSearch/WebFetch(arxiv), the driver gets Bash.)

---

## 3. `WORKFLOW-factory.md` → `papers/<paper>/workflows/*.md`

The three workflow **playbooks** (MD, not code — P3) the kickoff session follows. Each playbook = ordered steps + explicit branch table + which role it invokes. Full specs in `PLAN-workflows.md`; this factory holds their template bodies:

- `plan-execute.md` — planner (reconcile docs, delete stale plans, write current `PLAN.md`) → plan-executor (make it run to smoke).
- `driver-wf.md` — driver smoke → driver full → branch (minor→fixer / major→plan-execute / divergence→diagnose-wf / converged→success); ledger novelty guard.
- `diagnose-wf.md` — enumerate & rank likely causes (source-grounded) → reconcile → return most-significant cause(s), or "no novel cause → failure".

Kickoff and master are orchestration **behaviors** the session follows (described in `PLAN-workflows.md`), not separate workflow files.

---

## 4. Instantiation procedure (factory → paper project)

Run once per paper (the draft's "For each paper" 1–5):

1. **Cache deps + research** — researcher writes the dossier; **confirms code absence** (clone to `repro/upstream/` only if code exists); caches paper text into `paper/`; **verifies** the shared `research-cache/whoandwhen/` clone (created in Phase 1) is present. (No code found for the three current targets → reimplement-from-prose is the default path.)
2. **`CLAUDE.md`** — copy `CLAUDE-factory.md`, fill placeholders from the dossier.
3. **Roles** — copy the 6 agents from `SUBAGENT-factory.md` into `.claude/agents/`.
4. **Workflows** — copy the playbooks from `WORKFLOW-factory.md` into `workflows/`.
5. **Project glue** — write `.claude/settings.local.json` (perms, see `PLAN-workflows.md` §perms), **wire the pipeline to the shared root `.env` (`../../.env`) — do not create a per-paper `.env`**, empty `LEDGER.md`, and a `.gitignore` for this project's `runs/`/`__pycache__`/caches (the **root** `.gitignore` already ignores the shared root `.env`). Then the master proceeds to this paper's kickoff phase.

**Frozen-factory rule (P2/P4):** the pilot (the first paper in the canonical order — see `project-setup-draft.md` §7) may surface factory gaps — fix them in `factories/` once, re-instantiate. After the pilot, change factories **only for a genuine cross-paper defect**, then re-instantiate all projects; **never fork roles/workflows per paper.** Paper-specific variance lives in the project `CLAUDE.md`/`PLAN.md`.
