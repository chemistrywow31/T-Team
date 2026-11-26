---
name: Solution Analysis
description: Run Phases 2 and 3 of a T-Team consultation with the user — six-dimension analysis on both evidence axes, the Phase 2.5 self-flag, debate synthesis, and technology selection with the AI decision tree
---

# Solution Analysis

Phases 2 and 3 turn the confirmed Requirements Summary into an analysis the user has confirmed and a technology choice per component. Present findings one dimension, or one group of related dimensions, at a time, and adjust to what the user tells you before going on. Write to `phase-2-analysis/` and `phase-3-selection/`; the source registry starts in Phase 2.

## Phase 2: analysis

1. Apply `multi-dimensional-analysis` to the solution and to each major component: six dimensions (Feasibility, Performance, Security, Cost, Maintainability, Risk), each labelled on both axes, knowledge source (Layer 1/2/3) and existence proof (Layer-A/B/C).
2. For the Risk dimension, apply `failure-mode-analysis`. When AI or agentic components are present, include its four AI failure modes.
3. Register every external claim with `source-registry-management` before using it, cite by `source_id`, and tag UNVERIFIED claims (`.claude/rules/evidence-standards.md`).
4. Cross-domain integration of ten or more domains, or a first-of-its-kind design, gets the `[Layer-C: Analyst inference + demonstration]` disclosure.
5. At Level 4, write `evidence-dossier.md` (`.claude/rules/worklog.md`) and apply at least one `reasoning-methodology` combo, saying which and why: Combo 1 for an architecture choice, Combo 2 for a multi-candidate trade-off, Combo 3 when the output cites production cases or numbers.

When the evidence for a dimension cannot support more than Low, say so rather than rating it up:

```
INSUFFICIENT_EVIDENCE: Cannot rate dimension {N} above Low because {gap}.
Need: {source type or independent verification}.
```

If confidence is Medium or below on any dimension, put this in the report header. It is one of the Phase 2.5 triggers.

```
PHASE_2_SELF_FLAG: Confidence Medium or below on dimension(s) {list}.
Recommend Phase 2.5 Adversarial Debate to stress-test the recommendation.
```

### Analysis Report

```markdown
# Multi-dimensional Analysis Report

## Recency Warning        (when required)
## Layer-C Disclosure     (when required)

## 1. Technical Feasibility
**Rating**: High / Medium / Low
**Knowledge Layer**: 1 / 2 / 3
**Existence Layer**: A / B / C
**Findings**: {analysis with [SRC-NNN] citations}
**Risks**: {risks}

## 2. Performance & Scalability
## 3. Security
## 4. Cost                (with a cost table)
## 5. Maintainability & Team Fit
## 6. Risk Assessment     (failure-mode registry, AI failure modes when applicable)

## Cross-dimension Trade-offs
## Overall Verdict
**Verdict**: Go / Go with conditions / No-go
**Reasoning Methodology Applied**: {combo and why, at Level 4}
**Self-flag**: {when triggered}
```

## Phase 2.5: synthesis

When the debate ran, read the four round outputs and write `debate-summary.md` per Step 3 of `adversarial-debate`. You wrote the recommendation under debate, so record each concession as the debaters made it and leave unresolved divergence for the user to decide.

## Phase 3: selection

Apply `technology-comparison` to each major component: two or three real candidates, the seven-criterion matrix with `[SRC-NNN]` on the scores, the pragmatism check, and the recommendation with its trade-off and migration path. Tag each candidate with its Tech Radar ring (`adr-c4-authoring` Part 3).

Any AI, agentic or multi-agent recommendation adds the four-question trace from `.claude/rules/pragmatism.md` to its recommendation block, and at Level 4 confirms the five adoption conditions are attainable.

## Heuristics

Reach for these when they bear on the decision; they are not a checklist.

- Diagnose the team's state (falling behind, treading water, repaying debt, innovating) and match the solution's complexity to it.
- Picture the worst-case failure and how far it spreads.
- An organization has about three innovation tokens; spend them on what differentiates it and use boring tech elsewhere.
- Prefer strangler fig, canary and refactor over a big-bang rewrite; prefer flags and gradual rollouts over irreversible commitments.
- The architecture has to fit the org chart (Conway's Law).
- Slow CI and painful local setup show up later as worse software.
- Remove accidental complexity before touching the essential kind.
- Treat reliability as an error budget to spend, not an uptime number.
