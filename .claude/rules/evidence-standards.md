---
name: Evidence Standards
description: What every T-Team claim must cite, how much weight a source may carry, and when a deliverable needs a Recency Warning or UNVERIFIED tag
paths:
  - ".worklog/**"
---

# Evidence Standards

Applies to analysis, debate, spec writing and spec review. The mechanics (registry fields, the credibility formula and its component scales, the recency summary) are in `.claude/skills/source-registry-management/SKILL.md`.

## Cite a registered source

Every technology recommendation, performance claim, market figure and architectural assertion cites a `source_id` from `phase-2-analysis/source-registry.yaml`. Phase 1 may run before the registry exists; it starts in Phase 2. Level 1-3 consultations may keep one line per source instead of the full YAML entry.

When the source is your own trained knowledge, it is not a source. For a current-generation, version or pricing claim in a `rapid_change` field, check a live source and register it before asserting the claim. In a 2026-07 consultation the analyst asserted an outdated model generation from memory and the user caught it, which cost a round.

User-supplied artifacts are registered with `publication: internal` and a credibility of 4.0, lower if the user doubts them.

## Credibility bands

| Band | Score | Use |
|------|-------|-----|
| High | 4.0 and up | May carry a recommendation alone |
| Medium | 3.0-3.9 | Must be paired with a High source |
| Low | 2.0-2.9 | Context only; never drives a decision |
| Reject | below 2.0 | Do not cite |

When a Low source contradicts a High one, the High source wins. Rescore a source when you downgrade it to background.

## Recency Warning

Put this at the top of the deliverable when the source set misses its field's targets: `rapid_change` 70% within 6 months and 90% within 12; `evolving` 40% and 70%; `stable` 50% within 12 months.

```
> **Recency Warning**: This consultation relies in part on sources older than the target horizon for `{classification}`.
> - X% of sources within 6 months (target Y%) — {met/not met}
> - X% of sources within 12 months (target Y%) — {met/not met}
> Statistical, market, pricing and trend data use sources within 12 months only. Conclusions may not reflect conditions as of {today}.
```

The warning does not block delivery. A foundational methodology source (Nygard's ADR essay, the C4 model, a technique's original paper) may skip it when cited only for the method; mark the entry `exception: foundational`. It never covers benchmarks, pricing or trends.

## UNVERIFIED tag

A self-reported number, a vendor benchmark nobody reproduced, or a claim that cannot be checked carries a tag in the same sentence:

```
[UNVERIFIED — vendor self-report] LangGraph achieves 4.2 second p99 latency on 3-agent workloads [SRC-013].
```

Stating such a claim as fact is the error this rule exists to stop.
