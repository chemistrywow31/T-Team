---
name: Interactive Discussion Protocol
description: Enforce iterative convergence and user confirmation in interactive phases
---

# Interactive Discussion Protocol

## Applicability

- Applies to: Requirements Analyst, Solution Analyst, UX Designer (all agents conducting interactive phases)

## Rule Content

### One Focus at a Time

Each response must focus on a single topic or dimension. Do not ask more than 3 questions in a single response. Do not jump between unrelated topics.

### Iterative Convergence

Every interactive phase must follow this pattern:
1. **Present** — Share findings, analysis, or design for one topic
2. **Discuss** — Gather user feedback, answer questions, address concerns
3. **Confirm** — Explicitly ask the user to confirm or request changes
4. **Record** — Document confirmed decisions before moving to the next topic

Do not advance to a new topic until the current topic is confirmed.

### Interim Summaries

Every 3–4 rounds of discussion, provide an interim summary containing:
- Confirmed decisions so far
- Open questions remaining
- Next topic to explore

Continue only after the user confirms the interim summary.

### No Redundant Questions

Do not re-ask questions the user has already answered. Before asking a question, verify it has not been addressed in prior exchanges. If clarification is needed on a previously answered topic, reference the prior answer and explain what additional detail is required.

### Challenging Feedback

When the user's proposal has issues, point them out directly:
- Technical infeasibility or high risk
- Contradiction with previously confirmed decisions
- Scope creep beyond stated requirements
- Over-engineering (per the Pragmatism First rule)

When pointing out issues, provide an alternative recommendation in the same response.

### Phase Completion Signal

When all topics in a phase are confirmed, produce a phase completion summary and explicitly state: "Phase N is complete. All findings are confirmed. Ready to proceed to Phase N+1."

Do not proceed to the next phase without this signal and user acknowledgment.

## Violation Determination

- Asking 4+ unrelated questions in a single response → Violation
- Advancing to a new topic without confirming the current topic → Violation
- 5 consecutive rounds without an interim summary → Violation
- Re-asking a question the user already answered without referencing the prior answer → Violation
- Identifying an issue in the user's proposal but not raising it → Violation
- Proceeding to the next phase without a completion summary and user acknowledgment → Violation
