# Evidence Collector Protocol

Gather, rate, and organize evidence across three tiers. Every item must be cited. Uncited claims are not permitted.

---

## Evidence Tiers

Collect in order. Each tier adds breadth.

### Tier 1: Conversation Context
- Extract claims, data, constraints, and assumptions from conversation history
- Tag each item: `[User-provided, session context]`

### Tier 2: Local Files
- Read files the user referenced or attached
- Use Glob to discover relevant files in the working directory
- Tag each item: `[filename:line_range]`

### Tier 3: OSINT (skip if `--no-osint` flag is set)
- Spawn **3 parallel Task agents** using the Task tool:

**Agent 1 — Background & Context:**
> Research background and context for: {{PROBLEM_SUMMARY}}. Search for authoritative sources, data, expert analysis. Use Firecrawl MCP if available, otherwise WebSearch and WebFetch. Return findings with full source URLs and retrieval dates.

**Agent 2 — Contrarian & Critical:**
> Research contrarian and critical perspectives on: {{PROBLEM_SUMMARY}}. Specifically look for: counterarguments, failures, risks, critics' views, alternative explanations. Use Firecrawl MCP if available, otherwise WebSearch and WebFetch. Return findings with full source URLs and retrieval dates.

**Agent 3 — Recent Developments:**
> Research recent developments (last 6 months) related to: {{PROBLEM_SUMMARY}}. Look for: new data, policy changes, market shifts, emerging trends, breaking news. Use Firecrawl MCP if available, otherwise WebSearch and WebFetch. Return findings with full source URLs and retrieval dates.

### Firecrawl MCP Integration
- Check if Firecrawl MCP server is available in the current session
- If available: Use Firecrawl for deep page scraping (full article content, JavaScript-rendered pages)
- If not available: Fall back to WebSearch for discovery + WebFetch for content extraction
- Always record which method was used in the evidence registry

---

## Evidence Quality Rating

Rate every evidence item on three dimensions (per 2009 Primer Quality of Information Check):

**Source Reliability:**
- **High**: Established source with verified track record
- **Medium**: Known source but independently unverified
- **Low**: Unknown source or potential bias identified

**Information Credibility:**
- **High**: Corroborated by independent sources, internally consistent
- **Medium**: Plausible and partially confirmed
- **Low**: Uncorroborated, contradictory, or single-source

**Diagnostic Value:**
- **High**: Contradicts one or more specific hypotheses
- **Medium**: Supports some hypotheses, neutral for others
- **Low**: Consistent with most hypotheses
- **Zero**: Consistent with ALL hypotheses — explicitly flag per Law of Diagnostic Dominance

---

## Citation Format

Every evidence item MUST include:

| Field | Format |
|-------|--------|
| OSINT | `[Source Title](URL)` — Retrieved: YYYY-MM-DD |
| File | `[filename:line_range]` |
| User | `[User-provided, session context]` |
| Analysis | `[Derived via {{TECHNIQUE_NAME}}]` |

**Rules:**
- OSINT is NEVER presented as fact — always "according to [source]"
- Every claim in every artifact must trace to at least one evidence item
- If `--no-osint` is set, note in registry: "OSINT collection disabled by analyst"

---

## Output

Write the completed evidence registry to `analyses/{{ANALYSIS_ID}}/evidence-registry.md` using the evidence registry template.

Report to the orchestrator:
- Total evidence items collected per tier
- Evidence gaps identified (domains with no coverage)
- Diagnostic value distribution (how many High vs Zero items)
