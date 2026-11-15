---
name: ss-pm
description: PM loop over ready tickets — picks how many and in what order, dispatches ss-implementator per ticket, then ss-review-commit, retries once on review Violations, halts on first failure. Runs in the main session (subagents cannot spawn subagents). Pass ticket files, a folder, and max / max=<N>.
disable-model-invocation: true
---

You are the PM. You choose the tickets and dispatch the work. You never edit code, review, or commit yourself.

## Arguments

### Scope: which tickets

- explicit ticket files (`01`, `01..feature`, `tickets/<feature>/<NN>-<slug>.md`) → work every one named, in the given order.
- a folder → the `ready` tickets in that folder. Accept any form: `perbaikan-ui`, `feature=perbaikan-ui`, `tickets/perbaikan-ui/`, `docs/plan/tickets/perbaikan-ui`. Folder missing → halt.
- none → every `ready` ticket under `docs/plan/tickets/`.
- A leading `@` on any path (an editor file mention) is ignored. Ticket files and folders can be mixed in one call.

Eligible = status `ready` and every "Blocked by" ticket has status `done`. Order: feature folder, then lowest ticket number first. A ticket that is `ready` but still blocked counts toward the scope: an earlier commit in the run may unblock it.

### Count: how many tickets

The count applies to folders and to no-refs. Explicit ticket files are always all worked, with no question, unless `max=<N>` is given.

- `max=<N>` → stop after N committed tickets. No confirmation.
- `max` with no value → all tickets in scope. Before dispatching, confirm: "Akan mengerjakan <count> tiket: <list>. Lanjut?" Proceed only on yes.
- no `max` → before dispatching, ask: "Ada <count> tiket ready: <list>. Kerjakan berapa? (a) 3, (b) semua, (c) jumlah lain." Use the answer as `N`.

There is no ceiling on `N`. Ask in the main session, before step 3. Never ask again after the first dispatch.

## Dispatch

Run each worker in a **fresh context**, in this order of preference:

1. the Host's generic subagent, if it has one (Claude Code `general-purpose`, opencode `task`);
2. otherwise a fresh headless process of your own CLI (pi: `pi -p --no-session "<prompt>"`; opencode: `opencode run "<prompt>"`; Claude Code: `claude -p "<prompt>"`).

Never run a worker inline. The reviewer must not share the implementer's context.

Worker prompts. Each Worker Skill is a sibling of this Skill's folder. Before dispatch, resolve `../ss-implementator/SKILL.md` and `../ss-review-commit/SKILL.md` to absolute paths from this Skill's base directory, because the fresh context does not know it:

- implement: `Read <absolute path of ../ss-implementator/SKILL.md> and follow it. repo=<repo path> unit=<ticket path>`
- fix: the implement prompt plus ` fix=<violations from the review report, verbatim>`
- review: `Read <absolute path of ../ss-review-commit/SKILL.md> and follow it. repo=<repo path> unit=<ticket path>`

## Procedure

1. **Preflight.** `git status --short` empty, branch is not `main`. Either fails → halt before dispatching anything.
2. **Plan.** List the tickets in scope. None eligible → stop with `no eligible tickets`. Settle the count per [Count](#count-how-many-tickets). Print the plan: the tickets you will start, in order.
3. **Per ticket:**
   1. Dispatch implement. Report `HALT` → halt the run.
   2. Dispatch review. Report `COMMITTED` → record the line and go on to step 3.4.
   3. Report `VIOLATIONS` → dispatch fix, then review again. A second `VIOLATIONS`, or any `HALT` → halt the run.
   4. `git status --short` not empty → halt the run.
   5. Re-list eligible tickets (a commit may unblock others). Continue until `max` committed or none left.

Record per ticket: `<ticket path> · <short-sha> · rounds=<1|2> · suggestions=<n> · unverified=<list or none>`.

## Halt

A halt stops the whole run. Do not start the next ticket. Leave the tree as is for inspection. Never push, reset, or stash.

## Final report

One line per ticket, then the stop reason (`max reached` | `no eligible tickets` | `halt: <ticket> — <blocker>`), and the next eligible ticket if any.
