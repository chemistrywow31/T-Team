---
name: Worklog
description: Mandatory phase-level documentation in .worklog/ for verifiability, traceability, and continuity of all work
---

# Worklog

## Applicability

- Applies to: All agents (every agent must read from and write to the worklog during task execution)

## Rule Content

### Every Task Must Have a Worklog

Every task executed by the team must maintain a worklog under the `.worklog/` directory. The worklog records reference information, key findings, and decision rationale for each phase, ensuring all work is verifiable, traceable, and continuable.

### Directory Structure and Naming

```
.worklog/{yyyymm}/{task-name}/phase-{n}-{label}/
  ├── references.md    ├── findings.md    ├── decisions.md    └── audit.md (optional)
```

- `{yyyymm}`: Year-month of task start (e.g., `202603`)
- `{task-name}`: kebab-case (e.g., `english-teaching-team`)
- `phase-{n}-{label}`: numbered sequentially, kebab-case label (e.g., `phase-1-discovery`)

### Required Files Per Phase

Each phase folder must contain three core files:

#### references.md

Record all information sources consulted during this phase:
- URLs to documentation, articles, or tools evaluated
- File paths to internal documents referenced
- Conversation segments or user statements that informed decisions
- Domain Researcher reports (when investigation was conducted)

Each reference must include: source identifier, brief description of content, and how it was used.

#### findings.md

Record key discoveries and analysis derived from the references:
- Facts established from research
- Patterns identified across multiple sources
- Constraints or limitations discovered
- Comparative analysis results (e.g., tool A vs tool B)

Each finding must trace back to at least one entry in `references.md`.

#### decisions.md

Record every decision made during this phase:
- The decision statement (what was decided)
- The rationale (why this option was chosen)
- Alternatives considered and why they were rejected
- Supporting evidence (references to `findings.md` and `references.md`)
- Impact assessment (what this decision affects downstream)

Each decision must be defensible — a reader who disagrees must be able to identify which specific evidence or reasoning to challenge.

### Evidence Chain Requirement

The three files form an evidence chain: **references → findings → decisions**. Every decision must trace back through findings to references. A decision with no traceable evidence chain is a violation.

When no external reference exists for a decision, `references.md` must explicitly state: "No established reference found for {topic}." The corresponding `decisions.md` entry must document the first-principles reasoning used instead.

### Worklog Creation Timing

- The coordinator creates the worklog directory structure at the start of each task, before dispatching any phase work
- Each agent writes to the worklog during and at the end of their phase work
- The coordinator verifies worklog completeness at each phase boundary before proceeding

### Worklog as Context Offloading

Once information is written to the worklog, agents do not need to retain it in context. The worklog serves as persistent external memory:
- Downstream agents read upstream phase worklogs instead of receiving full context in the task dispatch
- Phase completion summaries reference worklog paths, not inline content
- When resuming interrupted work, the worklog provides full context for continuation

### Required Phase Artifacts (T-Team Specific)

Beyond the three core files, T-Team consultations produce the following structured artifacts at specific phase folders. These artifacts make the evidence chain auditable.

| Artifact | Phase folder | Required when |
|----------|--------------|---------------|
| `source-registry.yaml` | `phase-2-analysis/` | Always — every Phase 2 onwards |
| `evidence-dossier.md` | `phase-2-analysis/` | Level 4 consultations only |
| `debate-summary.md` | `phase-2-analysis/` | Phase 2.5 fired (per coordinator triggers) |
| `adr-{NNN}.md` | `phase-5-spec/adrs/` | Each architecture decision in spec |
| `c4-{level}-{name}.md` | `phase-5-spec/diagrams/` | Each C4 diagram in spec |

#### `source-registry.yaml` template

```yaml
sources:
  - source_id: SRC-001
    url: https://example.com/article
    author: "Author Name"
    publication: "Publication or Vendor"
    date: 2026-04-15
    field_classification: rapid_change   # rapid_change | evolving | stable
    age_at_access: 11                     # days
    credibility_score: 4.2
    key_claims:
      - "LangGraph achieves 4.2s p99 latency"
      - "47M+ monthly downloads"
    unverified_flags: []
recency_summary:
  field_classification: rapid_change
  pct_within_6_months: 60
  pct_within_12_months: 86
  warning_required: true
```

#### `evidence-dossier.md` template

A structured Markdown file with sections:
1. Consultation Summary — one paragraph stating the question being answered
2. Source Set — table of source_id, credibility, key claims, used in which dimension
3. Cross-Reference Matrix — which sources corroborate which findings
4. Layer-A/B/C Existence-Proof Map — which findings have which level of production evidence
5. Open Questions — claims for which the source set is insufficient

#### `debate-summary.md` template

Sections:
1. Trigger — which Phase 2.5 condition fired
2. Round 1 — Advocate opening / Skeptic rebuttal (with evidence cites)
3. Round 2 — Advocate counter / Skeptic closing (with concessions noted)
4. Convergence Points — claims both agents agree on
5. Unresolved Divergence — claims still in dispute, with each side's position

## Violation Determination

- Task completes a phase with no corresponding worklog directory → Violation
- Phase worklog missing any of the three core files (references.md, findings.md, decisions.md) → Violation
- `decisions.md` contains a decision with no traceable evidence (no reference to findings or references) → Violation
- `findings.md` contains a finding with no source reference → Violation
- Coordinator proceeds to next phase without verifying worklog completeness → Violation
- Worklog directory or file names do not follow kebab-case convention → Violation

## Exceptions

- Phases that produce no decisions (e.g., a phase that only collects user input without making design choices) may have an empty `decisions.md` with a note: "No decisions made in this phase — input collection only."
- For tasks with a single phase, the phase folder is still required (e.g., `phase-1-execution/`).

Tradeoff: Artifact-template enforcement (source-registry.yaml, evidence-dossier.md, debate-summary.md, ADR/C4 directories) keeps this rule above the 100-line cap. The artifacts are load-bearing for T-Team's evidence-chain integrity; extracting them to a separate rule weakens the worklog rule's authority. The added length is the lesser cost.
