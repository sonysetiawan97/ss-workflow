---
name: ss-spec
description: "Turn settled context into a written spec. Use when the user wants a spec after a ss-grilling session, or says write the spec. Sources: the grilling session's decisions, docs/prd.md when it matches the feature being specced, and codebase context. No interview - synthesizes what is already decided into docs/spec/<feature-slug>.md."
---

Take the settled decisions from the grilling session and the codebase, and produce a spec at `/docs/spec/<feature-slug>.md` (kebab-case feature name; `ss-ticket` reuses it for the ticket folder). One file per feature: never overwrite another feature's spec; update the file in place when it already covers this feature. Do NOT interview the user; just synthesize what is already decided. The spec records decisions that were made, in the project's own vocabulary. If the spec states something nobody decided, that is a defect — flag it to the user instead of filling it in yourself.

## Inputs

1. **The grilling session's settled decisions** — the primary source: the design tree as it was resolved in the current conversation, plus anything written to `GLOSSARY.md` and `docs/adr/` during the session.
2. **`/docs/prd.md`** — the secondary source, used only when the PRD describes the **same feature context** as the session being specced. Check: does the PRD's feature name, problem statement, and requirements match what was grilled? If the PRD covers a different feature (or the grilling introduced a new context the PRD doesn't have), do not pull from it: the spec stands on the session's decisions alone, and the PRD should be updated by `ss-prd` instead. If the PRD matches, it supplies user stories, requirements, and out-of-scope lines the session may not have re-stated.
3. **The codebase** — current state, seams, prior art.

When gathering codebase context for the spec and `graphify-out/graph.json` exists, answer structural questions with `graphify query "<question>"` before falling back to file reads or grep.

## Process

1. Read the grilling session's decisions. If `/docs/prd.md` exists and matches the session's feature, read it and every file it links. Note what is settled and what is under-specified. If the source itself has open questions, stop and say which ones block the spec; do not invent answers.

2. Explore the repo to understand the current state of the codebase, if you haven't already. Use the project's domain vocabulary throughout the spec, and respect any ADRs in the area being touched.

3. Sketch the **seams** at which the work will be tested. Existing seams are preferred over new ones. Use the highest seam possible; if new seams are needed, propose them at the highest point you can. The ideal number of seams is one. Show the user these seams and confirm they match expectations before writing.

4. Write the spec to `/docs/spec/<feature-slug>.md` using the template below. Create `docs/spec/` if missing.

<spec-template>

# Spec: <name>

Source: the settled grilling decisions, plus `/docs/prd.md` when it covers the same feature. This spec is a decision record for a fresh session: it assumes no shared context.

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
- Which modules are tested, and the seams from step 3.
- Prior art: similar existing tests to follow.

## Out of Scope

What was deliberately refused or deferred. These are usually the most useful lines on the page.

## Further Notes

Anything else a fresh session needs to pick up the work.

</spec-template>

5. Show the user the written spec and wait for confirmation. Do not start implementing until the user says the spec is right.

## It's working if

- It starts writing instead of asking a new round of questions.
- It shows the seams before writing, and proposes as few as it can.
- It uses the project's nouns, not generic boilerplate.
- The user remembers making every decision in it. Nothing was invented to fill a section.
- The out-of-scope section lists real things.
