---
name: ss-commit
description: Commit the working tree locally as Conventional Commits 1.0.0 — repo convention first, one logical change per commit, staged by name. Never pushes. Closing stage of ss-workflow, run after ss-implement or ss-diagnose.
disable-model-invocation: true
---

Turn the working tree into one or more **atomic** commits: each commit is one logical change that builds and passes on its own. Local only — pushing, rebasing, and merging belong to the user or to a shipping skill.

## Arguments

- none → derive the message from the diff.
- `"<type>: <subject>"` → use this header (e.g. a ticket's subject line); still check it against the convention.
- `unit=<path>` → a ticket (`docs/plan/tickets/…`) or request file (`docs/plan/backlog-review/…`): take the subject from its title, the body's *why* from its "What to build" / agent-brief summary, and add `Refs: <path>`.

## Steps

1. **Read the change.** `git status --short`, `git diff`, `git diff --cached`, untracked files. Nothing to commit → say so and stop.
   Done when every changed path is known.

2. **Find the convention.** The shape is always [COMMIT-FORMAT.md](./COMMIT-FORMAT.md). The type list, scope usage and subject language come from, in order, the first that exists:
   1. written rules — `CONTRIBUTING.md`, `docs/governance/change-process.md`, the repo's `CLAUDE.md`, a commitlint config;
   2. the pattern in `git log -20 --format='%s'` — scope or no scope, subject language, casing;
   3. the defaults in [COMMIT-FORMAT.md](./COMMIT-FORMAT.md).
   A requested type missing from the repo's allowed list (e.g. `ci` where only `feat|fix|test|docs|chore|refactor` are listed) → keep it, and flag the mismatch in the report.
   Done when type list, scope usage, and subject language are decided.

3. **Group.** Split the change into logical units. One unit per commit; a unit whose subject needs "and" is two. Mixed hunks inside one file that belong to different units → ask rather than guess.
   Done when every changed path is assigned to exactly one commit.

4. **Stage by name.** `git add <path>…` per unit — never `git add -A` or `git add .`. Leave out secrets (`.env*` except examples, keys, credentials, tokens), build output, and editor/OS junk (`.DS_Store`); report anything left out.
   Done when `git diff --cached --stat` shows exactly the unit.

5. **Commit.** Write the message using the template in [COMMIT-FORMAT.md](./COMMIT-FORMAT.md) exactly and commit with a heredoc. Pre-commit hook fails → fix the cause, restage, and make a **new** commit attempt; never `--no-verify`, never `--amend` unless the user asked.
   Done when `git log -1` shows the commit and the unit's paths are gone from `git status`.

6. **Report** in the Report shape of [COMMIT-FORMAT.md](./COMMIT-FORMAT.md#report): one line per commit, then `Left out:` and `Convention mismatch:`.
