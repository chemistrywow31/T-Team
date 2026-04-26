---
name: Context Management
description: Prevent context bloat by enforcing focused subtasks and summary-based reporting
---

# Context Management

## Applicability

- Applies to: All agents in the Technical Solution Design team

## Rule Content

### Coordinator Subtask Design

Solution Coordinator must break work into focused subtasks when delegating to agents. Each subtask must:
- Have a single clear objective
- Include only the context the agent needs (confirmed summaries from prior phases, not full conversation history)
- Specify the expected output format

### Summary-based Reporting

When an agent completes a subtask, report results as a structured summary. Do not return raw analysis, full conversation logs, or intermediate working notes. The summary must contain:
- Key findings or decisions (bulleted list)
- Open questions or items needing user confirmation
- References to specific sections if detailed content exists in a document

### Phase Transition Summaries

At each phase transition, the coordinator must produce a transition summary that includes:
- Confirmed decisions from the completed phase (not the full discussion history)
- Constraints and requirements relevant to the next phase
- Open items carried forward

This transition summary is the primary input for the next agent. The next agent must not require access to prior phase conversations.

### Context Accumulation Prevention

- Do not copy full prior-phase outputs into new subtasks. Summarize and reference.
- If an agent needs specific detail from a prior phase, reference the document section (e.g., "See Requirements Summary §Constraints") rather than inlining the content.
- Discard intermediate analysis drafts after the final version is confirmed.

## Violation Determination

- Passing full conversation history as subtask context instead of a focused summary → Violation
- Agent returning raw working notes instead of a structured summary → Violation
- Phase transition without a transition summary document → Violation
- Inlining full prior-phase output when a reference would suffice → Violation
