---
name: ss-review-commit
description: Worker dispatched by ss-pm. Reviews the uncommitted work for one ticket with ss-code-review; on a clean review marks the ticket done and commits it with ss-commit. Returns Violations without committing.
disable-model-invocation: true
---

Review one ticket's uncommitted work in a fresh context, then commit it if the review passes. You did not write this code. Do not fix it.

## Arguments

- `repo=<path>` → work in this repo.
- `unit=<ticket path>` → the ticket. Required.

## Procedure

Read `~/.agents/skills/ss-code-review/SKILL.md` and `~/.agents/skills/ss-commit/SKILL.md` by path.

1. **Review.** Run `ss-code-review` on the uncommitted changes with `unit=<ticket path>`.
   - Any **Violation** → stop. Commit nothing. Report `VIOLATIONS` with each one verbatim.
2. **Mark done.** Report `CLEAN` or `HAS SUGGESTIONS` → in the ticket file set the status to `done`, record each Suggestion in its status notes, and add:
   ```
   - **Selesai:** YYYY-MM-DD · <repo>@uncommitted
   ```
   Keep any `unverified:` lines the implementer wrote.
3. **Commit.** Run `ss-commit` with `unit=<ticket path>`. Local commit only. Never push. The ticket file goes in the same commit as the code.
4. **Check.** `git status --short` must be empty. Not empty → report `HALT`.

## Report

Last line, exactly one of:

- `COMMITTED <ticket path> · <short-sha> · suggestions=<n>`
- `VIOLATIONS <ticket path>` followed by the Violations, one per line
- `HALT <ticket path> — <exact blocker or failure>`
