---
name: ss-ticket
description: Break a spec, a backlog, or the current conversation into tracer-bullet vertical-slice tickets, each declaring its blocking edges, written to <project-root>/docs/plan/tickets/ — one file per ticket. No issue tracker. For wide refactors, sequence expand–contract.
disable-model-invocation: true
---

Break a spec, plan, backlog, or the current conversation into a set of **tickets** — tracer-bullet vertical slices, each declaring the tickets that **block** it.

The issue tracker is not used. Tickets live in the repo under `docs/plan/tickets/`.

## Process

### 1. Gather context

Work from whatever is already in the conversation. If the user passes a reference — a spec path (from `ss-spec`), a backlog path, or the `docs/plan/backlog-review/` request files from `ss-triage` — read its full contents. If the reference is a `ready-for-agent` request file, its agent brief is the authoritative contract; the ticket breakdown must make it implementable.

### 2. Explore the codebase (optional)

If not already explored, explore to understand the current state of the code. Ticket titles and descriptions use the project's domain glossary vocabulary and respect ADRs in the touched area. If `graphify-out/graph.json` exists, map the relevant area with `graphify query "<question>"` first — it is cheaper and wider than a file walk.

Look for opportunities to prefactor the code to make the implementation easier: "Make the change easy, then make the easy change." Prefactoring is its own ticket, always numbered first within the chain it enables.

### 3. Draft vertical slices

Break the work into **tracer-bullet** tickets.

<vertical-slice-rules>
- Each slice cuts a narrow but COMPLETE path through every layer (schema, API, UI, tests) — vertical, not a horizontal slice of one layer.
- A completed slice is demoable or verifiable on its own.
- Each slice is sized to fit in a single fresh context window.
- Any prefactoring is done first, in its own ticket.
</vertical-slice-rules>

Give each ticket its **blocking edges** — the other tickets that must complete before it can start. A ticket with no blockers can start immediately.

**Wide refactors are the exception.** A **wide refactor** is one mechanical change (rename a column, retype a shared symbol) whose **blast radius** spans the codebase so no vertical slice can land green. Do not force it into a tracer bullet; sequence it as **expand–contract**:

1. **Expand** — add the new form beside the old; nothing breaks.
2. **Migrate** — move call sites over in batches sized by blast radius (per package, per directory); each batch is its own ticket, blocked by the expand. Batches keep CI green one by one because the old form still exists.
3. **Contract** — delete the old form in a ticket blocked by every migrate batch, once no caller remains.

When even the batches cannot stay green alone, keep the sequence but have them share an integration branch that all block a final **integrate-and-verify** ticket; green is promised only there.

### 4. Quiz the user

Present the proposed breakdown as a numbered list. For each ticket:

- **Title**: short descriptive name.
- **Blocked by**: which other tickets gate it (numbers/titles).
- **What it delivers**: the end-to-end behaviour this ticket makes work, from the user's perspective.

Ask:
- Does the granularity feel right? (too coarse / too fine)
- Are the blocking edges correct — does each ticket only depend on tickets that genuinely gate it?
- Should any tickets be merged or split further?
- Which tickets does a person work instead of an agent? Those become **Human tickets**.

Iterate until the user approves.

### 5. Write the tickets

- Root = `git rev-parse --show-toplevel`.
- Path: `<root>/docs/plan/tickets/<feature-slug>/<NN>-<ticket-slug>.md`, numbered from `01` in dependency order (blockers first). Create the directory if missing.
  - `<feature-slug>`: same slug as the spec file `docs/spec/<feature-slug>.md` when one exists; otherwise a kebab-case feature name.
  - `<NN>`: two digits, zero-padded.
- Language: the project's document language for the body text; headings stay as in the template; keep identifiers as-is.
- Use the template in [TICKET-FORMAT.md](./TICKET-FORMAT.md) exactly: the same headings in the same order, nothing added or renamed, and "None decided." for an empty section. One ticket per file, never a combined file.
- Set each ticket's status per the Status section of [TICKET-FORMAT.md](./TICKET-FORMAT.md).
- Do NOT close or modify any spec or backlog file.

## It's working if

- The user says "make tickets from this spec" and gets a numbered list to quiz, not a wall of text.
- Every ticket is one fresh context window of work with a demoable end state.
- Wide refactors are expanded–migrated–contracted, never forced into one tracer bullet.
- Nothing outside `docs/plan/tickets/` is modified.
