---
name: ss-verify
description: Verify one ticket or request under docs/plan/tickets/ or docs/plan/backlog-review/ is genuinely done — re-check every acceptance criterion against the source of truth, re-run its tests, and confirm its claims. Read-only on code; may only update the unit's status line. Runs after ss-commit, before or instead of closing a unit.
disable-model-invocation: true
---

Verify that **one unit of work** (ticket or ready-for-agent request) is truly done. Re-run the work, not the memory: every claim in the ticket's status and notes gets re-checked against current state.

## Active skills

Load and hold **all three** for the whole session:

- **ponytail** level `full` (`/ponytail full`): check what is asked, nothing else. No re-running tests the ticket never claims, no fixing code found broken — report it.
- **caveman** (`/caveman`): terse output. Every verdict, path, and test result verbatim.
- **graphify** (`/graphify`): when `graphify-out/graph.json` exists in the target repo, orient with `graphify query` before the first file read.

## Arguments

- `<NN>` → `docs/plan/tickets/<feature-slug>/<NN>-<ticket-slug>.md`
- `<NN..feature>` → ticket `<NN>` under `docs/plan/tickets/<feature>/`
- `tickets/<feature>/<NN>-<slug>.md` → full relative path from repo root
- `review/<slug>.md` or bare slug → `docs/plan/backlog-review/<slug>.md`
- none → list every unit with status `done` (or state `done`) across `docs/plan/tickets/` **and** `docs/plan/backlog-review/`; ask which one. If exactly one, use it and say so.

The unit must carry a `Selesai:` line naming the repo and sha (or `uncommitted`). No `Selesai` line → halt: unit is not done; run `ss-implement` first.

## Steps

1. **Read the unit.** Load the ticket / request file and everything it references (agent brief, linked spec, ADRs).
   Done when every referenced file is read.

2. **Locate the code.** The `Selesai` line names `<repo>@<sha>`.
   - Repo path is relative to the framework root (e.g. `api-contract`). cd into it.
   - Check out the named state: if the repo's `dev` (or the branch the ticket names) already contains the sha → work there. Otherwise `git checkout <sha>` (detached is fine; never push, never branch from the ticket itself).
   - `Selesai: uncommitted` → verify the working tree still holds the change (`git status --short` non-empty, diff matches the ticket's scope). Missing → halt: the claimed work is not on disk.
   Done when the exact claimed state is checked out and the commit/diff exists.

3. **Re-check acceptance criteria one by one, in the repo's current state.** For each checkbox in the ticket's "Acceptance criteria" (or the request's acceptance criteria):
   - Runnable check (test command, curl, assert on a file/JSON field) → run it now and record pass/fail. Never reuse the ticket's recorded result — this is a re-run.
   - Existence check (a file, a tag, a changelog line, a `docs/` entry) → verify in the tree. `git tag` for tag claims; the changelog/spec/selisih file for text claims.
   - Multi-target claims (e.g. "green per Target: node, laravel, go") → run the check per target. Targets that cannot run here (no service in docker-compose, no binary) → record `unverified: <target>: <reason>` instead of claiming.
   - A criterion the ticket itself marks `unverified:` → re-attempt it. Still unrunnable → keep `unverified` with the same reason.
   Done when every criterion has a fresh verdict: pass, fail, or `unverified: <reason>`.

4. **Re-run the unit's tests.** The tests the ticket's own implementation added or that its "What to build" names — not the whole suite unless the ticket claims it.
   - Use the repo's documented runner (the ticket's `pnpm test` note, the repo `CLAUDE.md`, or the test command that exists in `package.json` / `Makefile`).
   - Test commands that need a live service: start it the way the repo's docs say (e.g. `pnpm env:up` / docker-compose), run the tests, stop it.
   - A test that fails now but passed in the ticket's recorded run → check for drift: does `git log <sha>..HEAD` touch the files the test covers? Report the drift, do not fix it.
   Done when every named test has a fresh pass/fail and any red has a drift explanation.

5. **Cross-check the claims.** Compare the ticket's recorded results against fresh ones:
   - Recorded "120 lulus / 5 gagal" vs. the fresh run's numbers → report the delta with the cause (fix since the ticket? environmental difference? test drift?).
   - `Selesai` sha: is it actually on the claimed branch's history? `git branch --contains <sha>`.
   - Tag claims: `info.version` in the spec equals the tag name.
   Done when every claim is confirmed, corrected, or flagged as stale.

6. **Verdict and status.** Write one line per criterion, then the unit's overall verdict:
   - **VERIFIED** — every criterion pass (or `unverified` with a valid reason), tests green or red-for-known-documented-reason, claims hold.
   - **FAILED: <list>** — any criterion fails, a test goes red without the ticket documenting the red, or a claim no longer holds. Do not fix code. Report exactly what is broken and route the user to `ss-diagnose` (broken) or a new ticket (scope that was never done).
   - **STALE** — verdicts hold but the `Selesai` sha / tag / version claims moved (later commits exist on the branch); report which.

   Then, if — and only if — the verdict is **VERIFIED**: update the unit's status line to add the verification note:
   ```
   - **Terverifikasi:** YYYY-MM-DD · <repo>@<sha> · seluruh AC <pass|unverified-sebut-siapa>
   ```
   For **FAILED** / **STALE**, change nothing on disk — the tree stays in place for the next stage.

   Report: the unit path, the verdict, per-criterion lines, any `unverified` reasons, the test delta vs. the ticket's record, and what to run next (`ss-diagnose` on FAILED, `ss-implement` on a not-done unit, or the next ready ticket on VERIFIED).

## Halt

A halt stops the work, leaving everything in place for inspection. Halts:

- no `Selesai` line on the unit (not done; `ss-implement` first);
- the claimed sha does not exist in the named repo;
- the claimed working-tree change is gone (`git status` clean and no matching commit);
- the unit contradicts the repo's sources of truth (`CLAUDE.md`, ADRs) — report the contradiction rather than choosing a side.

## Out of scope

- No code edits. Verification is read-only on the repos it checks; the only write is the `Terverifikasi:` line in the unit's own file.
- No fixing. Red stays red and gets reported. `ss-diagnose` owns the fix.
- No re-implementing. The unit's work was done by `ss-implement`; this only re-proves it.
