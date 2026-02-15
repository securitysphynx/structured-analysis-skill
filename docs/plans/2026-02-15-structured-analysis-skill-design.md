# Structured Analysis Skill — Design Document

> **Date**: 2026-02-15
> **Status**: Approved
> **Form Factor**: Claude Code Skill
> **Approach**: Orchestrator + Reference Architecture (Approach B)

---

## 1. Overview

A single Claude Code skill (`/analyze`) that conducts structured analytic techniques against any problem domain. The skill adapts to the problem, gathers evidence actively (including OSINT), executes established SAT protocols, self-corrects, and produces comprehensive templatized artifacts with mandatory citations.

### Requirements Summary

| Dimension | Decision |
|-----------|----------|
| **Form factor** | Claude Code skill |
| **Invocation** | `/analyze` with optional args |
| **Modes** | Adaptive orchestrator (default), direct technique, guided workflow, resume |
| **Techniques at launch** | 14: Essential 8 + Lean SATs + Frontier |
| **Domain** | Agnostic |
| **Input sources** | Conversation + file ingestion + OSINT (Firecrawl MCP primary, WebSearch/WebFetch fallback) |
| **Output** | Templatized markdown report + working artifacts + monitoring plan |
| **Self-correction** | 3-layer (protocol check, self-critique, human gate at end) |
| **Skill structure** | Single master skill with internal protocol files |

---

## 2. Skill Entry Point & Mode Routing

### Invocation Patterns

```
/analyze                          → Adaptive orchestrator (default)
/analyze <technique>              → Direct technique execution
/analyze --guided                 → Full step-by-step workflow
/analyze --resume <analysis-id>   → Resume or check monitoring plan
/analyze --lean                   → Abbreviated flow for time-pressured situations
/analyze --no-osint               → Disable web research for offline/sensitive work
```

### Adaptive Orchestrator (Default Mode)

1. Reads problem context from conversation history
2. Runs Selection Logic (prime Section 5 — 4-step decision flow):
   - Trigger check (is SAT warranted?)
   - Stage check (where in the process?)
   - Challenge check (12-question rubric)
   - Effort check (time budget)
3. Recommends 1-3 techniques with rationale, asks user to confirm or adjust
4. Executes selected technique(s) in sequence, producing artifacts at each stage
5. Self-critique, human review gate, final report + monitoring plan

### Direct Technique Mode

Skips selection logic. Executes specified technique protocol against current problem context. Still produces artifacts and report.

### Guided Workflow Mode

Complete SAT lifecycle, phase by phase:
- Phase 1: Framing (Customer Checklist + Issue Redefinition)
- Phase 2: Assumptions (KAC)
- Phase 3: Evidence gathering (OSINT + file ingestion)
- Phase 4: Core analysis (technique-appropriate)
- Phase 5: Stress testing (Premortem + What If?)
- Phase 6: Report + monitoring plan

User can exit or skip phases.

### Resume Mode

Reads previous analysis from `analyses/` directory. Continues where it left off or reviews monitoring plan indicators against new information via fresh OSINT.

---

## 3. Evidence Gathering Architecture

### Evidence Sources (3 tiers)

```
Tier 1: Conversation Context (immediate, free)
  └── Problem statement, prior discussion, user-provided facts

Tier 2: Local Files (fast, reliable)
  └── Documents, code, data files the user points to or that Glob discovers
  └── Previous analyses in analyses/ directory

Tier 3: OSINT Research (parallel, rich)
  └── Firecrawl MCP (primary, requires API key)
  └── WebSearch / WebFetch (fallback)
  └── Multiple queries spawned in parallel via Task agents
```

### Evidence Collection Flow

1. Extract key terms and entities from problem statement
2. Ask user if there are specific files or URLs to include
3. Spawn parallel Task agents for OSINT:
   - Agent 1: Background/context research
   - Agent 2: Contrarian/alternative perspectives
   - Agent 3: Recent developments
4. Simultaneously read any local files referenced
5. Synthesize into structured evidence registry

### Evidence Quality Assessment

