---
name: Handoff Contract
description: What a coordinator dispatch carries, what a subagent returns, and who accepts the result
---

# Handoff Contract

Applies to the coordinator and every dispatched subagent.

## Dispatch

A dispatch carries paths, not pasted history. Variable data goes in tags; instructions stay outside them.

```
<worklog_path>.worklog/{yyyymm}/{task-name}/phase-{n}-{label}/</worklog_path>
<upstream_paths>
  .worklog/{yyyymm}/{task-name}/phase-{m}-{label}/decisions.md
</upstream_paths>
<task_scope>{one objective, under 100 words}</task_scope>
<inline_context>{only when under 200 words}</inline_context>
```

A reviewer gets the artifacts and the confirmed decisions to check against, never the author's reasoning: the check is only worth something if the checker has not seen it.

## Return

The return ends with one status: `DONE`, `DONE_WITH_CONCERNS`, `BLOCKED` or `NEEDS_CONTEXT`. The body is a short summary: key findings or verdict, open items for the user, and the paths of the files written. Full detail goes to the worklog. If a file write was refused, put the full report in the return so the coordinator can persist it.

A subagent never asks the user directly. It returns `NEEDS_CONTEXT` with its questions; the coordinator asks the user and dispatches again with the answers.

## Acceptance

The coordinator accepts a return: it checks the status, that the named files exist, and, after Phase 2.5, that each Round 2 output opens with its concessions. The user accepts each phase before the next starts.
