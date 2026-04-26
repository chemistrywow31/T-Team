---
name: Adversarial Debate
description: Two-agent evidence-based debate methodology for stress-testing technical recommendations with concession protocol
---

# Adversarial Debate

## Purpose

Run a structured two-round debate between Solution Advocate (pro-feasibility) and Solution Skeptic (counter-evidence) to surface single-perspective blind spots before a recommendation reaches the user. Output is a debate summary documenting convergence points and unresolved divergence.

This skill is invoked exclusively during Phase 2.5 — Adversarial Debate, which fires only when one of the four trigger conditions is met (per Solution Coordinator workflow).

## When to Use

Invoke this skill when Solution Coordinator dispatches Phase 2.5. Triggers:

1. Solution Analyst's Phase 2 output recommends Level 4 multi-agent / orchestrator-worker architecture
2. The recommendation has high blast radius (production-affecting; > 10K users; financial / regulated workflow)
3. User explicitly asks for stress-test / counter-arguments / challenge
4. Solution Analyst self-flags Medium-or-below confidence on any analysis dimension

If none of the triggers fire, this skill is not invoked and Phase 2.5 is skipped.

## Method

### Step 0: Prerequisites

Before debate begins, verify:
- Source Registry exists at `.worklog/.../phase-2-analysis/source-registry.yaml`
- Solution Analyst's Phase 2 output is complete and confirmed by user
- Both Solution Advocate and Solution Skeptic have Read access to the registry

The debate transcript is written incrementally to `.worklog/.../phase-2-analysis/debate-summary.md`.

### Step 1: Round 1 — Opening

**Advocate** (dispatched first):
1. Read Solution Analyst's Phase 2 recommendation
2. Read the Source Registry
3. Produce an Opening Statement defending the recommendation:
   - State the recommendation in one sentence
   - List 3-5 strongest evidence pieces with `source_id` citations
   - Identify the specific use-case conditions under which the recommendation holds
4. Output max 600 words