Per 2009 Primer's Quality of Information Check:
- **Source reliability**: Track record, potential bias, access to information
- **Information credibility**: Internally consistent? Corroborated? Physically possible?
- **Diagnostic value**: Discriminates between hypotheses? (Law of Diagnostic Dominance — consistent with everything = zero value)

### Citation Rules

- Every claim in every artifact includes its source
- OSINT findings: `[Source Title](URL)` with retrieval date
- File-sourced evidence: `[filename:line_range]`
- User-provided facts: `[User-provided, session context]`
- Analytical judgments: `[Analyst assessment via <technique name>]`
- Firecrawl-sourced content: crawl timestamp and extraction method noted
- **No uncited claims. No exceptions.**

### Guard-Rails

- OSINT results always labeled with source URLs
- Never presents OSINT as fact — always "according to [source]"
- `--no-osint` flag disables web research
- Evidence registry saved as working artifact for auditability

---

## 4. Technique Execution Engine

### Execution Contract

Every technique follows a common structure:

```
1. SETUP     → Read protocol from protocol file, establish inputs needed
2. PRIME     → Present technique's purpose and expected output
3. EXECUTE   → Run technique steps against evidence + problem context
4. ARTIFACT  → Write working artifact from template
5. FINDINGS  → Extract key findings with confidence levels
6. HANDOFF   → Pass findings to next technique in chain
```

### The 14 Techniques at Launch

**Launch Phase**

| Technique | Protocol Source | Key Output Artifact |
|---|---|---|
| Customer Checklist | 09-core-techniques + 01-primer | `requirements.md` |
| Issue Redefinition | 09-core-techniques | `problem-framing.md` |
| Problem Restatement (Lean) | 04-agile-rigor | Appended to `problem-framing.md` |

**Exploration & Diagnostic Phase**

| Technique | Protocol Source | Key Output Artifact |
|---|---|---|
| Structured Brainstorming | 01-primer + 09-core | `brainstorm.md` |
| Key Assumptions Check | 01-primer + 02-analysis | `assumptions.md` |
| ACH | 01-primer + 02-analysis | `ach-matrix.md` |
| Inconsistencies Finder (Lean) | 04-agile-rigor | `inconsistencies.md` |
| Cross-Impact Matrix | 09-core-techniques | `cross-impact.md` |

**Challenge & Foresight Phase**

| Technique | Protocol Source | Key Output Artifact |
|---|---|---|
| What If? Analysis | 01-primer + 09-core | `what-if.md` |
| Premortem + Self-Critique | 09-core-techniques | `premortem.md` |
| Counterfactual Reasoning | 04-agile-rigor | `counterfactual.md` |
| Contrasting Narratives | 04-agile-rigor | `narratives.md` |

**Decision Support Phase**

| Technique | Protocol Source | Key Output Artifact |
|---|---|---|
| Bowtie Analysis | 04-agile-rigor | `bowtie.md` |
| Opportunities Incubator | 04-agile-rigor | `opportunities.md` |

### Protocol Loading

Protocols are read from markdown files at execution time. Updating a technique = editing the protocol file. Adding technique #15 = new `.md` file + orchestrator routing update.

### Technique Chaining

```
Customer Checklist → Issue Redefinition → KAC → [Selection Logic] → Core Technique(s) → Premortem → Report
         ↓                    ↓              ↓              ↓                    ↓
   requirements.md    problem-framing.md  assumptions.md  technique artifact  premortem.md
```

Each technique's output becomes input context for the next. Evidence registry is shared across all techniques.

---

## 5. Skill File Architecture

### Directory Layout

