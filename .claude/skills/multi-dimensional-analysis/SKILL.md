---
name: Multi-dimensional Analysis
description: Structured framework for evaluating technical solutions across six dimensions, with two orthogonal axes (knowledge-source layers and existence-proof layers)
---

# Multi-dimensional Analysis

## Purpose

Evaluate any technical solution across six quality dimensions, anchored by two orthogonal analytical axes:

- **Knowledge-source axis (Layer 1/2/3)**: Where does this idea come from? Established practice / current trend / first-principles reasoning.
- **Existence-proof axis (Layer-A/B/C)**: At what scale has this been validated? Component / orchestration-pattern / cross-domain.

The two axes answer different questions and are both required for Level 4 consultations.

## When to Use

Use this skill when Solution Analyst evaluates a proposed technical approach during Phase 2 (Multi-dimensional Analysis). Apply it to the overall solution and to individual component decisions.

## Method

### Step 1: Establish Baseline

Collect from the Requirements Summary:
- Performance targets (concurrent users, data volume, latency)
- Budget constraints
- Team composition and skill set
- Compliance requirements
- Timeline
- Consultation level (per `pragmatism.md` decision tree): 1 / 2 / 3 / 4

### Step 2: Knowledge-Source Axis (Layer 1/2/3)

Before evaluating any dimension, ground the analysis in existing knowledge:

1. **Layer 1 — Battle-tested standards**: Identify proven patterns and solutions with extensive production track records. Use these as the default recommendation.
2. **Layer 2 — Current best practices**: Review recent industry trends and emerging approaches. Adopt only when Layer 1 solutions have documented shortcomings for the stated requirements.
3. **Layer 3 — First principles**: When Layer 1 and Layer 2 conflict, or when no established solution fits, reason from fundamentals. Prioritize logic over convention.

Document which layer each recommendation originates from. Layer 2 and Layer 3 recommendations must include explicit reasoning for why Layer 1 is insufficient.

### Step 3: Existence-Proof Axis (Layer-A/B/C)

Independent of where the idea comes from, classify what level of production validation it has. Cite source registry entries.

| Layer | Definition | Threshold for use |
|-------|-----------|-------------------|
| Layer-A — Component level | Individual technology component is production-validated by at least 2 third-party deployments | Required for any Adopt-tier recommendation |
| Layer-B — Orchestration-pattern level | The combination pattern has at least 1 publicly documented production case at comparable scale | Required for Adopt-tier multi-component architectures |
| Layer-C — Cross-domain level | Single-team integration across many domains is Analyst inference and demonstration; no public production case at the proposed breadth | Trial-tier or Assess-tier; must include Layer-C honesty disclosure |

The Layer-A/B/C classification appears in:
- ADR Layer-A/B/C Existence Proof section (per `adr-c4-authoring` skill)
- Solution Analyst's Phase 2 output (the analysis report)
- Layer-C disclosures in deliverables when applicable

### Step 4: Layer-C Honesty Disclosure

When the recommendation involves cross-domain integration that lacks public production cases, the analysis must include a `[Layer-C: Analyst inference + demonstration]` block stating:

```
[Layer-C: Analyst inference + demonstration]

The proposed cross-{N}-domain integration ({domain list}) is, as of {date},
an Analyst inference. No public production case at this breadth has been documented.
Adoption requires the team to verify {specific conditions}.
```

This block is mandatory for cross-domain (greater than or equal to 10 domains) multi-agent recommendations and for first-of-kind integrations. It is informational, not blocking.

### Step 5: Evaluate Each Dimension

For each of the six dimensions, produce:
- **Rating**: High / Medium / Low (relative to meeting requirements)
- **Layer-A/B/C label**: A / B / C with cited source_id evidence
- **Key findings**: 2-5 bullet points of concrete observations
- **Evidence**: Reference to Source Registry entries by source_id
- **Risks**: Specific risks with severity and probability

The six dimensions:

1. **Technical Feasibility** — Can it be implemented? What are unknowns? Reference implementations?
2. **Performance & Scalability** — Does design meet performance requirements? Bottlenecks? Growth ceiling?
3. **Security** — Attack surface, auth model, data classification, threat modeling (STRIDE), compliance, supply chain risk.
4. **Cost** — Development, infrastructure, ongoing maintenance, alternative comparison.
5. **Maintainability & Team Fit** — Team experience, learning curve, onboarding, long-term structure.
6. **Risk Assessment** — Technical, operational, business risks; failure modes (per `failure-mode-analysis` skill); mitigations.

