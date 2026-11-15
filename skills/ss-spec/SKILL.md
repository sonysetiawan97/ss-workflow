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

4. Write the spec to `/docs/spec/<feature-slug>.md` using the template in [SPEC-FORMAT.md](./SPEC-FORMAT.md) exactly: the same headings in the same order, nothing added or renamed, and "None decided." for an empty section. Create `docs/spec/` if missing.

5. Show the user the written spec and wait for confirmation. Do not start implementing until the user says the spec is right.

## It's working if

- It starts writing instead of asking a new round of questions.
- It shows the seams before writing, and proposes as few as it can.
- It uses the project's nouns, not generic boilerplate.
- The user remembers making every decision in it. Nothing was invented to fill a section.
- The out-of-scope section lists real things.