```
skills/
└── structured-analysis/
    ├── analyze.md                ← Master skill file (entry point)
    ├── protocols/
    │   ├── orchestrator.md       ← Selection logic, mode routing, workflow phases
    │   ├── evidence-collector.md ← Evidence gathering, OSINT patterns, citation rules
    │   ├── report-generator.md   ← Report synthesis, monitoring plan generation
    │   └── techniques/
    │       ├── customer-checklist.md
    │       ├── issue-redefinition.md
    │       ├── problem-restatement.md
    │       ├── structured-brainstorming.md
    │       ├── key-assumptions-check.md
    │       ├── ach.md
    │       ├── inconsistencies-finder.md
    │       ├── cross-impact-matrix.md
    │       ├── what-if.md
    │       ├── premortem.md
    │       ├── counterfactual-reasoning.md
    │       ├── contrasting-narratives.md
    │       ├── bowtie-analysis.md
    │       └── opportunities-incubator.md
    └── templates/
        ├── report-template.md
        ├── monitoring-plan-template.md
        ├── evidence-registry-template.md
        ├── meta-template.md
        ├── techniques/
        │   ├── customer-checklist.md
        │   ├── issue-redefinition.md
        │   ├── problem-restatement.md
        │   ├── brainstorm.md
        │   ├── assumptions.md
        │   ├── ach-matrix.md
        │   ├── inconsistencies.md
        │   ├── cross-impact.md
        │   ├── what-if.md
        │   ├── premortem.md
        │   ├── counterfactual.md
        │   ├── contrasting-narratives.md
        │   ├── bowtie.md
        │   └── opportunities.md
        └── sections/
            ├── header.md
            ├── citation-block.md
            ├── confidence-scale.md
            └── judgment-table.md
```

### Runtime Flow

```
/analyze [args]
  │
  ├── analyze.md loaded into context
  ├── Mode determined from args
  ├── orchestrator.md read for selection logic (if adaptive/guided)
  │
  ├── evidence-collector.md read when gathering phase begins
  │   └── Spawns parallel Task agents for OSINT
  │       ├── Firecrawl MCP (primary) → WebSearch/WebFetch (fallback)
  │       ├── Contrarian research agent
  │       └── File/local reading agent
  │
  ├── techniques/<selected>.md read for each technique execution
  │   └── templates/techniques/<selected>.md used for artifact structure
  │   └── Working artifact written to analyses/<id>/working/
  │
  ├── Self-correction layers execute (silent)
  │
  ├── Human review gate (single consolidated checkpoint)
  │
  ├── report-generator.md read for synthesis phase
  │   └── templates/ used as scaffolding
  │   └── Final report + monitoring plan written
  │
  └── Summary presented in conversation
```

### Key Design Decision: Protocols as Readable Markdown

Protocol files and templates are structured markdown, not code. This means:
- Updating a technique = editing a markdown file
- Adding technique #15 = new `.md` in protocols/techniques/ + orchestrator routing update
- Library docs (00-09) remain the knowledge base; protocols are distilled execution instructions
- No Python, no dependencies — pure prompt engineering

### Relationship: Library Docs → Protocol Files

```
docs/library/01-tradecraft-primer-2009.md     ← Deep reference (theory, history, context)
docs/library/02-tradecraft-primer-analysis.md  ← Expert guides (detailed how-to)
         ↓ distilled into ↓
skills/structured-analysis/protocols/techniques/ach.md  ← Execution protocol (do this now)
```

---

## 6. Template-Driven Artifact System

### Design Principles

1. Every template is fill-in-the-blank with `{{PLACEHOLDER}}` tokens
2. Section templates (`sections/`) are included by reference for consistency
3. Templates define minimum required sections — skill can add but never omit
4. Claude writes natural analytical prose within rigid structural framework

### Output Directory Structure

```
analyses/
└── 2026-02-15-<problem-slug>/
    ├── report.md                    ← Final structured report
    ├── monitoring-plan.md           ← Indicators/signposts tracking
    ├── evidence-registry.md         ← Source catalog with quality ratings
    ├── working/                     ← Intermediate technique artifacts
    │   ├── requirements.md
    │   ├── problem-framing.md
    │   ├── assumptions.md
    │   ├── ach-matrix.md
    │   └── ...
    └── meta.md                      ← Session metadata (enables resume)
```

### Shared Section Templates

**`sections/header.md`** — every artifact starts with:

