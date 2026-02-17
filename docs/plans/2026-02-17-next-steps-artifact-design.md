# Design: `next-steps.md` — Standalone Iteration Suggestions Artifact

**Date**: 2026-02-17
**Status**: Approved

## Problem

Iteration suggestions are currently buried inside `working/review-summary.md` and scattered across `meta.md`. Users have no single, obvious file to check for "what should I iterate on?" and the `--iterate` handler has to cobble together information from multiple sources. This makes it harder for users to review gaps and harder for the skill to pick up iteration tasks.

## Solution

A new top-level analysis artifact at `analyses/<id>/next-steps.md` that:

1. Consolidates all actionable iteration suggestions into one machine-parseable, user-readable file
2. Serves as the primary input for the `--iterate` handler (replacing meta.md-based lookup)
3. Tracks resolution status across iterations, creating a full audit trail
4. Shows auto-remediated items so users see the complete picture

## File Location

`analyses/<id>/next-steps.md` — analysis root, alongside `report.md` and `meta.md`.

## Template Structure

```markdown
# Next Steps: {{ANALYSIS_ID}}

Generated: {{DATE}} | Iteration: {{N}} | Quality Score: {{SCORE}}/5.0

## Summary
{{TOTAL_COUNT}} items identified. {{REMEDIATED_COUNT}} auto-remediated. {{RESOLVED_COUNT}} resolved. {{OPEN_COUNT}} open.

## Iteration Items

| # | Flag Source | Flag Type | Severity | Status | Technique(s) | Evidence Focus |
|---|-----------|-----------|----------|--------|-------------|----------------|
| 1 | Layer 2 - 3a | Unstated premises | HIGH | REMEDIATED | kac | ... |
| 2 | Layer 2 - 3b | Evidence imbalance | HIGH | OPEN | ach | ... |
| 3 | Layer 2 - 3e | Missing perspectives | MEDIUM | OPEN | narratives | ... |
| 4 | Sufficiency | Low source diversity | LOW | OPEN | (evidence only) | ... |

## Detail

### 1. [REMEDIATED] Unstated premises (Layer 2 - 3a) — HIGH
{{DESCRIPTION}}
- **Technique**: kac
- **Evidence focus**: {{FOCUS}}
- **Remediation note**: Auto-remediated in cycle 1. See working/kac.remediation-prior.md

### 2. [OPEN] Evidence imbalance (Layer 2 - 3b) — HIGH
{{DESCRIPTION}}
- **Technique**: ach
- **Evidence focus**: {{FOCUS}}

### 3. [RESOLVED — Iter 2] Missing perspectives (Layer 2 - 3e) — MEDIUM
{{DESCRIPTION}}
- **Technique**: narratives
- **Evidence focus**: {{FOCUS}}
- **Resolved**: Iteration 2 ({{DATE}}). {{NEW_EVIDENCE_COUNT}} new evidence items ({{ID_RANGE}}). {{ONE_LINE_SUMMARY}}.

## Suggested Command
`/analyze --iterate {{ANALYSIS_ID}} {{OPEN_TECHNIQUE_LIST}}`
<!-- Only includes techniques for OPEN items -->
```

## Status States

| Status | Meaning |
|--------|---------|
| `OPEN` | Identified, not yet addressed |
| `REMEDIATED` | Auto-remediated by the remediation gate in the same run |
| `RESOLVED` | Addressed by a subsequent `--iterate` run |
| `DEFERRED` | User explicitly chose not to address |

## Protocol Changes

### 1. `report-generator.md` — Phase A writes next-steps.md

Step 7 adds a new output: write `next-steps.md` to analysis root using the template above. Data source is the same flag collection already produced for review-summary.md (Steps 3a-3h + Sufficiency Gate flags). All items written with status `OPEN`.

The iteration suggestions section in `review-summary.md` is simplified to: "See next-steps.md for iteration details."

### 2. `orchestrator.md` — Auto-remediation gate updates status

After the auto-remediation gate acts on HIGH flags:
- Read `next-steps.md`
- Update status from `OPEN` → `REMEDIATED` for each HIGH flag that was acted on
- Append a remediation note to the detail section
- Update the summary counts

Phase B presenter references `next-steps.md` when telling the user about remaining iterations.

### 3. `iteration-handler.md` — Reads and updates next-steps.md

**Input change (Step 1)**: Read `next-steps.md` instead of meta.md to determine which techniques to re-run. Filter to `OPEN` items only.

**New step after Step 5 (cross-iteration synthesis)**:
1. Read `next-steps.md`
2. For each technique re-run in this iteration, find matching `OPEN` items → update to `RESOLVED — Iter {{N}}`
3. Append resolution note: iteration number, date, new evidence count/range, one-line summary
4. If Layer 2 self-critique in the new iteration produces new flags, append as new `OPEN` items at the bottom
5. Update summary counts
6. Update suggested command to reflect remaining `OPEN` items only

### 4. New template: `templates/next-steps-template.md`

Contains the template structure above with placeholder variables.

## What Doesn't Change

- Layer 2 self-critique checks (Steps 3a-3h)
- Flag-to-technique mapping table (Step 7b)
- meta.md — still captures iteration history audit log
- review-summary.md — still captures quality score and Layer 2 flags for Phase B, but delegates iteration details to next-steps.md
- Tier Gate Protocol — unchanged
- Evidence collection — unchanged
