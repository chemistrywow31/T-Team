---
name: Failure Mode Analysis
description: Systematic identification of realistic failure scenarios, including AI-agent-specific failure modes, with detection and recovery strategies
benefits-from:
  - "multi-dimensional-analysis"
---

# Failure Mode Analysis

## Purpose

Identify how each major component can fail under real-world conditions, assess cascade impact, detect silent failures, and produce a failure mode registry that feeds into the Risk Assessment dimension and Spec Writer deliverables.

T-Team adds a dedicated AI Agent Failure Modes section because AI-system failures are not detectable by standard APM (HTTP 200 with degraded output) and require purpose-built detection and recovery.

## When to Use

Use this skill during Phase 2 (Multi-dimensional Analysis) after the initial six-dimension evaluation, and during Phase 3 (Technology Selection) to compare failure characteristics of technology candidates. Always include the AI Agent Failure Modes section when consultation involves AI or agentic components.

## Method — Generic Components

### Step 1: Component Inventory

List every major component from the architecture (services, databases, queues, external APIs, caches, CDNs, auth providers). For each component, identify:
- Input sources (what feeds data into this component)
- Output consumers (what depends on this component's output)
- State ownership (what data does this component exclusively control)

### Step 2: Failure Scenario Generation

For each component, generate failure scenarios across these categories:

**Availability failures**: The component is unreachable or unresponsive
- Network partition between this component and its consumers
- Resource exhaustion (memory, disk, connections, file descriptors)
- Dependency unavailability (upstream service down)

**Data integrity failures**: The component produces incorrect output
- Stale cache serving outdated data
- Race conditions under concurrent writes
- Schema drift between producer and consumer

**Performance degradation**: The component responds but too slowly
- Slow query under unexpected data volume
- Connection pool exhaustion causing queuing
- Third-party API latency spikes

**Security failures**: The component is compromised or misconfigured
- Authentication bypass or token expiration handling
- Injection through unsanitized input
- Credential exposure in logs or error messages

### Step 3: Silent Failure Detection

For each failure scenario, answer: "If this fails right now, how long before someone notices?"

Classify detection latency:
- **Immediate** (< 1 minute): User-facing error, monitoring alert fires
- **Delayed** (1 minute - 1 hour): Detected by periodic health checks or log aggregation
- **Silent** (> 1 hour or never): No alert, no user-visible error, data quietly corrupts

Mark every Silent failure as **Critical**. These are the highest-priority items in the failure mode registry.

### Step 4: Cascade Analysis

For each failure scenario, trace the downstream impact:
1. Which components fail next if this component fails?
2. Does the cascade amplify (one failure causes N failures) or dampen (circuit breaker, fallback)?
3. What is the blast radius — how many users or business processes are affected?

Classify cascade risk:
- **Isolated**: Failure stays within this component
- **Contained**: 1-2 downstream components, circuit breakers limit spread
- **Cascading**: 3+ components or entire system

### Step 5: Failure Mode Registry

Produce a table for each component:

| Failure Scenario | Category | Detection | Cascade | Severity | Mitigation |
|-----------------|----------|-----------|---------|----------|------------|
| {scenario} | Availability / Data / Performance / Security | Immediate / Delayed / Silent | Isolated / Contained / Cascading | Critical / High / Medium / Low | {strategy} |

Severity rule:
- Silent + Cascading = **Critical**
- Silent + Contained = **High**
- Delayed + Cascading = **High**
- Immediate + Isolated = **Low**
- All other combinations = **Medium**

## Method — AI Agent Failure Modes

When the consultation involves AI / agentic / multi-agent components, additionally analyze these four AI-specific failure modes. Standard APM does NOT detect any of these.

### AI-FM-1: Tool-Call Schema Hallucination

**What fails**: The LLM calls a tool with invalid arguments — wrong parameter names, missing required fields, incorrect types — that the validation layer rejects.

**Detection**: Pydantic v2 (or equivalent typed schema validator) catches the invalid call; tool failure rate > 5% triggers Langfuse alert.

**Recovery**: Structured-output retry up to 3 attempts; on third failure, escalate to human or downgrade to a simpler tool.

**Prevention**: Every tool has 100% unit-test coverage of its schema; tool descriptions explicitly enumerate valid argument shapes.

### AI-FM-2: Context Window Truncation (State Corruption)

**What fails**: Conversation / state exceeds the model's context window. The model receives a truncated view, drops critical instructions or facts, and produces output as if the dropped content never existed.

**Detection**: Pre-call token count > 80% threshold triggers alert; LangGraph token-counter middleware logs near-limit calls.

**Recovery**: Agent Skills modular loading (load only the active domain's skill); RAG replaces static knowledge stuffing; Context Engineering reduces prompt bloat.

**Prevention**: Every subagent loads only its own domain skill; static knowledge is externalized to vector DB; instruction bloat is monitored per `pragmatism.md` decision-tree Q4.

### AI-FM-3: Retry Loop (Token Budget Exhaustion)

**What fails**: The agent retries the same failed action repeatedly, consuming token budget without progress.

**Detection**: Langfuse session token usage > budget threshold; counter for "same tool called consecutively N times" with N >= 3 triggers alert.

**Recovery**: Circuit breaker (LangGraph conditional edge) terminates retry; hand off to human; mark failure mode for retrospective.

**Prevention**: LangGraph `max_iterations` cap; `interrupt_before` / `interrupt_after` checkpoints; explicit failure recovery in agent prompts (no infinite "try harder" loops).

### AI-FM-4: Silent Quality Degradation

**What fails**: HTTP 200, latency normal, but the output's semantic quality is silently dropping. User sees plausible-looking output that is actually incorrect, biased, or missing critical content.

**Detection**: Three-tier evaluation:
- **Layer 1 — Unit Evals**: Assertion-based output tests run on every deployment
- **Layer 2 — LLM-as-Judge**: Rubric-driven scoring of output quality on representative samples
- **Layer 3 — Production Sampling**: Tail-based sampling (5%) including high-token, high-latency, error-adjacent traces (Langfuse pattern)

**Recovery**: Score below threshold triggers regenerate-with-tighter-constraint; orchestrator checkpoint reset; manual review.

**Prevention**: Eval dataset includes human-annotated ground truth; rubric is reviewed quarterly; production sampling rate is non-zero (even 5% catches drift).

### AI Failure Mode Registry

When AI components are present, the failure mode registry adds:

| Failure Mode | Detection | Recovery | Prevention | Three-Tier Eval Layer |
|--------------|-----------|----------|------------|------------------------|
| AI-FM-1 Tool-call schema | Pydantic / tool failure rate > 5% | Retry up to 3, then escalate | 100% tool unit-test coverage | Layer 1 |
| AI-FM-2 Context truncation | Token count > 80% threshold | Agent Skills modular load, RAG | Per-subagent skill scoping | Layer 1 + 2 |
| AI-FM-3 Retry loop | Same tool consecutive N >= 3 | Circuit breaker, human handoff | max_iterations, interrupt checkpoints | Layer 2 |
| AI-FM-4 Silent quality degradation | Three-tier eval | Regenerate, checkpoint reset | Eval dataset + sampling | Layer 2 + 3 |

This registry section is mandatory for any Phase 5 spec that includes AI components.

## Examples

### Normal case: Generic component (User Authentication Service)

Component: JWT-based, Redis session cache, PostgreSQL user store

| Failure Scenario | Category | Detection | Cascade | Severity | Mitigation |
|-----------------|----------|-----------|---------|----------|------------|
| Redis cache down — all sessions invalidated | Availability | Immediate (mass logout) | Cascading (every authenticated endpoint fails) | Critical | DB-fallback session validation; Redis TTL graceful degradation |
| JWT secret rotation — old tokens rejected | Data | Delayed (gradual user complaints) | Contained (only auth service) | Medium | Dual-key validation during rotation |
| Stale user permissions in cache after role change | Data | Silent (user retains revoked access) | Isolated (single user) | High | Event-driven cache invalidation; max cache TTL 5min for permissions |
| SQL injection via username field | Security | Silent (no error, data exfiltrated) | Cascading (full DB compromise) | Critical | Parameterized queries; WAF; auth-query audit log |

Critical items: Redis fallback strategy and input sanitization must be addressed in the Architecture Spec before development begins.

### Edge case: Multi-agent system AI failure registry

Component: 5-domain LangGraph orchestrator with Claude Agent SDK subagents, Qdrant RAG.

AI failure mode registry (mandatory addition):

| Failure Mode | Detection | Recovery | Prevention |
|--------------|-----------|----------|------------|
| AI-FM-1 Tool-call schema hallucination on Qdrant tool | Pydantic schema validation, alert at > 5% failure | Retry up to 3, then return "I couldn't retrieve that" | Qdrant tool description explicitly enumerates valid filter shapes |
| AI-FM-2 Context truncation in domain handoff | Token count > 80% threshold per subagent | Agent Skills load only the active domain | Each subagent loads its own skill; never the union |
| AI-FM-3 Retry loop on RAG retrieval | Same query repeated N >= 3 | Circuit breaker; hand off to human routing | max_iterations=10 per subagent |
| AI-FM-4 Silent quality degradation in domain answer | Langfuse 5% sample with LLM-as-Judge | Regenerate with tighter constraint; human review for low-score | Quarterly rubric review; ground-truth dataset for each domain |

This registry feeds into the Three-Tier Eval section of the Phase 5 spec.

### Rejection case: AI failure modes omitted from AI-system spec

Spec includes a multi-agent customer support system. Failure mode registry contains only generic component failures (Redis, PostgreSQL). No AI Agent Failure Modes section.

Spec Reviewer rejects: "AI components present; AI Agent Failure Modes section is mandatory per `failure-mode-analysis` skill. Add AI-FM-1 through AI-FM-4 with detection / recovery / prevention specific to this system."

Spec Writer adds the four AI failure modes and re-submits.

## References

- `skills/multi-dimensional-analysis` — Step 6 invokes this skill for AI consultations
- `rules/evidence-standards.md` — Source citation for failure mitigations
- Survey-Corps report §5 Q-C — Four AI-specific failure modes specification
- Survey-Corps report §14.4 — R-Phase4-3 silent quality degradation threat to consulting credibility
- Survey-Corps report §14.4a — Instruction Bloat three-layer solution (Agent Skills + RAG + Context Engineering)
