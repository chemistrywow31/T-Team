---
name: Technology Comparison
description: Structured methodology for comparing technology candidates and selecting the most pragmatic option
---

# Technology Comparison

## Purpose

Provide a repeatable method for comparing 2–3 technology candidates for a given component, using the six analysis dimensions as evaluation criteria, and selecting the option that maximizes stability and pragmatism.

## When to Use

Use this skill during Phase 3 (Technology Selection) when the Solution Analyst needs to evaluate concrete technology options for a specific component (e.g., database, framework, hosting platform, message broker).

## Method

### Step 1: Identify Candidates

Select 2–3 realistic candidates using the three-layer knowledge search:
1. **Layer 1 (Battle-tested)**: Start with proven, widely-deployed solutions. These are the default candidates.
2. **Layer 2 (Current best)**: Add a candidate from current best practices only if Layer 1 options have documented gaps for the stated requirements.
3. **Layer 3 (First principles)**: Consider unconventional approaches only when Layers 1–2 demonstrably fail the requirements.

Rules:
- Every candidate must have production-grade maturity (no alpha/beta unless no alternative exists).
- Every candidate must be compatible with the team's existing skill set or have a learning curve under 2 weeks.
- Do not add a third candidate solely for comparison. Two candidates are sufficient if the choice is clear.

### Step 2: Build Comparison Matrix

Evaluate each candidate across seven dimensions, scoring each as Strong / Adequate / Weak:

| Criterion | Candidate A | Candidate B | Candidate C |
|-----------|------------|------------|------------|
| Technical Feasibility | {score + note} | {score + note} | {score + note} |
| Performance & Scalability | {score + note} | {score + note} | {score + note} |
| Security | {score + note} | {score + note} | {score + note} |
| Cost | {score + note} | {score + note} | {score + note} |
| Maintainability & Team Fit | {score + note} | {score + note} | {score + note} |
| Lock-in & Switching Cost | {score + note} | {score + note} | {score + note} |
| Risk | {score + note} | {score + note} | {score + note} |

**Lock-in & Switching Cost** evaluates:
- Vendor dependency: Is the solution tied to a specific vendor's proprietary APIs or infrastructure?
- Data portability: How difficult is it to export data to another system?
- Migration effort: If requirements change, what is the estimated effort to switch to an alternative?
- Standards compliance: Does the solution use open standards or proprietary formats?

### Step 3: Pragmatism Check

For the leading candidate, verify:
1. Is this the simplest option that meets all stated requirements?
2. Does the team already know this technology, or is the learning investment justified?
3. Are we choosing this because of current needs or speculative future needs?
4. Is there a simpler alternative we dismissed too quickly?

If the simpler option meets all requirements, recommend the simpler option even if the complex option scores higher on some dimensions.

### Step 4: Recommendation

State the recommendation with:
- Which candidate and why (one paragraph)
- **Completeness score** (1–10): Rate the implementation completeness this candidate enables within the stated timeline. 10 = full implementation including edge cases and error handling; 7 = happy path plus some edge cases; 3 = shortcut that defers significant work. When AI-assisted development makes completeness cheap, prefer the candidate that enables a higher completeness score.
- Key trade-off accepted (what are we giving up)
- Migration path (if requirements change later, how hard is it to switch)

## Examples

### Normal case: Primary database for a content management system

Input: 50K articles, 500 concurrent readers, full-text search, team knows PostgreSQL.

Output:

| Criterion | PostgreSQL + pg_trgm | MongoDB + Atlas Search | Elasticsearch + PostgreSQL |
|-----------|---------------------|----------------------|---------------------------|
| Feasibility | Strong — full-text via pg_trgm/tsvector | Strong — native search | Strong — best search quality |
| Performance | Adequate — sufficient for 500 concurrent | Adequate — similar scale | Strong — optimized for search |
| Security | Strong — mature auth model | Adequate — Atlas handles basics | Adequate — requires careful config |
| Cost | Strong — single DB, no extra service | Adequate — Atlas fees apply | Weak — two systems to maintain and pay for |
| Maintainability | Strong — team already knows it | Weak — team must learn MongoDB | Weak — two systems, new expertise needed |
| Lock-in & Switching | Strong — open source, standard SQL | Weak — Atlas proprietary APIs, data export requires tooling | Adequate — Elasticsearch is open source but adds operational dependency |
| Risk | Strong — proven, minimal unknowns | Adequate — vendor lock-in to Atlas | Adequate — operational complexity |

**Recommendation**: PostgreSQL with tsvector full-text search. The team already knows PostgreSQL, it handles the stated scale, and it avoids introducing a second data store. Full-text search quality is sufficient for 50K articles — Elasticsearch would be over-engineering at this scale.

**Completeness score**: 9/10 — PostgreSQL tsvector covers full-text search, ranking, highlighting, and language-specific stemming out of the box. Only advanced features like fuzzy matching across languages would require additional work.

**Trade-off accepted**: Search relevance ranking is less sophisticated than Elasticsearch. This is acceptable for 50K articles.

**Migration path**: If search requirements grow beyond PostgreSQL's capabilities, add Elasticsearch as a read-only search index without changing the primary database.

### Edge case: Two viable candidates with one decisive non-functional axis

Input: Message queue for an order-processing pipeline. Candidates: Kafka 4.1, RabbitMQ 4.5. Both meet throughput. Decisive axis: ordering guarantees (Kafka strong; RabbitMQ per-queue only).

Output: Use RabbitMQ when ordering is per-customer-queue (which the order pipeline allows). Recommend Kafka when global event-time ordering matters across all events. Neither is over-engineering at the stated 50K msg/sec; the choice is shape-fit, not scale-fit.

### Rejection case: Three "candidates" but two are strawmen

Draft analysis lists PostgreSQL, Cassandra, and Neo4j as candidates for a 5K-record relational dataset. Cassandra and Neo4j are strawmen — neither fits the access pattern.

Action: drop the strawmen. Use this skill's Step 1 rule "Do not add a third candidate solely for comparison. Two candidates are sufficient if the choice is clear." The recommendation is PostgreSQL with no further comparison needed.
