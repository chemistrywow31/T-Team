---
name: Requirements Discovery
description: Run the Phase 1 interview of a T-Team consultation, classify its Consultation Level, and write the Requirements Summary
---

# Requirements Discovery

Phase 1 turns the user's question into a confirmed Requirements Summary. Stay in requirements space: proposing a solution here anchors the analysis before the constraints are known. Write to `phase-1-discovery/`.

## Interview

Let the user describe the goal in their own words before drilling in. Then cover, one area at a time:

- the problem and the outcome wanted;
- scope, in and out;
- constraints: budget, timeline, team size and skills, existing systems and integrations, compliance, load (concurrent users, data volume, latency);
- who uses the system, who maintains it, who decides;
- assumptions, each marked confirmed or pending.

Ask early, in the first round, which parts are in the user's control and in scope, and what the end-to-end deliverable looks like when it is done. In a 2026-07 consultation neither was asked up front; that cost a round of analysis on a gateway that turned out to be out of scope, and the pipeline grew from four stages to seven in round six. An assumption the design rests on needs an explicit yes, not two rounds without objection.

If the request is too vague to classify, say what is missing and ask for it rather than inventing requirements:

```
INSUFFICIENT_REQUIREMENTS: Cannot classify because {gap}.
Asking the user: {question}.
```

## Consultation Level

Classify once the requirements are clear. The level sets downstream rigor: the Phase 2.5 triggers, the evidence dossier, the Layer-C disclosure. For AI solutions it follows Q3 in `.claude/rules/pragmatism.md`.

- Level 1: prompt chaining or routing; no architecture decision.
- Level 2: a single tool-using capability, such as RAG.
- Level 3: one specialized agent or component with significant scope.
- Level 4: multi-agent orchestration or a major architecture decision.

## Requirements Summary

```markdown
# Requirements Summary

## Consultation Level
{1-4} — {one-line justification}

## Objective
{one paragraph}

## Scope
### In Scope
- {item}
### Out of Scope
- {item}

## Constraints
| Category | Constraint | Value/Detail |
|----------|-----------|--------------|

## Stakeholders
| Role | Relationship | Key Concerns |
|------|-------------|--------------|

## Assumptions
| # | Assumption | Status |
|---|-----------|--------|

## Success Criteria
- {criterion with a measurable target}
```

The user confirms the summary before Phase 2 starts.
