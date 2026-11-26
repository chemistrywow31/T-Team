---
name: Pragmatism First
description: How T-Team keeps recommendations simple, sizes them to the stated load, and gates any AI or multi-agent recommendation
paths:
  - ".worklog/**"
---

# Pragmatism First

Applies to every recommendation and design decision. A system that runs reliably in production is worth more than one built on the newest framework.

## The simplest solution that works

For each decision, ask: does this solve a problem that exists today; is there a simpler option that meets the stated requirements; what would switching to the complex option later cost? If the simpler option meets every stated requirement, recommend it. "Future-proofing" alone does not justify complexity.

These need quantified justification in the Requirements Summary before you recommend them: microservices over a monolith, Kubernetes over a single server or managed PaaS, more than one database type, a message queue where synchronous processing meets the latency target, a custom build where a mature open-source or SaaS option exists, a cache in front of a database that handles the load, event sourcing or CQRS over CRUD, GraphQL over REST, and horizontal scaling where a vertical scale-up handles the load.

| Metric | Simple solution | Consider complex |
|--------|-----------------|------------------|
| Concurrent users | < 10K | > 50K |
| Data volume | < 100GB | > 1TB |
| Write throughput | < 1K ops/sec | > 10K ops/sec |
| Team size | < 10 developers | > 30 developers |
| Deploy frequency | < 10/day | > 50/day |

Between the columns, default to simple unless evidence says otherwise. Design for 3-5x the stated requirements, not 100x, and not beyond 10x without the user asking. Write down the scaling path instead of building it. Scale vertically first.

## Lakes and oceans

Label each work item. A lake can be finished, edge cases and tests included, within the stated timeline: recommend the complete version. An ocean is a multi-quarter rewrite or open scope: name the lake inside it that delivers the core value and recommend building that completely.

## AI and multi-agent decision tree

Before recommending an AI, agentic or multi-agent solution, answer these four questions and put the answers in the deliverable.

```
Q1: Is the marginal value at least 15x that of plain automation or single-shot chat?
    NO  → recommend plain automation or single-shot chat. Stop.
Q2: Are 1-2 dedicated engineers available for at least 4 months?
    NO  → recommend a single agent with RAG as the starter. Stop.
Q3: What level is it?
    Level 1-2 (prompt chaining, routing) → a single agent. Stop.
    Level 3 (RAG + tool use)             → one specialized agent. Stop.
    Level 4 (multi-agent orchestration)  → continue.
Q4: How many technical domains?
    1-3   → conditional yes; start with the most frequent domain.
    3-8   → yes, orchestrator-worker.
    10-15 → yes, with the Layer-C disclosure: no public production case of
            cross-domain integration at this breadth as of 2026-04.
```

Marginal value in Q1 is the whole incremental value, in one unit (currency or one agreed KPI) so the ratio can be computed: cost saved, revenue gained, losses avoided through fewer errors, and capabilities the baseline cannot provide at all. Counting cost savings alone understates it; "users will love it" is not a number. When the baseline cannot do the task, Q1 passes if representative test cases show the gap and the capability has a documented business value.

## Level 4 adoption conditions

A Level 4 recommendation needs all five to be reasonably attainable: an explicit ROI argument; an orchestrator-worker structure on a stable framework; domain-specialized subagents, each with its own prompt, tools and corpus; RAG over a vector store rather than knowledge stuffed into prompts; and three-tier evaluation (unit evals, LLM-as-judge, production sampling). If any one is missing, the recommendation is "Conditional: build the missing conditions before adoption".
