# Spec Format

A spec lives at `docs/spec/<feature-slug>.md`, one file per feature. `ss-ticket` reuses the slug for the ticket folder.

## Template

```md
# Spec: <name>

- **Date:** YYYY-MM-DD
- **Source:** <the settled grilling decisions, plus `docs/prd.md` when it covers the same feature>

## Problem Statement

The problem, from the user's perspective.

## Solution

The solution, from the user's perspective.

## User Stories

A LONG, numbered list covering every aspect of the work. Each in the form:

1. As an <actor>, I want a <feature>, so that <benefit>

## Implementation Decisions

A list of the decisions already made: modules built or modified, their interfaces, architectural choices, schema changes, API contracts, specific interactions. No specific file paths or code snippets — they go stale fast. Exception: if a prototype produced a snippet that encodes a decision more precisely than prose (state machine, reducer, schema, type shape), inline it and note it came from the prototype; keep only the decision-rich parts.

## Testing Decisions

- What makes a good test here (external behavior, not implementation details).
- Which modules are tested, and the seams.
- Prior art: similar existing tests to follow.

## Out of Scope

What was deliberately refused or deferred. These are usually the most useful lines on the page.

## Further Notes

Anything else a fresh session needs to pick up the work.
```

## Rules

- Use the template exactly: the same headings in the same order, nothing added or renamed.
- A section with no settled decision behind it holds only "None decided."
- The spec is a decision record for a fresh session: it assumes no shared context.
- Inside a section, use lists, tables, bold lead-ins and code blocks, never extra headings.
- No line numbers: they go stale with the next edit. Point at a Skill or a section by name.
