---
name: ss-diagnose
description: Diagnosis loop for hard bugs and performance regressions — build a tight red-capable feedback loop, minimise, test ranked hypotheses, fix with a regression test, clean up. Use when the user says "diagnose", "debug this", or reports something broken, throwing, failing, or slow.
---

Find the cause of a hard bug before fixing it. The order of the phases is the discipline: no hypothesis without a feedback loop, no fix without a reproduced and minimised failure. Skip a phase only with a stated reason.

Read `GLOSSARY.md` (if it exists) and the ADRs in the area you touch, so names and seams match the project's model.

**Graph first.** If `graphify-out/graph.json` exists, use it to orient before hypothesising: `graphify query "<question>"` about the involved code paths beats reading files one by one.

## Redact

Every command, output, and captured artifact you show has its secrets replaced by `<REDACTED>`. Keep credentials in env vars the loop reads, not in what you print. From captured traffic, quote only the lines that carry the signal. Redacted output not enough to diagnose → say so and ask the user.

## Phase 1: Feedback loop

This phase is most of the work. With a tight pass/fail signal for *this* bug, bisection and hypothesis tests become mechanical; without one, reading code does not help.

Build one, trying in roughly this order:

1. A failing test at the seam that reaches the bug (unit, integration, e2e).
2. A `curl` / HTTP script against a running dev server.
3. A CLI run on a fixture input, diffed against a known-good output.
4. A headless browser script that drives the UI and asserts on DOM, console, or network.
5. A captured trace (request, payload, event log) saved to disk and replayed through the code path alone.
6. A throwaway harness: the smallest slice of the system that runs the bug path in one call.
7. A property / fuzz loop for "sometimes wrong": many random inputs, watch for the failure.
8. A bisection harness between two known states, run with `git bisect run`.
9. A differential loop: same input through old and new version (or two configs), diff the outputs.
10. Last resort, a human in the loop: a small bash script that prints each step for the user, waits with `read`, and saves what they report to a file you read back.

Then tighten it: faster (narrow scope, skip unrelated setup), sharper (assert the exact symptom, not "didn't crash"), deterministic (pin time, seed randomness, isolate filesystem and network). For a non-deterministic bug, aim for a high reproduction rate: loop the trigger, add stress, narrow timing windows.

Cannot build a loop → stop. List what you tried and ask for one of: access to an environment that reproduces it, a redacted captured artifact (HAR, log dump, recording with timestamps), or permission for temporary instrumentation. Do not hypothesise without a loop.

Done when you can name **one command**, already run at least once (show it and its redacted output), that is:

- [ ] **red-capable** — drives the real bug path and asserts the user's exact symptom;
- [ ] **deterministic** — same verdict every run, or a pinned high rate for flaky bugs;
- [ ] **fast** — seconds, not minutes;
- [ ] **agent-runnable** — runs unattended, or through the step 10 script.

Reading code to build a theory before this command exists is the failure this skill prevents.

## Phase 2: Reproduce and minimise

Run the loop and watch it go red. Confirm that the failure is the one the **user** described, not a nearby one, and capture the exact symptom (message, wrong output, timing) for later checks.

Then shrink the repro: cut inputs, callers, config, data, and steps one at a time, re-running after each cut.

Done when every remaining element is load-bearing: removing any one turns the loop green.

## Phase 3: Hypothesise

Write **3–5 ranked hypotheses** before testing any. Each states its prediction: "If <X> is the cause, then changing <Y> makes the bug disappear." A hypothesis without a prediction is discarded or sharpened.

Show the ranked list to the user: they may re-rank it or rule some out. Do not wait if they are away.

## Phase 4: Instrument

Each probe tests one prediction from Phase 3. Change one variable at a time.

- Prefer a debugger or REPL; otherwise targeted logs at the points that tell hypotheses apart. Never log everything and grep.
- Tag every debug log with one unique prefix, e.g. `[DEBUG-a4f2]`, so cleanup is one grep.
- Performance regression: measure a baseline first (timing harness, profiler, query plan), then bisect. Logs are usually the wrong tool.

## Phase 5: Fix and regression test

Write the regression test before the fix, at a **correct seam**: one where the test exercises the real bug pattern as it happens at the call site. Follow `ss-tdd` for what a good test is.

1. Turn the minimised repro into a failing test at that seam.
2. Watch it fail. If you forced the red by mutating code or a fixture, diff against a clean copy to prove the mutation landed.
3. Apply the fix at the root cause, in the shared code all callers pass through.
4. Watch the test pass.
5. Re-run the Phase 1 loop on the original, un-minimised scenario.

No correct seam → that is a finding: the design stops this bug from being locked down. Record it, and suggest `ss-improve-codebase-architecture` for the area.

## Phase 6: Clean up and hand off

- [ ] Phase 1 loop no longer reproduces the bug.
- [ ] Regression test passes, or the missing seam is recorded.
- [ ] No `[DEBUG-…]` instrumentation left (grep the prefix).
- [ ] Throwaway harnesses deleted, or moved to a clearly marked debug location.

Report the confirmed hypothesis, the fix, and the regression test. Leave the tree uncommitted and tell the user: "Run `ss-commit`; put the confirmed cause in the commit body." A fix that turned out larger than one small change goes to `ss-triage` as a request instead.
