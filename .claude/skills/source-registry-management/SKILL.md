---
name: Source Registry Management
description: Build and maintain the per-consultation source registry with credibility weighting, recency tracking, and UNVERIFIED tagging
---

# Source Registry Management

## Purpose

Construct and maintain the Source Registry that grounds every T-Team recommendation in cited evidence. The registry is the single authoritative location for sources used in a consultation; all agents read from it and cite by `source_id`.

This skill is invoked by Solution Analyst, Solution Advocate, Solution Skeptic, and Spec Writer whenever they introduce a new technology claim, performance figure, market datum, or architectural assertion.

## When to Use

Invoke this skill at three junctures:

1. **Phase 2 Multi-dimensional Analysis** — Every external claim referenced during analysis is registered before being used.
2. **Phase 2.5 Adversarial Debate** — Every piece of evidence cited by Advocate or Skeptic is registered first; debate may not introduce un-registered evidence.
3. **Phase 5 Spec Authoring** — Every assertion in spec deliverables traces back to a registered source.

## Method

### Step 1: Locate the Registry

The registry lives at `.worklog/{yyyymm}/{task-name}/phase-2-analysis/source-registry.yaml`. If the file does not exist (Phase 2 just starting), create it with an empty `sources:` list.

### Step 2: Determine Field Classification

Before scoring credibility, classify the source's field:

| Classification | Examples | Recency targets |
|----------------|----------|-----------------|
| `rapid_change` | AI tooling, agent frameworks, vector DBs, frontend frameworks, LLM models, observability tools, message queues, cloud-native platforms | 70% within 6 months, 90% within 12 months |
| `evolving` | DevOps practices, design systems, programming language major versions, ORM patterns | 40% within 6 months, 70% within 12 months |
| `stable` | Relational database fundamentals, classical algorithms, RFC-defined protocols, foundational papers | No specific target |

When uncertain, default to `evolving`. Foundational papers (ADR original, C4 model, CoVe paper) are `stable` and may bypass recency warnings via the `exception: foundational` field.

### Step 3: Score Credibility

Apply the formula from `rules/evidence-standards.md`:

For `rapid_change`: `credibility = (Author × 0.15) + (Publication × 0.15) + (Recency × 0.50) + (Corroboration × 0.20)`

For `stable`: `credibility = (Author × 0.30) + (Publication × 0.30) + (Recency × 0.10) + (Corroboration × 0.30)`

Score each component 0.0-5.0:

- **Author Authority**: Recognized expert / org maintainer (5.0); domain practitioner (4.0); credentialed but unknown (3.0); anonymous / pseudonymous (2.0); contradicted by domain (1.0)
- **Publication Reputation**: Tier-1 academic / vendor primary docs (5.0); reputable industry blog / conference talk (4.0); developer blog with traction (3.0); personal blog (2.0); content farm (1.0)
- **Recency**: Within 1 month (5.0); 1-3 months (4.5); 3-6 months (4.0); 6-12 months (3.0); 1-2 years (2.0); 2+ years (1.0); foundational exception (5.0)
- **Corroboration**: ≥ 3 independent confirming sources (5.0); 2 independent confirming (4.0); 1 confirming (3.0); contested (2.0); contradicted by ≥ 2 sources (1.0)

### Step 4: Tag UNVERIFIED Claims

Read the source carefully for self-reported numbers. Flag the specific claims that lack independent reproduction:

- Vendor benchmarks not reproduced by third parties
- Self-attested customer numbers ("trusted by 500+ enterprises")
- Internal evaluation results (e.g., "+90% performance" without external audit)
- Market size estimates from the vendor's own analyst reports
- ROI or productivity claims based on internal case studies only

Add to `unverified_flags` with the specific claim text. Recommendations citing this source must wrap the affected claim with `[UNVERIFIED — vendor self-report]` or similar.

### Step 5: Compute Recency Summary

After Phase 2 source collection completes, compute:

```yaml
recency_summary:
  field_classification: rapid_change
  total_sources: 28
  pct_within_6_months: 60
  pct_within_12_months: 86
  warning_required: true   # any target missed
```

If `warning_required: true`, the consultation deliverable must include a Recency Warning header (per `evidence-standards.md`).

### Step 6: Cite by source_id

When referencing a source in any T-Team output, cite by `source_id` only. Examples:

- "LangGraph achieves 4.2s p99 latency [SRC-013]"
- "Tech Radar Vol.34 ranks Team of coding agents as ASSESS [SRC-017]"
- "[UNVERIFIED — vendor self-report] Devin claims 67% PR merge rate [SRC-025]"

Do not inline source URLs or descriptions in deliverables. The registry is the single source of truth — citations are pointers.

## Examples

### Normal case: Adding a high-credibility source

User says: "I read that LangGraph is the production-leading multi-agent framework. Source: https://blog.langchain.dev/langgraph-v1/"

Registry entry:
```yaml
- source_id: SRC-013
  url: https://blog.langchain.dev/langgraph-v1/
  author: "LangChain Team"
  publication: "LangChain Official Blog"
  date: 2025-10-22
  field_classification: rapid_change
  age_at_access: 186
  credibility_score: 4.2
  key_claims:
    - "LangGraph v1.0 GA"
    - "47M+ monthly downloads"
  unverified_flags: []
```

Use in deliverable: "LangGraph v1.0 (GA 2025-10-22, 47M+ monthly downloads) [SRC-013]."

### Edge case: Foundational source older than recency target

User cites Michael Nygard's 2011 ADR essay. Age at access: 5475 days. Field: `stable` (foundational methodology).

Registry entry:
```yaml
- source_id: SRC-015
  url: https://cognitect.com/blog/2011/11/15/documenting-architecture-decisions
  author: "Michael Nygard"
  publication: "Cognitect Blog"
  date: 2011-11-15
  field_classification: stable
  age_at_access: 5275
  credibility_score: 4.0
  key_claims:
    - "ADR format: Status / Context / Decision / Consequences"
  unverified_flags: []
  exception: foundational
```

The `exception: foundational` allows citation without triggering a Recency Warning. Use only for the methodology — not for current benchmarks or pricing.

### Rejection case: Self-reported vendor metric without audit

Source: "Vendor X claims 90.2% performance improvement on internal evaluation."

Registry entry includes:
```yaml
unverified_flags:
  - "90.2% performance improvement is self-reported on internal evaluation; no third-party reproduction."
```

Use in deliverable: "[UNVERIFIED — vendor self-report] Vendor X reports a 90.2% performance improvement on internal evaluation [SRC-024]. Production reproduction has not been independently confirmed."

Forbidden: stating "Vendor X improves performance by 90.2% [SRC-024]" without the UNVERIFIED tag.

## References

- `rules/evidence-standards.md` — The rule this skill implements
- Survey-Corps report §3.1a — Original source registry methodology
- Survey-Corps report Appendix A — Working example of source registry with UNVERIFIED tags
