# PRD Format

The PRD lives at `docs/prd.md`. One PRD per repo at a time.

## Template

```md
# PRD: <feature name>

- **Date:** YYYY-MM-DD
- **Source:** <grilling session date, GLOSSARY.md, ADR paths>

## Problem

The problem the user faces, from the user's perspective.

## Goals / Non-Goals

What this feature does, and what it deliberately does not do.

## Users / Actors

Who uses it and in what role.

## Requirements

Numbered. Each checkable: a reviewer can tell done from not-done. A requirement about data, endpoints or pages states the rule and links the companion section for the shape (`see [schemas.md#<table>](schemas.md#<table>)`).

## Acceptance Criteria

Numbered, testable. Each independently verifiable — a test or a manual check that can be run without reading code.

## Constraints / Assumptions

What was fixed by the interview or by the environment, not discovered during implementation.

## Companion Docs

- [Schemas](schemas.md) — tables, columns, keys
- [API list](api-list.md) — endpoints
- [Page routes](page-routes.md) — routes and pages

## Changelog

<!-- newest first -->

- **YYYY-MM-DD** — <one line: what changed and why>. <new feature context / new decision / requirement N revised / schemas.md: table X added>
```

## Rules

- Use the template exactly: the same headings in the same order, nothing added or renamed.
- A section with no settled decision behind it holds only "None decided."
- `Date` is the date of the latest write. `Source` names what the decisions came from.
- Inside a section, use lists, tables, bold lead-ins and code blocks, never extra headings.
- `Companion Docs` lists only the companions chosen for this repo's kind. When none is chosen, it holds only "None: <reason from the environment>."
