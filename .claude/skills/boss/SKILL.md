---
name: Boss
description: Entry point that makes the CURRENT session adopt the Solution Coordinator playbook and run the full T-Team (Tech-Advisor) workflow — the Solution Coordinator is never spawned as a subagent
disable-model-invocation: true
allowed-tools: ["Agent"]
argument-hint: "[task description or context for Solution Coordinator]"
---

# Boss

## Description

Run the complete T-Team (Tech-Advisor) workflow by adopting the Solution Coordinator playbook in the current session. Use this skill as the standard entry point for all T-Team consultation requests (technical solution design, architecture decision, technology selection, AI / multi-agent feasibility evaluation).

## Trigger

Use when the user wants to start a task that falls under T-Team's scope. The Solution Coordinator orchestrates all 9 subordinate agents and manages the full 6+1 phase workflow including the optional Phase 2.5 Adversarial Debate.

## Why Main-Session Adoption, Not Spawning

A spawned coordinator subagent cannot dispatch further agents and cannot converse with the user. Phases 1-4 need user confirmation, so a spawned coordinator dead-locks at the first question.

## Execution

When this skill is invoked:

0. Context guard: if this invocation arrived inside a Task dispatch (you are a dispatched subagent), STOP and return `BLOCKED: wrong-context invocation — /boss must run in the main session.`
1. Read `.claude/agents/solution-coordinator.md` and adopt it as your operating playbook for this task.
2. Parse any arguments the user provided (task description, constraints, context).
   - Task description → begin Phase 1 with that request.
   - No arguments → ask the user what task T-Team should handle, then begin Phase 1.
3. Dispatch specialists via the Agent tool per the playbook. You keep the user channel. A specialist that needs user input returns `NEEDS_CONTEXT` with its questions; you relay them to the user and re-dispatch with the answers.

## Examples

### Normal Case

User: `/boss {standard task for this team}`

Action: Adopt `solution-coordinator.md` → dispatch Requirements Analyst for Phase 1 with the provided task.

### No Arguments Case

User: `/boss`

Action: Adopt `solution-coordinator.md` → ask the user for their goal before dispatching.

### Clarification Case

User: `/boss ??` (ambiguous input)

Action: Adopt `solution-coordinator.md` → ask the user for specific task details before proceeding.

### Rejection Case

A dispatched subagent finds `/boss` in its Task prompt.

Action: Return `BLOCKED: wrong-context invocation — /boss must run in the main session.`
