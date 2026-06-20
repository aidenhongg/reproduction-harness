# PLAN — Papers, shared dependency & reproduction targets

We reproduce **agent-level** Who&When accuracy only — **step-level is out of scope.** Targets are **paper-self-reported**, identified via a background dossier (2026-06-19); the SOTA memory corroborates the method *selection*, not the specific numbers (the deep-research dossier disclaims ECHO/FALAT). **At instantiation the researcher re-reads each primary paper and quotes the exact agent-level cell + GT-mode + denominator + backbone, and confirms code absence, before any of it gates.** **†** = dossier/memory-only, unverified locally.

## Target papers (agent-level, Who&When hand-crafted)

| Paper | arXiv | Venue † | Backbone (P6) † | Agent-level target † | Code | Repro difficulty |
|---|---|---|---|---|---|---|
| **FALAT** (pilot) | 2606.00765 | preprint | DeepSeek-V3.2 / MiniMax-2.5 — researcher pins the headline's | **73.0%** (HC) ‡ | none found | MED-HIGH — newest |
| **ECHO** | 2510.04886 | NeurIPS'25 LLM-Eval workshop (AWS) | Claude 3.5 Sonnet v2 | **68.4%** (HC) ‡‡ | none found | MED-HIGH — ensemble + compression |
| **Shapley/PCI** | 2509.08682 | AAAI 2026 | GPT-4o | **56.8%** (HC) ‡‡‡ | none found | HIGH — Monte-Carlo Shapley + PCI from prose |

> **‡ FALAT 73.0%** is **memory-only** — the dossier never surfaced a FALAT agent cell (only step-level 46.0/29.1). The companion **68.0%** is, per that same memory entry, the algorithm-generated agent cell — **not** a no-GT value. There is **no dossier-corroborated cross-paper "slash convention"** (the dossier actually lists Shapley as `48.5/56.8` = Algorithmic/Hand-Crafted); these pairings are memory-sourced, so the researcher quoting the exact cell from FALAT's **own** paper is the sole basis. We target the **HC** cell (73.0%); the researcher pins **its exact backbone** and we reproduce with **that one model only** — no experimenting across the paper's other backbones (P8). If that headline backbone isn't available on OpenRouter, it's an **instantiation blocker to surface**, never a swap for a different or cheaper model.
> **‡‡ ECHO 68.4%** appears only in **CHIEF's** comparison table, not ECHO's own paper — lowest-confidence; quote ECHO's own HC agent cell first. Agent count/temps (dossier: 6 agents, 0.3–0.9) unconfirmed.
> **‡‡‡ Shapley 56.8%** is recorded in the dossier as the bare **Hand-Crafted** agent cell (no GT-mode qualifier; a second voter cites 57.61% — resolve). Confirm GT-mode + denominator.
> **GT-mode + denominator + code:** "with-GT" is our intended cell but is **asserted, not yet sourced** for these — the researcher confirms per paper. "Code: none found" = absent from the dossier (FALAT/ECHO repos were never fetched); the researcher confirms absence (GitHub / paper / project page) before defaulting to reimplement-from-prose — if code exists it becomes the sole source of truth (P1).

## Per-paper notes

### FALAT (2606.00765) — pilot
- **Method:** **hierarchical dependency-guided search** — build an "expectation" of correct solving to flag suspicious regions, trace typed dependencies to separate error-*introducing* from error-*inheriting* steps, then a **counterfactual sufficiency check**. Score its **agent-level** output only.
- **Target:** HC agent 73.0% (‡ memory-only). **Hard pilot precondition:** confirm 2606.00765 resolves and quote the agent cell + GT-mode + denominator + backbone verbatim from the paper before it gates. **If it doesn't resolve or reports no agent-HC cell, surface the blocker and pilot the next-best-sourced paper (ECHO/Shapley) instead — don't stall factory validation.**
- **Deps:** the **single headline-cell backbone** — the paper reports DeepSeek-V3.2 / MiniMax-2.5; the researcher pins which one produces the 73.0% HC cell and we run **only that** (via OpenRouter, no substitution, P8); Who&When; reimplement from prose. Pilot also stands up the OpenRouter wiring. **Cost:** search + a counterfactual re-run per candidate → several calls/case (§Cost).

