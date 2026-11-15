# Ticket Format

Tickets live at `docs/plan/tickets/<feature-slug>/<NN>-<ticket-slug>.md`, one ticket per file.

## Template

```md
# <NN> — <Ticket title>

- **Date:** YYYY-MM-DD
- **Source:** <spec / backlog path, if any>
- **Status:** ready
- **Blocked by:** <NN — title; NN — title>, or "None — can start immediately"

## What to build

The end-to-end behaviour this ticket makes work, from the user's perspective — not a layer-by-layer implementation list.

## Acceptance criteria

- [ ] Criterion 1
- [ ] Criterion 2
```

## Status

One of `ready · ready-for-human · in-progress · done · blocked — <reason>`.

- `ready`: an agent works it.
- `ready-for-human`: a **Human ticket**. A person works it and sets `done` by hand; `ss-pm` never dispatches it.
- Notes on the outcome (`unverified: …`, review suggestions) follow the status on the same line, after ` · `.

A ticket is workable when its status is `ready` or `ready-for-human` and every blocker is `done`. This is worked out, never written: no status changes when a ticket unblocks.

## Rules

- Use the template exactly: the same headings in the same order, nothing added or renamed.
- A section with nothing decided holds only "None decided."
- The only lines added later go under the header block: `Done:` (written by `ss-implement` or `ss-review-commit`) and `Verified:` (written by `ss-verify`).
- No specific file paths or code snippets — they go stale fast. Exception: a prototype snippet that encodes a decision more precisely than prose (state machine, reducer, schema, type shape), noted as coming from a prototype.
