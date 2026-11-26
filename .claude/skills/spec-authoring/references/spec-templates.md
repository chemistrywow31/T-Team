# Detailed Spec Templates

The four documents Phase 5 writes beside the ADRs and C4 diagrams. Drop a section that does not apply to this system and say why in the Architecture Spec; add one when the system needs it.

## Architecture Spec

```markdown
# Architecture Specification

## Overview
{One paragraph}

## C4 Level 1 — System Context
{Embed or link to c4-level-1-context.md}

## C4 Level 2 — Container
{Embed or link to c4-level-2-container.md}

## ADR Index
| ADR | Title | Status |
|-----|-------|--------|
| ADR-001 | {title} | Accepted |

## Non-functional Requirements

| Requirement | Target | Measurement Method | Source |
|------------|--------|-------------------|--------|
| Availability | {value} | {how measured} | [SRC-NNN] |
```

## API Specification

```markdown
# API Specification

## Base URL
{Base URL and versioning strategy}

## Authentication
{Auth method, token format, refresh flow — cite ADR}

## Endpoints

### {Method} {Path}

**Description**: {what it does}
**Auth**: {required role or scope}
**Source**: [SRC-NNN] (when behavior derives from a documented standard)

Request: {schema}
Response (200): {schema}

Error Responses:
| Code | Meaning | Response Body |
|------|---------|--------------|
```

## Database Schema

```markdown
# Database Schema

## Overview
{Database type, version, ADR reference for the choice}

## Tables / Collections

### {table_name}

| Column | Type | Constraints | Description |
|--------|------|------------|-------------|

**Indexes**:
- `idx_{name}` on `({columns})` — {rationale, ADR reference}

**Relationships**:
- {table_name}.{col} → {other_table}.{col} ({cardinality})

## Migration Strategy
{Approach — cite ADR}
```

## Deployment Plan

```markdown
# Deployment Plan

## Infrastructure

| Resource | Specification | Purpose | ADR |
|----------|--------------|---------|-----|

## Environment Configuration

| Variable | Description | Example |
|----------|------------|---------|

## CI/CD Pipeline
{Stages, triggers, approval gates}

## Monitoring & Alerting

| Metric | Threshold | Alert Action |
|--------|----------|-------------|

## Three-Tier Eval (when AI components present)

| Layer | Tool | Coverage |
|-------|------|----------|
| Layer 1 — Unit Evals | {tool} | {what's covered} |
| Layer 2 — LLM-as-Judge | {tool} | {what's covered} |
| Layer 3 — Production Sampling | {tool} | {sampling rate} |

## Rollback Strategy
{Concrete steps}
```