```markdown
# {{TECHNIQUE_NAME}}: {{PROBLEM_TITLE}}
> **Analysis ID**: {{ANALYSIS_ID}}
> **Date**: {{DATE}}
> **Technique**: {{TECHNIQUE_FULL_NAME}}
> **Phase**: {{PHASE_NAME}}
> **Analyst**: User + Claude ({{MODE}} mode)
> **Evidence base**: {{EVIDENCE_COUNT}} items | [Full registry](../evidence-registry.md)
```

**`sections/confidence-scale.md`** — standardized per ICD 203:

| Level | Meaning |
|-------|---------|
| **High** | Well-corroborated by multiple independent sources; key assumptions well-supported |
| **Moderate** | Plausibly supported but with notable gaps; some assumptions unverified |
| **Low** | Fragmented evidence; significant assumptions unsupported; alternative explanations viable |

**`sections/citation-block.md`** — mandatory footer:

```markdown
## Sources & Citations
| Ref | Source | Retrieved | Reliability | Method |
|-----|--------|-----------|-------------|--------|
| [1] | {{FULL_CITATION_WITH_URL}} | {{DATE}} | {{HIGH/MED/LOW}} | {{OSINT/FILE/USER/ANALYSIS}} |

*All OSINT sources retrieved via {{Firecrawl MCP / WebSearch fallback}}.*
*All analytical judgments produced via {{TECHNIQUE_NAME}} protocol.*
```

### Key Technique Templates

**ACH Matrix** (`templates/techniques/ach-matrix.md`):
- Problem statement
- Hypotheses table (ID, description, source/basis)
- Evidence table (ID, evidence, source, reliability, citation)
- Diagnosticity matrix (hypotheses x evidence, C/I/N ratings, diagnostic value per item)
- Inconsistency tally (per hypothesis, ranked)
- Sensitivity analysis (critical evidence items, reinterpretation impact)
- Preliminary findings (most plausible hypothesis, confidence, rationale)
- Citation block

**Key Assumptions Check** (`templates/techniques/assumptions.md`):
- Current analytic line
- Assumption inventory (assumption, stated/unstated, category, S/C/U bin, linchpin flag)
- Linchpin analysis (justification, failure conditions, supporting/challenging evidence, impact)
- Fault lines summary (priority, risk, recommended action)
- Citation block

**Final Report** (`templates/report-template.md`):
- Executive summary
- Problem framing
- Evidence base summary (link to full registry)
- Analysis sections per technique (purpose, process, findings, confidence, link to working artifact)
- Synthesis (cross-technique agreement/disagreement, integrated assessment)
- Key judgments table (judgment, confidence, supporting techniques, critical assumptions)
- Risks & blind spots (from self-critique)
- Monitoring plan summary (link to full plan)
- Complete sources bibliography

**Monitoring Plan** (`templates/monitoring-plan-template.md`):
- Key judgments under watch
- Confirming indicators table (indicator, what to look for, source to check, status)
- Disconfirming indicators table (same structure)
- Trigger thresholds (how many disconfirming indicators warrant re-analysis)
- Review log (date, reviewer, indicators updated, action taken)

### How Templates Get Filled

Templates serve as structural contracts. The skill:
1. Reads template to understand required sections and format
2. Performs analysis following technique protocol
3. Writes artifact following template structure with actual analytical content
4. Ensures no required section is empty — notes why if a section can't be filled

---

## 7. Self-Corrective Mechanisms

### Three Layers

**Layer 1: Protocol Compliance Check (after every technique, silent)**

Verifies against technique output:
- All required template sections present and substantive
- Citations present throughout
- Protocol "watch-outs" addressed
- For diagnostic techniques:
  - At least 3 hypotheses considered
  - Null hypothesis included
  - Deception/denial considered
  - Inconsistent evidence exists (if nothing contradicts → blind spot flag)
  - Zero-diagnostic-value evidence identified (Diagnostic Dominance check)

Self-corrects inline or flags in artifact. No user interruption.

**Layer 2: Analytical Self-Critique (before report synthesis, silent)**

