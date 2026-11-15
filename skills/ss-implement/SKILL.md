---
name: ss-implement
description: Implement one ticket from docs/plan/tickets/ or one ready-for-agent request from docs/plan/backlog-review/ — tdd at pre-agreed seams, verify against acceptance criteria, then hand off to ss-code-review and ss-commit. Leaves a clean, uncommitted tree for review.
disable-model-invocation: true
---

Implement the work described by **one ticket** or **one ready-for-agent request**. The ticket / agent brief is the contract — its acceptance criteria are the gate.

## Active skills

Load and hold **all three** for the whole session:

- **ponytail** level `full` (`/ponytail full`): smallest change that completes the task. Skip features nobody asked for, no unrequested abstractions, shortest working diff wins.
- **caveman** (`/caveman`): terse output. Answer first, kill ceremony, every technical fact verbatim.
- **graphify** (`/graphify`): when `graphify-out/graph.json` exists, orient with `graphify query` before the first file read.

These apply at every step: implementation, verification, review dispatch, and handoff reporting.

## Arguments

- `<NN>` → `docs/plan/tickets/<feature-slug>/<NN>-<ticket-slug>.md`
- `<NN..feature>` → ticket `<NN>` under `docs/plan/tickets/<feature>/`
- `tickets/<feature>/<NN>-<slug>.md` → the full relative path from repo root
- `review/<slug>.md` or a bare slug → `docs/plan/backlog-review/<slug>.md` (a `ready-for-agent` request file from `ss-triage`)
- none → list every `ready` ticket across all features under `docs/plan/tickets/` **and** every `ready-for-agent` request under `docs/plan/backlog-review/`; ask which one. If exactly one item is available, use it and say so.

If the reference is a `ready-for-agent` request file, its **agent brief** is the authoritative spec: read it in full before starting. For ticket references, load the ticket file and its "Blocked by" tickets.

## Steps

1. **Read the unit of work.** Load the ticket / request file and everything it references (agent brief, linked spec).
   Done when every referenced file is read.

2. **Preflight.**
   - Every "Blocked by" item has status `done` (for tickets), unless the user has explicitly waived it. A not-done blocker → halt and report which one.
   - Working tree is clean (`git status --short` empty). Not clean → halt.
   - The repo is on `dev` or a feature branch under `dev` (the ticket's named branch if it names one; never `main`). On `main` → halt: check out `dev` (or the ticket's branch) first.
   - For a `ready-for-agent` request: the agent brief is present and complete (current behavior, desired behavior, key interfaces, acceptance criteria, out of scope). If any section is missing or the request still needs info, halt and route it back to `ss-triage`.
   Done when all checks pass. Only then set the unit's status to `in-progress` (for a request file, its state line).

3. **Implement with tdd at the unit's test seams.**
   - If `graphify-out/graph.json` exists, orient the area the ticket touches with `graphify query "<question>"` before the first file read.
   - Follow the `ss-tdd` skill: write the failing test first, then only enough code to pass it; one slice at a time. Confirm the seams with the user before writing the first test when they are not already named in the ticket / brief.
   - Refactor only after green, within the unit's scope, re-running the tests after each step.
   - Run typechecking regularly, single test files regularly, and the full test suite once at the end.
   Done when every acceptance criterion in the ticket / brief is satisfied by a passing test or a verified manual check.

4. **Verify.** Run every acceptance-criterion check in the ticket or agent brief, exactly as written.
   - Every check is **green** before moving on.
   - Red → fix within the unit's scope and re-run. Still red after an honest attempt, or green only by widening scope → halt.
   - A check that cannot be run here (missing service, manual device check) → record it as `unverified: <check>` in the unit's status line rather than claiming it.

5. **Review.** Resolve the sibling path `../ss-code-review/SKILL.md` to an absolute path from this Skill's base directory, then dispatch a `general-purpose` subagent: "Read `<absolute path>` and follow it on the uncommitted changes in `<repo>`, `unit=<path>`. Return the report." You wrote the code, so the review runs in that fresh context, never inline.
   - **Violation** → fix within the unit's scope, re-run step 4, review again. Still present after the second review, or fixable only by going outside the unit → halt.
   - **Suggestion** → record each in the unit's status notes. Apply none. Scope stays the unit's.
   Done when the report is `CLEAN` or `HAS SUGGESTIONS`.

6. **Hand off.** Set the ticket's status to `done`, or the request file's state line to `done`, and add a completion note:
   ```
   - **Done:** YYYY-MM-DD · <repo>@<working-tree-sha or "uncommitted">
   ```
   Report: the unit's path, its status, any `unverified:` checks, and the Suggestion count.
   Tell the user: "Run `ss-commit` to commit, then `ss-implement` on the next ready item to continue."

   The tree is **uncommitted** at handoff. `ss-commit` owns the commit.

## Halt

A halt stops the work, leaving the tree in place for inspection. Report what was done, where it stopped, and the exact blocker or failure. Halts:

- a blocked-by item is not done;
- the working tree was not clean at preflight;
- the current branch is `main` (implementation must run on `dev` or a feature branch under `dev`);
- a `ready-for-agent` request is missing or incomplete agent-brief sections;
- verification still red after an honest attempt, or green only by widening scope;
- a Violation that survives two review rounds;
- the unit's scope contradicts the project's sources of truth (`CLAUDE.md`, decision log) — report the contradiction rather than choosing a side.
