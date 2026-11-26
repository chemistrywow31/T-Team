---
name: Worklog
description: Where a consultation's records go and what each phase folder must hold, so every decision can be traced back to its evidence
paths:
  - ".worklog/**"
---

# Worklog

Applies to everyone who writes consultation output. The worklog is the team's memory between phases and between sessions: a later phase or a resumed session reads it instead of the conversation.

## Layout

```
.worklog/{yyyymm}/{task-name}/phase-{n}-{label}/
  references.md   findings.md   decisions.md
```

`{yyyymm}` is the month the consultation started. Task and phase labels are kebab-case (`phase-1-discovery`, `phase-2-analysis`, `phase-3-selection`, `phase-4-ux`, `phase-5-spec`, `phase-6-review`). The coordinator creates the task folder before Phase 1.

## The three files

- `references.md`: every source used in the phase, with what it is and how it was used. User statements that drove a decision count. When there is no external reference, write "No established reference found for {topic}."
- `findings.md`: facts, patterns, constraints and comparisons, each pointing to a reference.
- `decisions.md`: each decision with its rationale, the alternatives rejected and why, the findings it rests on, and what it affects downstream. A reader who disagrees should be able to name the evidence to challenge. When a decision has no external reference, write out the first-principles reasoning.

A phase that only collected input may leave `decisions.md` with "No decisions made in this phase — input collection only."

## Phase artifacts

| Artifact | Folder | When |
|----------|--------|------|
| `source-registry.yaml` | `phase-2-analysis/` | From Phase 2 on; format in `.claude/skills/source-registry-management/SKILL.md` |
| `evidence-dossier.md` | `phase-2-analysis/` | Level 4 only |
| `debate-summary.md` | `phase-2-analysis/` | When Phase 2.5 ran; format in `.claude/skills/adversarial-debate/SKILL.md` |
| `adrs/adr-{NNN}-{kebab-title}.md` | `phase-5-spec/` | One per architecture decision |
| `diagrams/c4-{level}-{name}.md` | `phase-5-spec/` | One per C4 diagram |

`evidence-dossier.md` has five sections: the question being answered in one paragraph; the source set (source_id, credibility, key claims, dimension used in); a cross-reference matrix of which sources back which findings; the Layer-A/B/C existence-proof map; and the claims the source set cannot yet support.

## Phase boundary

Before moving to the next phase, the coordinator checks that the phase folder has its three files and the artifacts above, and that each decision traces to findings and references. A registry-backed `phase-2-analysis/` still needs its three files, even if they are short and point to where the analysis happened.