**Skeptic** (dispatched second, with Advocate's Opening as input):
1. Read Solution Analyst's Phase 2 recommendation
2. Read the Source Registry
3. Read Advocate's Opening Statement
4. Produce a Rebuttal:
   - Identify the strongest counter-evidence (cite `source_id`)
   - List failure scenarios or edge cases the Advocate omitted
   - Challenge Advocate's specific evidence interpretations
   - Identify which Advocate claims rely on UNVERIFIED sources
5. Output max 600 words

### Step 2: Round 2 — Counter and Closing

**Advocate** (dispatched again, with Skeptic's Rebuttal as input):
1. Read Skeptic's Rebuttal
2. Produce a Counter-Rebuttal:
   - Concession requirement: explicitly acknowledge ≥ 1 valid Skeptic point. Begin with: "I concede the following point: {specific claim} because {reason}."
   - Defend remaining claims with additional evidence (new `source_id` citations allowed only if registered before debate)
   - Reframe the claim if necessary (narrow the scope, add conditions)
3. Output max 700 words

**Skeptic** (dispatched again, with Advocate's Counter as input):
1. Read Advocate's Counter
2. Produce a Closing Statement:
   - Concession requirement: explicitly acknowledge ≥ 1 valid Advocate point. Begin with: "I concede the following point: {specific claim} because {reason}."
   - State remaining objections clearly
   - Identify the residual unresolved divergence
3. Output max 700 words

### Step 3: Convergence and Divergence Synthesis

Solution Analyst (dispatched after both Round 2 outputs) produces the debate summary:

```markdown
# Debate Summary

## Trigger
{Which Phase 2.5 trigger fired, with reference}

## Convergence Points
List claims both Advocate and Skeptic agree on (typically 3-7 items). Each item:
- Statement
- Evidence sources both sides cite
- Layer (A / B / C)

## Unresolved Divergence
List claims still in dispute (typically 1-3 items). For each:
- The disputed claim
- Advocate position with evidence
- Skeptic position with evidence
- Resolution status: "Decision pending — awaiting user input on {specific tradeoff}"

## Recommendation Update
- Original recommendation: {one sentence}
- Updated recommendation: {one sentence, narrowed if needed}
- Conditions added by debate: {list}
```

### Step 4: User Confirmation

Solution Coordinator presents the debate summary to the user. The user resolves unresolved divergence by stating a position. The recommendation that goes to Phase 3 incorporates the debate's narrowing.

## Termination Conditions

Debate ends after Round 2 closing — no Round 3. This prevents runaway exploration.

If during Round 2 either agent fails to produce a concession (violating the concession requirement), Solution Coordinator marks the debate as "incomplete" and flags the issue for user attention rather than re-dispatching.

## Examples

### Normal case: Multi-agent framework recommendation

Input: Solution Analyst recommends LangGraph + Claude Agent SDK for an agentic customer support system.

**Round 1 Advocate (excerpt)**: "Recommendation: LangGraph v1.0 + Claude Agent SDK. Evidence: SRC-013 (47M+ monthly downloads, JP Morgan / BlackRock production), SRC-011 (4.2s p99 latency benchmark), SRC-002 (Anthropic-native MCP integration). Use-case fit: customer support with 5-domain specialization, 99.5% uptime target. Conditions: ROI ≥ 15× over single-shot chat is plausible given 2-week-to-2-day compression in resolution time."

**Round 1 Skeptic (excerpt)**: "Counter: Tech Radar Vol.34 [SRC-017] places 'Team of coding agents' at ASSESS, not ADOPT. Failure scenario: instruction bloat across 5 domains exceeds context window without RAG offload [SRC-017 CAUTION]. Advocate's ROI claim relies on the Anthropic 90.2% number which is [UNVERIFIED — internal evaluation, SRC-024]. Failure mode missing: silent quality degradation undetectable by HTTP monitoring [SRC-029]."

**Round 2 Advocate (excerpt)**: "I concede the following point: the Anthropic 90.2% claim is internal evaluation, so my ROI argument cannot rely on that figure as primary evidence. Reframed: ROI argument holds when the team independently measures resolution time and meets ≥ 15× compression. Defending the LangGraph recommendation with SRC-013 (47M+ monthly downloads, third-party verifiable) and SRC-011 (4.2s p99 from independent benchmark) which do not depend on UNVERIFIED data."

**Round 2 Skeptic (excerpt)**: "I concede the following point: LangGraph component-level production evidence (Layer-A) is established and not in dispute. Remaining objection: cross-5-domain integration is Layer-B (orchestration-pattern) which has Glean-style precedent [SRC-039], but the customer support domain mix proposed has no public production case — keeping this in Layer-C (Analyst inference). Recommendation should explicitly note this Layer-C condition."

**Convergence**: LangGraph + Claude Agent SDK is the right component stack (Layer-A confirmed). ROI argument requires independent measurement, not UNVERIFIED vendor numbers.

**Unresolved divergence**: Whether to start with 5 domains (Advocate) or 2-3 domains (Skeptic). Resolution: pending user input on team's risk tolerance.

### Edge case: User declines debate

User says "I trust the analysis, skip Phase 2.5." Solution Coordinator skips this skill entirely; consultation proceeds Phase 2 → Phase 3 directly. The skip is logged in the worklog with the user statement quoted.

### Rejection case: Concession failure

Advocate's Round 2 output begins with "Skeptic's points are all addressed by..." without an explicit concession statement. The skill's termination clause activates: Solution Coordinator marks the debate "incomplete — concession protocol violated" and surfaces this to the user. The unresolved analysis becomes the final recommendation rather than the debated version.

## References

- `rules/evidence-standards.md` — Source citation requirements during debate
- Survey-Corps report §3.1a — Adversarial debate mechanism specification
- Survey-Corps report §13 — Eren-Armin debate transcript (working example of two-round structure)