Mini-Premortem on the analysis itself:
1. **Assumption audit**: Unstated premises in findings?
2. **Evidence balance**: Count evidence items per hypothesis. Flag >2:1 imbalance.
3. **Confidence calibration**: Bipolar Bias guard — over-correcting toward uncertainty, or under-challenged?
4. **Alternative check**: Strongest 2-3 sentence case against top finding
5. **Missing voices**: Domains where no evidence was gathered

Results written into report's Risks & Blind Spots section.

**Layer 3: Human Review Gate (single checkpoint, after synthesis)**

Consolidated review before report finalization:

```
Analysis complete. Before I finalize:

📋 Problem framing: [summary]
📊 Evidence: [count] items ([breakdown by source type])
   ⚠️ Gaps: [identified gaps]
🔍 Key finding: [top-line result with confidence]
⚠️ Self-critique flags:
   - [flag 1]
   - [flag 2]
   - [flag 3]

What would you adjust before I finalize?
```

No intermediate interruptions. The agentic workflow runs unbroken.

### Rigor Scales with Mode

| Mode | Layer 1 | Layer 2 | Layer 3 |
|------|---------|---------|---------|
| Direct technique | Protocol check | Skipped | Brief summary |
| Adaptive (default) | Full | Full | Full review gate |
| Guided | Full | Full + detailed | Full review gate |
| Lean (`--lean`) | Abbreviated | Skipped | Brief summary |

### Deliberately Excluded

- Multi-agent debate (future enhancement — powerful but expensive)
- Automated re-runs on self-critique failure (risk of infinite loops)
- Numeric scoring thresholds (false precision — self-critique is qualitative)

---

## 8. Expansion Model

### Adding New Techniques

1. Create `protocols/techniques/<new-technique>.md` (execution protocol)
2. Create `templates/techniques/<new-technique>.md` (artifact template)
3. Update orchestrator's routing table and 12-question rubric mapping
4. Optionally update library docs if new reference material exists

### Future Enhancements (not in v1)

- Multi-agent debate mode for high-stakes analysis
- Integration with structured data sources (databases, APIs)
- Collaborative analysis (multiple human analysts + skill)
- Analysis comparison (diff two analyses of the same problem)
- Confidence tracking over time (how judgments evolved across sessions)

---

## 9. End-to-End Example

**User invokes**: `/analyze` during conversation about SE Asian market entry.

1. **Mode detection**: No args → Adaptive orchestrator
2. **Context read**: Conversation mentions market size, competitors, regulatory concerns
3. **Selection logic**: High consequence + persistent uncertainty → SAT warranted. Recommends: Issue Redefinition → KAC → Evidence Gathering → ACH → What If? → Opportunities Incubator → Premortem
4. **Issue Redefinition**: 3+ reframings → writes `working/problem-framing.md`
5. **KAC**: Surfaces assumptions, bins S/C/U, flags linchpins → writes `working/assumptions.md`
6. **Evidence gathering**: 3 parallel OSINT agents (Firecrawl primary) + local file reads → writes `evidence-registry.md`
7. **ACH**: 5 hypotheses × evidence matrix, diagnosticity scoring → writes `working/ach-matrix.md`. Layer 1 checks pass.
8. **What If?**: Assumes catastrophic failure, works backward → writes `working/what-if.md`
9. **Opportunities Incubator**: Scans for converging positive trends → writes `working/opportunities.md`
10. **Premortem**: Imagines analysis is wrong, inventories weaknesses → writes `working/premortem.md`
11. **Layer 2 self-critique**: Silent. Flags evidence imbalance and unsupported linchpin assumption. Writes into Risks & Blind Spots.
12. **Human review gate**: Presents consolidated summary with self-critique flags. User provides feedback.
13. **Report generation**: Incorporates feedback, writes `report.md` + `monitoring-plan.md` + `meta.md`. Presents summary in conversation.

**Total output**: 1 report, 1 monitoring plan, 1 evidence registry, 6-7 working artifacts, 1 metadata file — all with full citations.
