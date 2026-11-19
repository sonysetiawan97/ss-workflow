---
name: ss-prd
description: Create or update docs/prd.md and its companion docs (schemas, API list, page routes) from the current conversation context. Use when the user says "write the PRD", "update the PRD", or after a ss-grilling session has settled decisions. New feature context updates the existing PRD and appends a changelog entry.
---

Create or update `/docs/prd.md` and its companion docs from the current conversation context. No interview: synthesize what has already been discussed (typically a finished `ss-grilling` session). The PRD records settled decisions in the project's own vocabulary. If the PRD states something nobody decided, that is a defect — flag it to the user instead of filling it in yourself.

## Process

1. **Determine the feature.** The feature slug is the topic of the conversation (or one the user names). Check `docs/prd.md` for an existing PRD. If the conversation is about a **different feature** than the one in `docs/prd.md`, ask: replace the PRD, or abort? One PRD per repo at a time; multi-feature work gets its own PRD file under `docs/` only when the user explicitly asks for it.

2. **Determine the companions.** The repo's kind decides which companion docs the PRD carries. Read the environment (`package.json` dependencies, a `migrations/` or `schemas/` folder, a router or `pages/` / `app/` folder) and pick:

   | Repo kind | Companions |
   |---|---|
   | Backend (API, database) | `docs/schemas.md`, `docs/api-list.md` |
   | Frontend (pages, routes) | `docs/page-routes.md` |
   | Full-stack | all three |
   | Neither (docs, skills, CLI without routes or tables) | none |

   Done when every companion is either chosen or ruled out with a reason from the environment. Unclear kind → ask the user.

3. **Gather decisions from context.** The settled decisions live in:
   - `GLOSSARY.md` (term vocabulary) and `docs/adr/` (recorded trade-offs) — written during the grilling session
   - The current conversation itself
   - The existing PRD and its companions, when updating
   Read all of them. Do **not** re-interview. If a section has no settled decision behind it, write "None decided." — do not invent.

4. **Write or update `/docs/prd.md`.** Use the template in [PRD-FORMAT.md](./PRD-FORMAT.md) exactly: the same headings in the same order, nothing added or renamed, and "None decided." for an empty section. When the PRD already exists, update the sections in place; don't rewrite unchanged sections. Every settled decision gets a home in exactly one place, the PRD or one companion; don't restate the same decision in two places.

5. **Write or update each companion.** Use its template exactly: [SCHEMAS-FORMAT.md](./SCHEMAS-FORMAT.md), [API-LIST-FORMAT.md](./API-LIST-FORMAT.md), [PAGE-ROUTES-FORMAT.md](./PAGE-ROUTES-FORMAT.md). The PRD holds the *why* and the rule; the companion holds the *shape*. Companions carry no changelog of their own: their changes go in the PRD's changelog line. Done when every table, endpoint or route a requirement mentions has its companion entry, and every companion entry names the requirement it serves.

6. **Changelog discipline.** Every PRD or companion write appends one line to `## Changelog` at the top of that section: date, what changed, why. A new feature context layered on top of an existing PRD (e.g. grilling for feature B while the PRD covers feature A) updates the relevant sections AND appends a changelog line naming the new context.

7. **Show the user** the written PRD, each companion written, and the changelog line. Do not start implementing until the user confirms they match the shared understanding.

## It's working if

- It writes instead of asking new questions.
- It uses the project's nouns, not generic boilerplate.
- The user remembers making every decision in it — nothing was invented to fill a section or a companion row.
- Each decision lives in one place: the PRD links the companion instead of repeating its columns, endpoints or routes.
- The changelog line tells a fresh session exactly what changed and why, without re-reading the conversation.
