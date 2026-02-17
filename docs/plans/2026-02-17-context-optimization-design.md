# Context Optimization & Reliability Improvements Design

> **Date**: 2026-02-17
> **Branch**: `context-optimization`
> **Status**: Approved

---

## Problem Statement

Five issues identified from production use of the structured-analysis skill:

1. **Context compaction risk** — Too much work happens in main agent context (report synthesis reads ~150KB of artifacts back in), risking degraded performance from context compaction
2. **Sequential OSINT** — Evidence collection runs foreground subagents sequentially (one per theme), making 6-10 theme collection unnecessarily slow
3. **Technique cap mismatch** — The 14-question rubric regularly selects 6 techniques but the soft cap is 5, creating artificial friction
4. **Iteration doesn't revise report** — Cross-iteration synthesis output has nowhere to go; the report generator doesn't know it's in iteration mode
5. **Self-critique findings always deferred** — Layer 2 flags always map to post-analysis iteration suggestions, even when they could be caught and addressed during the initial run

---

## Design

### 1. Report Synthesis Subagent (Context Offloading)

Split the report generator into two phases:

**Phase A — Synthesis Subagent** (foreground Task subagent):
- Reads all `working/` artifacts, `evidence-registry.md`, and `meta.md` from disk
- Executes report-generator Steps 1-3 (gather inputs, cross-technique synthesis, Layer 2 self-critique)
- Reads and fills `templates/report-template.md`
- Writes three files:
  1. `report.md` — full draft report
  2. `monitoring-plan.md` — indicators and signposts
  3. `working/review-summary.md` — compact file (~500 tokens) containing: reframed problem, evidence count/breakdown, key finding + confidence, quality score, all Layer 2 flags, and iteration suggestions

**Phase B — Human Review & Finalization** (main context):
- Reads only `working/review-summary.md`
- Presents the human review gate (Step 4)
- If user has feedback: spawns a second subagent that reads feedback + current `report.md`, applies edits, rewrites
- If no feedback: proceeds to Step 6 (present results) and Step 7 (iteration suggestions) using summary data

**Subagent prompt template**:
```
You are a structured analysis report synthesizer. Your task:

1. Read all working artifacts from analyses/{{ANALYSIS_ID}}/working/
2. Read evidence-registry.md and meta.md
3. Read the report-generator protocol at {{SKILL_DIR}}/protocols/report-generator.md
4. Follow Steps 1-3 of the protocol exactly
5. Read and fill {{SKILL_DIR}}/templates/report-template.md
6. Write: report.md, monitoring-plan.md, working/review-summary.md

The review-summary.md must contain ONLY:
- Problem (reframed)
- Evidence count + tier breakdown
- Key finding + confidence
- Quality score + pass/advisory/fail
- All Layer 2 flags (numbered list)
- Iteration suggestions (mapped from flags per Step 7 rules)

{{ITERATION_CONTEXT_IF_APPLICABLE}}

Return ONLY: "Report written. Quality score: X/5.0 (STATUS)"
```

**Impact**: Removes ~150KB of artifact content from main context. Main context only sees ~500-token review summary.

---

### 2. Parallel OSINT Collection

Replace sequential foreground subagent dispatch with parallel batches.

**New flow**:
```
Batch 1: [Theme 1, Theme 2, Theme 3] → wait for all (core themes)
Batch 2: [Theme 4, Theme 5, ...] → wait for all (adaptive themes)
```

**Protocol changes to `evidence-collector.md`**:
- Replace "Spawn foreground Task subagents sequentially" with parallel batch dispatch
- Core themes (1-3) as Batch 1, adaptive themes (4+) as Batch 2
- Add prerequisite: "Parallel dispatch requires search/scrape tools in the allow list. If tools require per-call approval, fall back to sequential dispatch to avoid permission prompt flooding."

