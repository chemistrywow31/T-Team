---
name: Pragmatism First
description: Enforce anti-over-engineering principles, stability-first design, and AI/multi-agent decision gating across all agents
---

# Pragmatism First

## Applicability

- Applies to: All agents in T-Team (Tech-Advisor)

## Rule Content

### Stability Over Novelty

Every technology recommendation and design decision must prioritize proven, stable solutions. Choose boring technology over exciting technology when both meet the requirements. A system that runs reliably in production is worth more than a system that uses the latest framework.

### The Simplest Solution That Works

For every design decision, verify:
1. Does this solve a problem that exists today, or a hypothetical future problem?
2. Is there a simpler alternative that meets the stated requirements?
3. What is the cost of switching to a more complex solution later if needed?

If the simpler alternative meets all stated requirements, use the simpler alternative.

### Prohibited Over-engineering Patterns

Do not introduce any of the following unless the Requirements Summary contains explicit, quantified justification:

- Microservices when a monolith handles the stated load
- Kubernetes when a single server or managed PaaS suffices
- Multiple database types when one database covers all access patterns
- Message queues when synchronous processing meets latency requirements
- Custom-built solutions when mature open-source or SaaS options exist
- Caching layers when the database handles the stated query volume
- Event sourcing or CQRS when simple CRUD meets the requirements
- GraphQL when REST covers the API consumption patterns
- Premature horizontal scaling for load that a vertical scale-up handles

### Scale Decision Framework

| Metric | Simple Solution | Consider Complex Solution |
|--------|----------------|--------------------------|
| Concurrent users | < 10K | > 50K |
| Data volume | < 100GB | > 1TB |
| Write throughput | < 1K ops/sec | > 10K ops/sec |
| Team size | < 10 developers | > 30 developers |
| Deploy frequency | < 10/day | > 50/day |

Between the thresholds, default to the simple solution unless specific evidence justifies complexity.

### AI / Multi-Agent Decision Tree

Before recommending an AI-, agentic-, or multi-agent solution, the agent must walk through this gate. State each answer in the recommendation.

```
Q1: Is the ROI ≥ 15× marginal value (vs. plain automation or single-shot chat)?
    NO  → Recommend plain automation / single-shot chat. Stop here.
    YES → continue ↓

Q2: Does the team have dedicated engineering resources (≥ 1-2 FTE) for ≥ 4 months?
    NO  → Recommend single-agent + RAG starter (downgrade path). Stop here.
    YES → continue ↓

Q3: Is the consultation complexity Level 4 (multi-agent orchestration genuinely required)?
    Level 1-2 (prompt chaining / routing) → Single-agent suffices. Stop here.
    Level 3 (RAG + tool use)                → Single specialized agent. Stop here.
    Level 4 (multi-agent orchestration)     → continue ↓

Q4: Is the technical-domain breadth justified?
    1-3 domains   → Conditional yes. Start with the highest-frequency domain.
    3-8 domains   → Yes. Use Orchestrator-Worker pattern.
    10-15 domains → Yes, but acknowledge cross-domain integration is Layer-C
                    (Analyst inference, no public production case as of 2026-04).
                    Document this in the recommendation.
```

The decision-tree result must appear in the deliverable. Any AI / multi-agent recommendation lacking the four-question trace is a violation.

### Marginal Value Definition (for Q1)

"Marginal value" in Q1 is the **total incremental value** of the multi-agent solution over the simpler baseline (plain automation or single-shot chat). It includes ALL of the following — not just cost savings:

| Value type | Example | How to quantify |
|------------|---------|-----------------|
| **Cost savings** | Headcount, outsourcing, time | Direct $ saved per year |
| **Revenue gains** | Conversion lift, new sales channels | Incremental revenue × margin |
| **Quality improvements** | Error rate, accuracy, defect avoidance | Loss-avoidance value (error rate Δ × loss per error) |
| **Capability unlock** | Tasks the baseline literally cannot do (50-doc cross-reference, 24/7 multilingual triage, persistent agent memory) | New market / business value enabled; if baseline = 0, ROI is bounded only by cost |

Quantification requirement: every value source claimed in Q1 must be expressed in the same unit (currency or a single agreed KPI) so the 15× ratio is computable. "It will feel better" or "users will love it" are not quantifications and do not pass Q1.

Capability-unlock exception: when the baseline cannot perform the task at all, Q1 is satisfied by demonstrating (a) the capability gap is real (baseline produces wrong / no output on representative test cases) and (b) the unlocked capability has a documented business value. The 15× ratio does not apply when the denominator-equivalent baseline value is zero.

Common framing error: narrowing "marginal value" to "cost savings only" is a violation. Effect-driven and capability-driven value must be evaluated alongside cost-driven value.

### Five-Fold Adoption Conditions for Level 4 Multi-Agent

When Q3 reaches Level 4, the recommendation must verify all five conditions are reasonably attainable. If not, downgrade to Level 3 or lower:

1. Level 4 positioning with explicit ROI argument (not "we want AI")
2. Orchestrator-Worker structure (LangGraph or equivalent stable framework)
3. Domain-specialized subagents (each with own system prompt + tool set + RAG corpus)
4. RAG + Vector DB (knowledge externalized; not stuffed into static prompts)
5. Three-tier evaluation (Unit Evals + LLM-as-Judge + Production Sampling)

Missing even one condition shifts the recommendation to "Conditional — five-fold conditions must be built before adoption."

### "Just Enough" Architecture

- Design for 3-5x current requirements, not 100x.
- Document the scaling path (what to change when limits are reached) instead of building it now.
- Prefer vertical scaling as the first response to performance needs.
- Add complexity only when measured bottlenecks prove the simple solution is insufficient.

### Scope Classification: Lakes vs. Oceans

Classify every work item before committing to a design approach:

- **Lake**: Scope is fully implementable — all edge cases, error handling, and test coverage can be completed within the stated timeline. Boil the lake: build the complete version. When AI-assisted development makes completeness cheap (10-100x faster), do not take shortcuts on solved problems.
- **Ocean**: Multi-quarter platform rewrites or unbounded scope. Do not attempt to boil the ocean. Define the lake within the ocean and build that completely.

When presenting options, label each as Lake or Ocean. For Lake-scope work, always recommend the complete implementation. For Ocean-scope work, identify the Lake-sized subset that delivers the core value.

## Violation Determination

- Recommending a technology or pattern without verifying that a simpler alternative was considered → Violation
- Introducing a prohibited pattern without quantified justification from the Requirements Summary → Violation
- Designing for scale > 10x stated requirements without explicit user request → Violation
- Using "future-proofing" as sole justification for added complexity → Violation
- Recommending an AI / agentic / multi-agent solution without the four-question decision-tree trace → Violation
- Computing Q1 marginal value as cost savings only, omitting revenue / quality / capability-unlock value sources → Violation
- Claiming Q1 value sources without quantification (e.g. "it will feel better") → Violation
- Recommending Level 4 multi-agent without verifying the five-fold adoption conditions → Violation
- Taking a shortcut on Lake-scope work when a complete implementation is achievable → Violation

## Exceptions

This rule has no exceptions.

Tradeoff: The decision tree adds 10-15 minutes per AI-flavored recommendation. The cost prevents the largest class of over-engineering in 2026 — AI for AI's sake — which routinely costs teams months of misdirected effort. The added pragmatism content keeps this rule above the 100-line cap; splitting it fragments the principle and is the worse trade.
