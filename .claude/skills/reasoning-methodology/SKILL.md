---
name: Reasoning Methodology
description: Catalog of LLM reasoning techniques (ToT, CoVe, Self-Consistency, ReAct, Plan-and-Solve, Reflexion) with three application scenarios for Level 4 consultations
---

# Reasoning Methodology

## Purpose

Apply structured reasoning techniques to high-stakes consultation outputs to raise quality and reduce hallucination. Solution Analyst invokes this skill when consultation is Level 4 (multi-agent / agentic recommendation) or when the output contains specific facts that downstream agents will rely on (numbers, version IDs, API names, citations).

This skill does NOT replace ordinary reasoning. It supplements it for the specific scenarios listed below.

## When to Use

Three specific scenarios trigger this skill:

| Scenario | Triggering condition | Combo |
|----------|----------------------|-------|
| Architecture Decision (ADR) | User's question forces a multi-option architecture choice | ReAct → ToT → Self-Consistency → CoVe |
| Technology Selection Trade-off | Comparing 2-3 candidates across multiple dimensions | Plan-and-Solve → Multi-Persona → Self-Refine → Reflexion |
| Production Case Verification | Output cites specific production cases, benchmarks, or numbers | ReAct → CoVe → Self-Consistency |

## Methodology Catalog

### Tree of Thoughts (ToT) — [MOB-001]

Generate N parallel branches; evaluate; prune; continue from best. Use for multi-option decisions (architecture choices, tech selections). Gain: Game of 24 74% vs CoT 4% (18.5x). Cost: 5-20x tokens.

Pattern: (1) generate 3-5 candidate paths; (2) evaluate each on criteria (correctness, fit, risk); (3) prune below threshold; (4) continue from highest-scoring; (5) stop on complete decision.

### Chain of Verification (CoVe) — [MOB-003]

Draft → list verification questions → answer each independently → revise. Use when output has specific facts (numbers, API names, version IDs, citations). Significant hallucination reduction on Wikidata / MultiSpanQA.

Pattern: (1) draft the answer; (2) generate verification questions; (3) answer each independently (no draft access); (4) compare to draft; (5) revise contradictions or flag for user.

### Self-Consistency — [MOB-009]

Sample N answers at non-zero temperature; majority-vote. Use when output space is small (yes/no, list, numerical). Gain: GSM8K +17.9%, SVAMP +11.0%. Cost: Nx tokens.

Pattern: (1) generate N independent chains; (2) extract final answer from each; (3) majority vote; (4) if no clear majority (less than or equal to 50%), flag as ambiguous to user.

### ReAct — [MOB-007]

Interleave think → act → observe → think in a single trace. Use when output requires retrieving external facts (RAG, web, file reads). Foundation pattern for tool-using agents — T-Team agents use ReAct implicitly via Claude Code's tool loop.

Pattern: (1) think "I need X"; (2) act (invoke tool); (3) observe result; (4) think "given X, next is Y"; (5) repeat.

### Plan-and-Solve — [MOB-005, MOB-008]

First produce an explicit numbered plan, then execute. Use when the question has ordered sub-questions.

Pattern: (1) read question; (2) numbered plan; (3) state plan to coordinator; (4) execute step by step; (5) report deviations.

### Reflexion — [MOB-004]

After failure, write self-critique, store in memory, retry with critique. Use when eval score below threshold or user rejected.

Pattern: (1) detect failure; (2) write self-critique; (3) store in worklog findings.md; (4) retry with critique as context; (5) cap retries at 2 — third failure escalates.

## Combo Recipes

### Combo 1 — Architecture Decision (ADR generation)

```
Step 1. ReAct: retrieve relevant data from Source Registry and external sources
Step 2. ToT: explore 3-5 candidate architectures; evaluate each on (a) Layer-A/B/C evidence,
        (b) failure-mode coverage, (c) team-fit, (d) reversibility
Step 3. Self-Consistency: regenerate the leading candidate's trade-off analysis 3 times;
        confirm consistency across runs
Step 4. CoVe: verify all specific claims in the ADR (numbers, citations, version IDs)
        against the Source Registry; revise contradictions
Step 5. (Phase 6) LLM-as-Judge eval rubric: confirm ADR contains Consequences + Trade-offs
        + Alternatives sections per `adr-c4-authoring` skill
```

