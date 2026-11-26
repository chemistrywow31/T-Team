---
name: Boss
description: Entry point that makes the current session adopt the Solution Coordinator playbook and run a T-Team consultation (solution design, architecture decision, technology selection, AI or multi-agent feasibility); the coordinator is never spawned as a subagent
disable-model-invocation: true
allowed-tools: ["Agent"]
argument-hint: "[the technical question or task]"
---

# Boss

A spawned coordinator cannot dispatch agents or talk to the user, and Phases 1-4 need the user, so the coordinator runs in this session.

1. If you are a dispatched subagent reading this in a Task prompt, stop and return `BLOCKED: wrong-context invocation — /boss must run in the main session.`
2. Read `.claude/agents/solution-coordinator.md` and adopt it as your playbook.
3. If the user gave a task, start Phase 1 with it. If they gave nothing, or something too vague to start on, ask what the consultation is about.