**Rationale for two batches**: Core themes have priority — if adaptive themes fail, the foundation exists. Keeps Firecrawl API load reasonable (3 concurrent, then 5-7 concurrent).

**Fallback**: Failed theme subagents don't block siblings. Failures logged for sufficiency gate retry cycle.

---

### 3. Technique Cap 5 → 6

In orchestrator.md Step 4, Default Mode:
- Change "max 5 techniques" → "max 6 techniques"
- Update overflow message threshold: "Recommending [6 prioritized]"

One-line change. The 14-question rubric naturally selects 6 for moderately complex problems.

---

### 4. Iteration-Aware Report Regeneration

Add explicit iteration context passing between orchestrator → report generator.

After cross-iteration synthesis produces the delta summary:

1. Archive prior report: `report.md` → `report.v{N}.md`
2. Archive prior monitoring plan: `monitoring-plan.md` → `monitoring-plan.v{N}.md`
3. Write `working/iteration-context.md` containing:
   - Iteration number
   - Delta summary (changes, stabilized findings, confidence shifts)
   - Trigger detail
   - Prior report version path
4. Dispatch report synthesis subagent with additional iteration instructions:
   - Populate Revision History section
   - Note judgment changes from prior iteration
   - Use `[PRIOR-v{N}: technique_name]` citations
   - Read prior report for comparison

For scoped iterations: same flow but report subagent only updates affected sections.

**What this fixes**: Cross-iteration synthesis output (iteration-handler Step 5) currently computes delta and then loses it. Now it's persisted in `iteration-context.md` and consumed by the report subagent.

---

### 5. Inter-Tier Feedback (Tier Gates)

Add a lightweight check after each subagent tier completes, before dispatching the next tier. Runs in main context using only compact findings summaries (~200-400 tokens each).

**Tier Gate checks**:

| Signal | Detection | Action |
|--------|-----------|--------|
| MISSING HYPOTHESIS | ACH/KAC flagged gap in hypothesis coverage | Add SETUP context to downstream technique with the missing hypothesis |
| EVIDENCE GAP | Technique flagged critical missing evidence | Spawn targeted 1-2 theme mini-collection before next tier |
| CONTRADICTION | Two same-tier techniques produced conflicting findings | Add SETUP context to downstream techniques noting the tension |
| CONFIDENCE COLLAPSE | Technique returned all Low confidence findings | Log Layer 1 flag, warn downstream techniques |

**Rules**:
- Additive only — inject context or add mini-collection, never remove/skip techniques
- Max 1 targeted evidence collection per gate
- No disk reads — only compact summaries already in main context
- All actions logged in `meta.md` under "Tier Gate Actions"
- Silent pass if no issues detected

**Impact on Layer 2**: Layer 2 still runs in report synthesis subagent but finds fewer actionable issues. Step 7 iteration suggestions become residual (things requiring a full re-run).

---

## Files Modified

| File | Changes |
|------|---------|
| `protocols/orchestrator.md` | Technique cap 5→6, tier gate protocol, iteration report dispatch, report synthesis subagent dispatch |
| `protocols/evidence-collector.md` | Parallel batch dispatch for Step 3a |
| `protocols/report-generator.md` | Split into Phase A (subagent) and Phase B (main context), add review-summary.md output |
| `protocols/iteration-handler.md` | Write iteration-context.md, archive prior report/monitoring-plan |
| `templates/review-summary-template.md` | New template for compact review summary |

---

## Risk Assessment

- **Report subagent quality**: The synthesis subagent must produce the same quality as in-context synthesis. Mitigated by: it follows the exact same protocol, has a fresh full-size context window, and reads complete artifacts from disk.
- **Tier gate complexity**: Adding inter-tier logic increases orchestrator complexity. Mitigated by: gates are additive-only, lightweight, and logged.
- **Parallel OSINT rate limits**: Concurrent Firecrawl calls might hit rate limits. Mitigated by: two-batch approach limits concurrency to 3 then 5-7.
