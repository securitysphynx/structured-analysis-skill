# Review Summary: {{ANALYSIS_ID}}

## Problem
{{REFRAMED_PROBLEM}}

## Evidence
- **Total items**: {{EVIDENCE_COUNT}}
- **Tier breakdown**: Tier 1: {{T1_COUNT}} | Tier 2: {{T2_COUNT}} | Tier 3: {{T3_COUNT}}
- **Evidence gaps**: {{GAPS_OR_NONE}}

## Key Finding
{{TOP_FINDING}}
- **Confidence**: {{CONFIDENCE_LEVEL}}
- **Supporting techniques**: {{TECHNIQUE_LIST}}

## Quality Score
- **Score**: {{SCORE}}/5.0
- **Result**: {{PASS_ADVISORY_FAIL}}
- **Lowest criterion**: {{LOWEST_CRITERION_NAME}} ({{LOWEST_SCORE}}/5)

## Layer 2 Flags
{{#EACH FLAG}}
{{N}}. **{{FLAG_SOURCE}}**: {{FLAG_DESCRIPTION}}
{{/EACH}}

## Iteration Suggestions
{{#IF HAS_ACTIONABLE_FLAGS}}
Self-critique identified {{FLAG_COUNT}} actionable items:

{{#EACH SUGGESTION}}
{{N}}. **{{FLAG_TYPE}}**: {{FLAG_DESCRIPTION}}
   → Re-run: {{TECHNIQUES}} | Evidence focus: {{FOCUS}}
{{/EACH}}

Suggested command: `/analyze --iterate {{ANALYSIS_ID}} {{TECHNIQUE_LIST}}`
{{/IF}}
{{#IF NO_ACTIONABLE_FLAGS}}
No actionable iteration suggestions. All self-critique checks passed or produced only in-report adjustments.
{{/IF}}
