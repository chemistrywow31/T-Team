---
name: Boss
description: Entry point that spawns the Solution Coordinator to run the full T-Team (Tech-Advisor) workflow
disable-model-invocation: true
allowed-tools: ["Agent"]
argument-hint: "[task description or context for Solution Coordinator]"
---

# Boss

## Description

Launch the Solution Coordinator to run the complete T-Team (Tech-Advisor) workflow. Use this skill as the standard entry point for all T-Team consultation requests (technical solution design, architecture decision, technology selection, AI / multi-agent feasibility evaluation).

## Trigger

Use when the user wants to start a task that falls under T-Team's scope. The Solution Coordinator orchestrates all 9 subordinate agents and manages the full 6+1 phase workflow including the optional Phase 2.5 Adversarial Debate.

## Execution

When this skill is invoked, spawn the Solution Coordinator agent to handle the entire workflow:

1. Parse any arguments the user provided (task description, constraints, context)
2. Spawn the `solution-coordinator` agent via the Agent tool with subagent_type `Solution Coordinator`
3. Pass the user's request and any arguments as the agent's prompt
4. The Solution Coordinator runs the full workflow as defined in its prompt

### Spawn Instructions

Use the Agent tool with these parameters:

- `subagent_type`: `Solution Coordinator`
- `prompt`: Include the user's original request. If no arguments were provided, instruct the coordinator to begin from the start of its standard workflow and clarify missing context via dialogue.

### With Arguments

```
/boss {task description}
```

Spawn Solution Coordinator with prompt: "Start the T-Team (Tech-Advisor) workflow. User request: {task description}"

### Without Arguments

```
/boss
```

Spawn Solution Coordinator with prompt: "The user invoked /boss with no arguments. Begin by clarifying what task they want T-Team to handle, then run the standard workflow."

## Examples

### Normal Case

User: `/boss {standard task for this team}`

Action: Spawn Solution Coordinator to run the standard workflow with the provided task.

### No Arguments Case

User: `/boss`

Action: Spawn Solution Coordinator to begin an interactive intake — clarify the user's goal before dispatching.

### Clarification Case

User: `/boss ??` (ambiguous input)

Action: Spawn Solution Coordinator with prompt instructing it to request specific task details before proceeding.