Apply these engineering heuristics during evaluation:
- **Blast radius instinct**: For every component, assess worst-case failure impact and which downstream systems are affected.
- **Reversibility preference**: Favor design choices that are easy to reverse (feature flags, A/B tests, gradual rollouts).
- **Conway's Law awareness**: Verify proposed architecture aligns with team's organizational structure.
- **Essential vs. accidental complexity**: Distinguish problems inherent to the domain from problems the team is creating for itself.
- **Systems over heroes**: Design for tired humans at 3am, not for best-case heroic debugging.

### Step 6: AI-Specific Failure Mode Check

When the consultation involves AI / agentic / multi-agent solutions, invoke the AI Agent Failure Modes section of `failure-mode-analysis` skill. The four AI-specific failure modes (tool-call schema hallucination, context truncation, retry loop, silent quality degradation) are not detectable by standard APM and must be explicitly designed for.

### Step 7: Cross-dimension Conflicts

Identify trade-offs between dimensions and document each with the recommended resolution and rationale (e.g., Performance vs. Cost, Security vs. Usability, Maintainability vs. Performance).

### Step 8: Summary Rating

Produce an overall feasibility assessment:
- **Go**: All dimensions High or Medium, no unmitigated High-severity risks, no Critical failure modes unaddressed, AI decision tree (per `pragmatism.md`) passed
- **Go with conditions**: Specific mitigations or further conditions required before adoption
- **No-go**: Any dimension Low without viable mitigation, or Critical failure modes with no mitigation path

If overall confidence is Medium or below on any dimension, self-flag for Phase 2.5 (Adversarial Debate) per coordinator triggers.

## Examples

### Normal case: Standard CRUD service

Input: E-commerce API serving 10K concurrent users; team knows Node.js + PostgreSQL; 3 months to MVP; $2K/month infra budget.

Layer 1/2/3: Layer 1 (battle-tested) — Node.js + PostgreSQL is the default for this scale.

Layer-A/B/C: Layer-A (PostgreSQL battle-tested), Layer-B (Node.js + PostgreSQL combination is documented in many production cases [SRC-X]).

Six-dimension output:

| Dimension | Rating | Layer | Key Finding |
|-----------|--------|-------|------------|
| Feasibility | High | A+B | Standard CRUD + search, proven patterns [SRC-X] |
| Performance | Medium | A | 10K concurrent needs connection pooling and Redis cache |
| Security | Medium | A | PCI compliance required for payments — separate ADR |
| Cost | High | A | $2K/month sufficient on managed services |
| Maintainability | High | A | Team already proficient |
| Risk | Medium | A | Payment integration is the primary unknown |

AI failure mode check: not applicable (no AI components).

Overall: Go with conditions — finalize payment provider and PCI approach.

### Edge case: Cross-domain multi-agent recommendation

Input: User wants AI-powered consulting tool covering 12 technical domains; team has 2 FTE for 3 months.

Layer 1/2/3: Layer 1 has no equivalent; Layer 2 partial (LangGraph orchestration is current best practice); Layer 3 reasoning required for the integration.

Layer-A/B/C: Layer-A (LangGraph component proven [SRC-013]); Layer-B (Glean 5-layer Orchestrator-pattern proven [SRC-039]); **Layer-C** (12-domain single-team integration has no public production case).

`[Layer-C: Analyst inference + demonstration]` block included.

Six-dimension output: Risk rated Medium (depending on team's ability to build all five-fold conditions per `pragmatism.md`); other dimensions Medium-High.

Overall: Go with conditions — limit to 3-5 high-frequency domains for Phase 1; Layer-C disclosure mandatory; Phase 2.5 Adversarial Debate must fire.

### Rejection case: Layer 2 recommended without Layer 1 shortcoming evidence

Draft output recommends "Use a custom-built event store (Layer 2 — current trend)" without explaining why PostgreSQL event sourcing (Layer 1) is insufficient.

Action: revise. Either (a) provide explicit Layer 1 shortcoming evidence (e.g., "PostgreSQL event sourcing requires manual snapshot tooling at scale > X events/sec [SRC-Y]"), or (b) downgrade recommendation to PostgreSQL event sourcing (Layer 1 default). Layer 2 without Layer 1 shortcoming is a violation of the layer-discipline rule.

## References

- `rules/evidence-standards.md` — Source citation requirements
- `rules/pragmatism.md` — AI / multi-agent decision tree
- `skills/failure-mode-analysis` — AI-specific failure modes (Step 6)
- `skills/source-registry-management` — Source citation mechanics
- Survey-Corps report §12.1, §13.3 — Layer-A/B/C three-layer existence proof framework (foundational source)
- Survey-Corps report §1 — Layer-C honesty disclosure protocol
