# 02 — Methodology & Provenance

How this research was produced, how to read the raw-data files, what the numbers mean, and how to reproduce or extend the run.

---

## The pipeline (5 phases, 100 agents)

The `deep-research` workflow is a deterministic fan-out/verify/synthesize harness. Phases and the exact constants used this run:

```
Scope  →  Search  →  (URL-dedup)  →  Fetch+Extract  →  Verify (3-vote)  →  Synthesize
  1         5                            18                75                   1        = 100 agents
```

| Constant | Value | Meaning |
|---|---|---|
| `VOTES_PER_CLAIM` | 3 | independent skeptical verifiers per claim |
| `REFUTATIONS_REQUIRED` | 2 | ≥2 refutes kills a claim |
| `MAX_FETCH` | 15 | soft fetch budget (18 fetched: high-relevance sources bypass the cap) |
| `MAX_VERIFY_CLAIMS` | 25 | only the top-25 ranked claims get verified |

**Phase detail:**

1. **Scope** (1 agent) — decomposed the question into **5 complementary search angles**: (a) broad/primary canonical methods & benchmarks, (b) academic/technical causal & counterfactual blame assignment, (c) terminology disambiguation (node vs span vs module vs step), (d) practitioner/actionable targeted node repair, (e) recent-frontier 2025 sweep. The scope agent also ran `Glob`/`Bash` and noted the working dir was empty (pure literature task). Output: `_data/01-scope.json`.

2. **Search** (5 agents, one per angle) — each ran `WebSearch` and returned its top 4–6 results ranked by relevance to the *original* question. **30 results total.** Output: `_data/02-search.json` → rendered in `03-SOURCES-all.md`.

3. **URL-dedup + Fetch budget** (in-pipeline, no agent) — results were sorted by relevance; duplicates across angles were dropped (**11 dupes**); once the 15-source budget filled, remaining medium/low-relevance uniques were dropped (**1 budget-dropped**: AgentSHAP 2512.12597). **18 unique sources** passed to fetch.

4. **Fetch+Extract** (18 agents) — each ran `WebFetch`, assessed source quality (all 18 came back **primary**), and extracted 2–5 falsifiable claims, each with a **direct quote** and an importance rating (central/supporting/tangential). **88 claims total.** Output: `_data/03-fetch.json` → `04-CLAIMS-all-extracted.md`.

5. **Verify** (75 agents = 25 claims × 3) — claims were ranked by `importance` then `sourceQuality`; the **top 25** were each handed to 3 adversarial voters told to *try to refute* (default to refuted if uncertain). Output: `_data/04-verify.json` → `05-VERIFICATION-votes.md`.

6. **Synthesize** (1 agent) — merged semantic duplicates among the 24 survivors, grouped them into **16 findings**, assigned per-finding confidence, and wrote the executive summary, caveats, and open questions. Output: `_data/05-synthesize.json`; the full assembled result (findings + refuted + sources + stats) is `_data/final-result.json`.

---

## What "confirmed / killed" means

- A claim **survives** iff ≥2 of 3 valid votes do **not** refute it (and a quorum actually voted). 
- **24 confirmed, 1 killed.** The kill: a DoVer repair-rate claim (0-3) — see `06` for why (internally contradictory parenthetical, not a fabricated result).
- **2-1 votes still confirm** but flag a genuine dissent. Four claims were 2-1 (AgenTracer-causal framing, "far-from-solved" recency, and two A2P claims on AI-authorship). The dissent evidence is preserved verbatim in `05-VERIFICATION-votes.md`.
- **Absence of verification ≠ refutation.** 63 of 88 claims were never voted on — they ranked below the top-25 budget. Several strong methods (AgentDebug, AgentRx, CHIEF, MAST, TRAIL) live entirely in this unverified tail; their numbers are single-source here.

---

## How the raw-data files were extracted

The workflow stores each subagent as a `.jsonl` transcript under:

```
…\93f04230-…\subagents\workflows\wf_56d0a8ee-1fa\agent-*.jsonl   (+ .meta.json)
```

The `.meta.json` files carry no label (`{"agentType":"workflow-subagent"}`), so each agent's **role** was recovered from its prompt (line 0): `"Decompose this research question"` → scope; `"## Web Searcher:"` → search; `"## Source Extractor"` → fetch; `"## Adversarial Claim Verifier (voter N/3)"` → verify; `"## Synthesis: research report"` → synthesize. Each agent's **return value** is the `input` of its final `StructuredOutput` tool call. A PowerShell pass parsed all 101 `.jsonl` (100 agents + 1 `journal`), classified by role, extracted the structured outputs, and wrote the per-role JSON in `_data/`. The three raw-data markdown files (`03`/`04`/`05`) were then generated directly from that JSON for fidelity (no manual transcription).

**Encoding note:** transcripts are UTF-8; they were read with `-Encoding UTF8`, so em-dashes/ampersands render correctly. JSON keeps `&` for `&` (normal escaping).

**One extraction caveat:** an interim PowerShell *preview* mis-displayed the four 2-1 tallies as "3-" due to the Windows-PowerShell-5.1 quirk where `.Count` on a single (non-array) object returns `$null`. The committed files compute tallies with `@(...)`-wrapped arrays and are correct; the JSON in `_data/` is the source of truth.

---

## Limitations of this research method

- **Single verification batch.** Only 25 of 88 claims were adversarially checked. The ranking heuristic (importance→quality) favored claims the *fetch* agent already rated "central," which can entrench a source's own framing.
- **Verifiers are LLMs doing web re-checks**, not domain experts. They catch transcription errors, contradictions, and source-quality mismatches well (as the DoVer kill and A2P dissents show), but a 3-0 "confirmed" means "not refuted by three skeptics," not "independently replicated."
- **Recency vs knowledge cutoff.** The run executed live web search in June 2026 and legitimately surfaced 2026 papers (CHIEF 2602.23701, CausalFlow 2605.25338, AgentRx, AgentFixer 2603.29848) that postdate the assistant's training cutoff — these could not be cross-checked against later literature.
- **No head-to-head.** All accuracy deltas are self-reported on differing subsets/settings; the workflow did not run any benchmark itself.

---

## Run metadata & reproduction

| | |
|---|---|
| Workflow | `deep-research` |
| Run ID | `wf_56d0a8ee-1fa` |
| Task ID | `w48ktoy05` |
| Script | `…\93f04230-…\workflows\scripts\deep-research-wf_56d0a8ee-1fa.js` |
| Transcripts | `…\93f04230-…\subagents\workflows\wf_56d0a8ee-1fa\` |
| Agents / tokens / tools / time | 100 / ~3,403,275 / 944 / ~1,388,099 ms |

- **Resume / re-run:** `Workflow({ scriptPath: "…deep-research-wf_56d0a8ee-1fa.js", resumeFromRunId: "wf_56d0a8ee-1fa" })` — unchanged `agent()` calls return cached results; only edited/new calls re-run.
- **Re-extract the data files:** re-run the PowerShell parse over the transcript dir (parse role from prompt line-0, pull final `StructuredOutput.input`).
- **`_data/` manifest:** `01-scope.json` · `02-search.json` · `03-fetch.json` · `04-verify.json` · `05-synthesize.json` · `final-result.json`.
