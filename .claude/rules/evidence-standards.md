---
name: Evidence Standards
description: Enforce source registry, credibility weighting, recency tracking, and UNVERIFIED tagging for all technical recommendations
---

# Evidence Standards

## Applicability

- Applies to: Solution Analyst, Solution Advocate, Solution Skeptic, Spec Writer, Spec Reviewer

## Rule Content

### Every Recommendation Cites a Registered Source

Every technology recommendation, performance claim, market figure, and architectural assertion in T-Team output must reference a source registered in the consultation's Source Registry. Recommendations without a registered source are violations.

The Source Registry lives at `.worklog/{yyyymm}/{task-name}/phase-2-analysis/source-registry.yaml` (per `worklog.md` artifact templates) and is created during Phase 2.

### Mandatory Source Registry Fields

Each registry entry must contain:

| Field | Required | Description |
|-------|----------|-------------|
| `source_id` | yes | `SRC-NNN` format, sequential per consultation |
| `url` | yes | Direct URL or document path; `internal` allowed for user-supplied artifacts |
| `author` | yes | Person, organization, or `unknown` |
| `publication` | yes | Journal, blog, vendor, or `internal` |
| `date` | yes | YYYY-MM-DD of publication |
| `field_classification` | yes | `rapid_change` / `evolving` / `stable` |
| `age_at_access` | yes | Days between publication and consultation date |
| `credibility_score` | yes | 0.0-5.0 (see weighting below) |
| `key_claims` | yes | Bulleted list of specific claims drawn from this source |
| `unverified_flags` | optional | Specific claims within the source that are self-reported / unaudited |

### Credibility Weighting Formula

For `rapid_change` fields (AI tooling, frontend frameworks, vector DBs, observability, message queues, agent frameworks):

```
credibility = (Author Authority × 0.15)
            + (Publication Reputation × 0.15)
            + (Recency × 0.50)
            + (Corroboration × 0.20)
```

Each component scored 0.0-5.0. Recency weight is dominant for `rapid_change` because half-lives in these fields are short.

For `stable` fields (relational databases, classical algorithms, foundational protocols), use:

```
credibility = (Author Authority × 0.30)
            + (Publication Reputation × 0.30)
            + (Recency × 0.10)
            + (Corroboration × 0.30)
```

### Credibility Bands

| Band | Score | Use |
|------|-------|-----|
| High | ≥ 4.0 | Primary evidence — recommendations may rely on this source alone |
| Medium | 3.0-3.9 | Supporting evidence — must be combined with at least one High source |
| Low | 2.0-2.9 | Background only — must not drive decisions; cite only for context |
| Reject | < 2.0 | Do not cite |

A Low source paired with a High source that contradicts it is treated as contradicted — the High source wins.

### Recency Warning Protocol

When the consultation's source set fails recency targets, an explicit Recency Warning must appear at the top of the deliverable:

| Field classification | 6-month target | 12-month target |
|----------------------|----------------|------------------|
| `rapid_change` | 70% of sources | 90% of sources |
| `evolving` | 40% of sources | 70% of sources |
| `stable` | (no specific target) | 50% of sources |

Format:
```
> **Recency Warning**: This consultation relies in part on sources older than the target horizon for `{classification}` ({m}/{n} months).
> - X% of sources within 6 months (target Y%) — {met/not met}
> - X% of sources within 12 months (target Y%) — {met/not met}
> Statistical / market / pricing / trend data uses sources within 12 months only. Conclusions may not fully reflect conditions as of {today}.
```

The warning is informational — it does not block the deliverable. It does shift downstream consumers' expectation of confidence.

### Foundational Exception

A source older than the recency target may bypass the warning when ALL conditions hold:

1. The source is foundational methodology (ADR-original, C4 model, Conway's Law paper, etc.)
2. The source is cited only for the methodology, not for current benchmarks / pricing / trends
3. The exception is logged in the registry entry with `exception: foundational`

### UNVERIFIED Tagging

When a source contains self-reported numbers, vendor-supplied benchmarks without third-party reproduction, or claims that cannot be independently checked, the recommendation citing that source must wrap the relevant claim with a UNVERIFIED tag:

```
[UNVERIFIED — vendor self-report] LangGraph achieves 4.2 second p99 latency on 3-agent workloads.
```

Forbidden: presenting UNVERIFIED claims as established fact. The tag must precede or follow the claim in the same sentence.

### Source Registry as Phase Gate

Solution Coordinator must verify the Source Registry exists and contains entries before transitioning from Phase 2 to Phase 3. A consultation entering Phase 3 with an empty or missing Source Registry is a violation by the coordinator.

## Violation Determination

- Recommendation cites no source → Violation
- Source Registry missing required fields (R1 §3.1a list) → Violation
- Source registered with `credibility_score` below 2.0 used as primary evidence → Violation
- Recency Warning required but absent from deliverable header → Violation
- UNVERIFIED claim presented without the UNVERIFIED tag → Violation
- Foundational exception used for current benchmarks / pricing / trends data → Violation
- Phase 2 → Phase 3 transition without Source Registry presence → Violation by coordinator

## Exceptions

- Phase 1 Discovery may proceed without Source Registry — discovery establishes the consultation; the registry begins in Phase 2.
- User-supplied artifacts (internal documents, prior decision records) are registered with `publication: internal`, `credibility_score: 4.0` by default; lower if user flags reliability concerns.
- Level 1-3 consultations may use a lightweight one-line-per-source registry format instead of the full yaml structure.

Tradeoff: Source Registry adds 15-30 minutes per consultation for registry maintenance. The cost is justified for Level 4 consultations where downstream cost of a wrong recommendation is hours-to-weeks.