### ECHO (2510.04886)
- **Method:** (1) 4-layer hierarchical context compression; (2) an ensemble of specialized LLM agents emit structured verdicts + confidence; (3) confidence-weighted consensus → responsible agent.
- **Target:** HC agent 68.4% (‡‡ CHIEF-reported; confirm ECHO's own cell).
- **Deps:** Claude 3.5 Sonnet v2 via OpenRouter; Who&When; reimplement the ensemble + compression prompts. **Cost:** ~ensemble-size calls/case (dossier ~6) (§Cost).

### Shapley/PCI (2509.08682)
- **Method:** *SBSLocator* — "Performance Causal Inversion" + **Monte-Carlo Shapley** over agent coalitions for agent-level blame. *(The step-level CDC-MAS machinery is out of scope — but agent-level Shapley/PCI from prose is still the highest-risk reimplementation, which is why Shapley is sequenced last.)*
- **Target:** HC agent 56.8% (‡‡‡).
- **Deps:** GPT-4o via OpenRouter; Who&When; Monte-Carlo Shapley over coalitions. **Cost:** combinatorial coalition sampling → the heaviest of the three (§Cost).

## Shared dependency — Who&When (cloned once in Phase 1)
- **Repo:** `github.com/ag2ai/Agents_Failure_Attribution` (paper 2505.00212). **Phase 1 / master clones it once** into `research-cache/whoandwhen/`; each project **references it read-only by relative path** (`../../research-cache/whoandwhen/`) — the researcher only *verifies* its presence, never re-clones, copies, or symlinks.
- **Structure:** hand-crafted (the **n=58** split we target) + algorithm-generated subsets.
- **Unit:** per task = (responsible **agent**, decisive step, explanation). We score **agent-level** accuracy on the HC cell.

## Cross-cutting reproduction risks (feed these to diagnoser/driver)
1. **No code found → reimplement from prose (P1)** (researcher confirms absence first). Prime site for unfaithful drift; ambiguous prose → quote the paper, take the literal reading.
2. **Backbone heterogeneity (P1/P8).** Use **exactly** the backbone the paper's headline cell uses — the researcher pins it; we run only that one model. Swapping to a different or cheaper backbone (even to fit cost, even to another backbone the paper itself reports) is a divergence, never a fix. An unavailable headline backbone is an instantiation blocker to surface, not a substitution.
3. **Cell provenance (verify per cell).** GT-mode, denominator, and backbone of each target are dossier/memory-asserted, not sourced — the researcher pins them from the primary paper. Denominator: 56.8% is closest to a k/58 grid point (≈33/58); 68.4% and 73.0% are further off — but verify each; don't assume a non-k/58 denominator just to explain a divergence.
4. **Self-reported, single-source numbers.** A faithful reproduction that lands lower **with a paper-grounded cause is a legitimate result to report** (P1), not a bug to mask.
5. **Tokens/trace as a fidelity signal** (not a budget; P8) — compare pipeline tokens/trace vs the paper's reported value if given.

## Cost (P7) — per case × method call-multiplicity
- Full-run cost ≈ (cases) × (**calls per case**) × (tokens/call) × (price) — canonical formula in `PLAN-workflows.md` §Cost ceiling. **None of the three is one-call-per-case:** ECHO runs an ensemble (~6) + compression, Shapley samples Monte-Carlo coalitions (combinatorial), FALAT does search + a counterfactual re-run per candidate. Real calls are **~6–20×+ the case count**, so "a few dollars" holds only for single-pass methods. For **Shapley**, the coalition-sample count is a **fidelity parameter** pinned from the paper, not a knob to shrink for cost.
- The driver **measures calls/case at smoke** and extrapolates before pricing the full run. If the estimate breaches **$10/run**, scope **N** down (not the fidelity sample count) — noting this widens the CI; if N must drop so far the CI can't meaningfully exclude a plausible alternative, report **indeterminate (underpowered)**, not SUCCESS (§Convergence). **The model is fixed to the paper's headline backbone — N (the eval subset) is the only cost lever; never switch to a cheaper or different model (P8).** Smoke = ~5 cases.

## Per-paper instantiation checklist (mirrors `PLAN-factories.md` §4)
1. researcher: confirm the arXiv ID resolves + **confirm code absence** + quote the agent-level HC cell + GT-mode + denominator + **the exact headline backbone** from the primary paper + **resolve that headline backbone to a concrete OpenRouter model-ID** (an unavailable headline backbone is an instantiation blocker to surface — we do not substitute, P8); verify the shared Who&When clone is present; cache paper text → `paper/`.
2. `CLAUDE.md` from `CLAUDE-factory.md` — fill placeholders from the confirmed cell.
3. roles → `.claude/agents/`.  4. workflows → `workflows/`.  5. glue: perms, **wire the pipeline to the shared root `.env` (`../../.env`) — no per-paper `.env`**, empty `LEDGER.md`, `.gitignore`.

## Success rule
Per the canonical rule in `PLAN-workflows.md` §Convergence (central pass; weak/edge → light diagnose). Agent-level only. Not restated here.

## Order (canonical in `project-setup-draft.md` §7)
**FALAT → ECHO → Shapley.** FALAT pilot; Shapley last (highest-risk agent-level reimplementation — Monte-Carlo Shapley + PCI from prose).