### Combo 2 — Technology Selection Trade-off

```
Step 1. Plan-and-Solve: decompose "framework selection" into sub-questions
        (maturity, performance, integration, cost, community)
Step 2. Multi-Persona: produce two perspectives — pro-feasibility (Advocate-style) and
        skeptic (Skeptic-style). If Phase 2.5 fires, this becomes the actual debate;
        otherwise, do it inline as a thinking exercise.
Step 3. Self-Refine: iterate on the initial recommendation, focusing on weak points
        identified by the skeptic perspective
Step 4. Reflexion: if the recommendation fails internal review, capture the failure
        reason and retry with that context
```

### Combo 3 — Production Case Verification

```
Step 1. ReAct: retrieve cited cases from Source Registry and external sources
Step 2. CoVe: for every cited number / company / benchmark, generate a verification
        question; answer independently; flag UNVERIFIED if reproduction is missing
Step 3. Self-Consistency: re-retrieve key data points from independent sources;
        confirm consistency

Output: production case section with all citations carrying credibility scores and
UNVERIFIED tags where appropriate (per `evidence-standards.md`).
```

## Examples

### Normal case: Architecture decision via Combo 1

User asks: "Should we use LangGraph or CrewAI for our agentic workflow?"

Solution Analyst applies Combo 1:
- **ReAct**: retrieves SRC-013 (LangGraph 47M downloads), SRC-005 (CrewAI 5.2M downloads), SRC-011 (latency benchmark)
- **ToT**: generates 3 candidates: (a) LangGraph alone, (b) CrewAI alone, (c) LangGraph + CrewAI for prototype-to-prod handoff. Evaluates: (a) Layer-A strong, (b) Layer-A medium, (c) over-engineering. Prunes (c); continues with (a) vs (b).
- **Self-Consistency**: regenerates the trade-off table 3 times. Confirms each run produces same recommendation rank (LangGraph for production, CrewAI only for early prototyping).
- **CoVe**: verifies numbers cited (4.2s p99 from SRC-011, 47M downloads from SRC-013, +450 token overhead from SRC-005). All confirmed.

Output: ADR-002 recommending LangGraph for production with CrewAI as optional prototyping tool, fully cited.

### Edge case: Self-Consistency reveals ambiguity

User asks: "Should we use Postgres or MongoDB for product catalog?"

Solution Analyst applies Self-Consistency: 3 runs produce 2 votes for Postgres, 1 for MongoDB. Below the clear-majority threshold (≥ 67%).

Action: surface the ambiguity to the user. "Two of three reasoning runs favored PostgreSQL on grounds A/B/C; one favored MongoDB on grounds D. The decision hinges on {specific tradeoff} — please confirm your preference on this dimension before I commit to a recommendation."

### Rejection case: CoVe finds unverifiable claim

Draft output cites: "Vendor X processes 100M requests/second [SRC-201]."

CoVe verification: re-read SRC-201 for the specific number. SRC-201 says "Vendor X processes >50M requests/second according to internal benchmarks". The 100M figure was hallucinated.

Action: revise draft to "[UNVERIFIED — vendor self-report] Vendor X reports >50M requests/second on internal benchmarks [SRC-201]." Mark CoVe-revised in the worklog.

## References

- Survey-Corps report §9.1 — Quantitative gain summary for each technique
- Survey-Corps report §9.4 — Three application scenarios specification
- Survey-Corps report §9.5 — 2026 reasoning-model boundary (when explicit prompting is no longer needed)
- ToT — Yao et al. 2023 [MOB-001]
- CoVe — Meta AI 2023 [MOB-003]
- Self-Consistency — Wang et al. 2022 [MOB-009]
- ReAct — Yao et al. 2022 [MOB-007]
- Plan-and-Solve — Wang et al. 2023 [MOB-005, MOB-008]
- Reflexion — Shinn et al. 2023 [MOB-004]
