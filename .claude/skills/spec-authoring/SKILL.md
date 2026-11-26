---
name: Spec Authoring
description: Write the Phase 5 spec of a T-Team consultation — ADRs, C4 diagrams, Tech Radar rings, and the architecture, API, database and deployment documents — from the confirmed Phases 1-4, then self-check it before review
---

# Spec Authoring

Phase 5 turns everything the user confirmed in Phases 1-4 into a spec someone else can implement without making design decisions. It runs without the user; Phase 6 reviewers check it next. Write to `phase-5-spec/`.

Read the confirmed outputs first: Requirements Summary, Analysis Report, Technology Selection, UX Design Spec, the source registry, and `debate-summary.md` when Phase 2.5 ran. The spec records decisions; it does not make new ones. If you find a decision nobody made, send it back to Phase 3 with the user instead of choosing.

## Steps

1. List every architecture decision from the selection matrix and the UX spec, numbered from ADR-001.
2. Write one ADR per decision with `adr-c4-authoring` Part 1. Keep each to a single decision: a 2026-07 review sent back an ADR that bundled two.
3. Draw C4 Levels 1-3 with Part 2, each with a legend. If a level would only repeat the one above (a single-process CLI, say), skip it and record why in `decisions.md`.
4. Tag every technology with its Tech Radar ring (Part 3) in the ADRs, the C4 legends and the spec summary.
5. Write the architecture, API, database and deployment documents from `references/spec-templates.md`. When AI components are present, the deployment plan includes the three-tier evaluation table.

Use exact values ("p95 under 200ms", not "fast"). Mark anything left open as `[TODO: {description}]` and list the TODOs in one summary section.

## Self-check before review

- Every ADR Decision names at least one C4 element, and every C4 Level 2 container has an ADR.
- Every API endpoint uses tables that exist in the schema; every environment variable the specs mention is in the deployment plan.
- Technology choices match the confirmed selection; non-functional targets match the Analysis Report.
- The same names, versions and protocols appear in every file. In that review, a config example said `http://` while the C4 diagram said HTTPS, and the CLI command table disagreed with the menu.
- Every assertion cites a `source_id`, and each cited source's key claim actually supports the sentence citing it.
- UNVERIFIED claims are tagged; the Recency Warning and the Layer-C disclosure are present when required.
