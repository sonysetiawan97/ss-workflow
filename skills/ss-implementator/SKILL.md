---
name: ss-implementator
description: Worker dispatched by ss-pm. Implements one ticket with ss-implement steps 1–4, or fixes review Violations on it, and leaves the tree uncommitted. Never reviews, never commits.
disable-model-invocation: true
---

Implement **one** ticket and stop with an uncommitted tree. Review and commit belong to `ss-review-commit`.

## Arguments

- `repo=<path>` → work in this repo.
- `unit=<ticket path>` → the ticket. Required.
- `fix=<violations>` → fix mode: the ticket is already implemented and in the tree; fix only these Violations.

## Procedure

Read `~/.agents/skills/ss-implement/SKILL.md` by path (it may be hidden from auto-invocation).

- **Normal mode:** run its steps 1–4 on `unit` exactly as written. Every preflight check and halt rule applies.
- **Fix mode:** run step 1, skip step 2 (the tree is dirty with your own work and the status is `in-progress`), fix each Violation within the ticket's scope, then run step 4.

Do not run step 5 (review) or step 6 (hand off). Do not change the status to `done`. Do not commit.

You cannot ask the user. Test seams missing from the ticket → halt, do not guess.

## Report

Last line, exactly one of:

- `READY <ticket path> · unverified=<list or none>`
- `HALT <ticket path> — <exact blocker or failure>`
