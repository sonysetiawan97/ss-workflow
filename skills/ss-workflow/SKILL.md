---
name: ss-workflow
description: "Static map of the ss engineering workflow: which skill runs when, in what order, and how they hand off. Consult when the user asks about the process or which skill to use, or starts a feature with no plan yet."
---

# SS Workflow

A static flow document. It does not run anything — it tells you which skill to invoke at each stage, and what that stage leaves behind so the next one can pick up.

## Precondition

None. No issue tracker and no per-repo setup: every ss skill reads and writes fixed paths in the repo (`GLOSSARY.md`, `docs/adr/`, `docs/prd.md`, `docs/spec/`, `docs/plan/`).

## The main flow: idea → ship

The route most work travels. Each stage's output is the next stage's input.

```
ss-grilling  →  ss-prd  →  ss-spec  →  ss-ticket  →  ss-implement  →  ss-commit
   (per feature)      (per PRD)     (per spec)      (per spec)      (per ticket)        (per unit)
```

| # | Skill | When | Leaves behind |
|---|---|---|---|
| 1 | `ss-grilling` | idea not yet settled | shared understanding, `GLOSSARY.md`, `docs/adr/` |
| 2 | `ss-prd` | decisions settled | `docs/prd.md` + changelog line |
| 3 | `ss-spec` | ready to write seams and implementation decisions | `docs/spec/<feature-slug>.md` |
| 4 | `ss-ticket` | spec too big for one session | `docs/plan/tickets/<feature-slug>/NN-*.md`, status `ready` |
| 5 | `ss-implement` | a `ready` ticket or `ready-for-agent` request | uncommitted tree, unit `done`, review report |
| 6 | `ss-commit` | after `ss-implement` or `ss-diagnose` | atomic Conventional Commits, not pushed |
| 5–6 | `ss-pm` | several tickets `ready`; run 5 and 6 for each without invoking them by hand | one local commit per ticket, run report |

`ss-ticket`, `ss-implement`, `ss-commit`, `ss-pm`, `ss-implementator`, `ss-review-commit`, and `ss-triage` set `disable-model-invocation: true`: the user invokes them by name (`/ss-commit`, …); the model never starts them on its own.

### 1. `ss-grilling`

**When:** you have an idea and it's not yet settled — no spec, no tickets, just a question and a hunch.

**What it does:** relentless interview, one frontier round at a time, recommended answer per question. As decisions settle, it writes to `GLOSSARY.md` and (sparingly) `docs/adr/` — that's the paper trail; it is not optional.

**Leaves behind:** shared understanding + `GLOSSARY.md` entries + ADRs where warranted.

**Done when:** the user confirms "that's the shared understanding," and nothing in the design tree is left silently assumed.

### 2. `ss-prd`

**When:** the grilling session just settled the decisions and you need a durable, dated record of them in the repo.

**What it does:** synthesizes the grilling session's settled decisions into `docs/prd.md`, using the template in `ss-prd/PRD-FORMAT.md` exactly, and appends one line to `## Changelog` naming what changed and why. Updates in place when the PRD already covers the same feature.

**Leaves behind:** `docs/prd.md` (current, with changelog history).

**Done when:** the user confirms the PRD matches the shared understanding; the changelog line is written.

### 3. `ss-spec`

**When:** you're ready to write the test-seams and implementation decisions down — the grilling context is settled, and optionally the PRD covers the same feature.

**What it does:** reads the grilling session's settled decisions as the primary source; if `docs/prd.md` covers the **same feature context**, it supplements with user stories, requirements, and out-of-scope lines. If the PRD is for a different feature, it is ignored for this spec — the spec stands on the session alone, and `ss-prd` should be run first to bring it in sync. Writes `docs/spec/<feature-slug>.md`, using the template in `ss-spec/SPEC-FORMAT.md` exactly.

**Leaves behind:** `docs/spec/<feature-slug>.md`; the slug names the ticket folder in the next stage.

**Done when:** the user confirms the spec is right and the seams match expectations.

### 4. `ss-ticket`

**When:** the spec is written and the work is too big for one context window, or you want tracer-bullet slices with declared blocking edges.

**What it does:** breaks the spec into vertical-slice tickets under `docs/plan/tickets/<feature-slug>/`, numbered in dependency order, each using the template in `ss-ticket/TICKET-FORMAT.md` exactly. Wide refactors are sequenced expand → migrate → contract, never forced into one slice.

**Leaves behind:** one file per ticket, each with a `Status: ready` line and a "Blocked by" list. In the quiz step the user names the tickets a person works. Those are **Human tickets**, written with `Status: ready-for-human`. A Human ticket is workable once all its blockers are `done`; nothing rewrites its status. The person sets `done` by hand.

**Done when:** the user approves the breakdown; every ticket is one fresh context window of work with a demoable end state.

### 5. `ss-implement`

**When:** a ticket is `ready` (or a `ready-for-agent` request file is waiting under `docs/plan/backlog-review/`).

**What it does:** takes one unit through, in order:

1. preflight — blockers `done`, tree clean, right branch;
2. sets the unit `in-progress`;
3. drives `ss-tdd` at the unit's pre-agreed seams (red → green → refactor, one slice at a time), with typechecking and the test suite;
4. verifies every acceptance criterion;
5. dispatches a fresh subagent to run `ss-code-review` over the uncommitted diff;
6. sets the unit `done`.

**Leaves behind:** a clean, uncommitted working tree; the ticket marked `done`; a review report (CLEAN / HAS SUGGESTIONS).

**Done when:** every acceptance criterion is satisfied by a passing test or verified manual check, and the review is clean.

### 6. `ss-commit`

**When:** `ss-implement` or `ss-diagnose` just finished and the tree is uncommitted.

**What it does:** turns the tree into atomic Conventional Commits on the current branch, staged by name. Never pushes.

**Done when:** the commit lands; the next ready ticket can be picked up.

### Autopilot: `ss-pm` (steps 5–6 for many tickets)

**When:** several tickets are `ready` and you want them implemented and committed without invoking steps 5 and 6 by hand each time.

**What it does:** runs in the main session, because subagents cannot spawn subagents. It picks the eligible tickets: status `ready`, blockers `done`. With `max=<N>` it works N tickets. With a bare `max` it confirms the count first. With no `max` it asks: 3, all, or another number. Explicit ticket files are always all worked. For each ticket it dispatches two workers in fresh contexts:

1. `ss-implementator` runs steps 1–4 of `ss-implement` and leaves the tree uncommitted.
2. `ss-review-commit` runs `ss-code-review`. If the review is clean, it sets the unit `done` and runs `ss-commit`.

It never dispatches a Human ticket (`ready-for-human`). A Violation goes back to the implementator once, with `fix=`. The PM halts the whole run on the first failure. The workers cannot ask the user, so a ticket that is missing its seams halts the run instead of guessing. On a runtime without subagents (pi), each worker runs as a fresh headless process (`pi -p --no-session`).

**Leaves behind:** one local commit per ticket, never pushed. The run ends with a report of one line per ticket and the stop reason, plus `<NN> — <title>: ready-for-human, all blockers done` for each Human ticket whose blockers are all `done`.

**Done when:** `max` is reached, no eligible ticket remains, or a halt is reported with its exact blocker.

## On-ramps

A starting situation that merges onto the main flow, not a step in it.

- **Backlog requests piling up** → `ss-triage`. Moves requests through the five-state triage machine (`needs-triage` → `needs-info` / `ready-for-agent` / `ready-for-human` / `wontfix`), verifies claims, grills if needed, writes agent briefs to `docs/plan/backlog-review/`. A `ready-for-agent` brief is `ss-implement`'s alternate input — same stage, different source; `ss-implement` then moves it `in-progress` → `done`. Don't triage your own tickets from `ss-ticket`; they're already agent-ready.

- **Something's broken and hard to pin down** → `ss-diagnose`. Builds a tight red-capable feedback loop, minimises the repro, tests ranked hypotheses, fixes with a regression test at a correct seam, then hands off to `ss-commit`. A fix that grows past one small change becomes a request for `ss-triage` instead.

- **A unit says `done` but you want proof** → `ss-verify`. Re-runs every acceptance criterion against the claimed repo state (checkout the `Done` sha, re-run the unit's tests, re-check tags/changelogs/claims), returns VERIFIED / FAILED / STALE, and only on VERIFIED stamps a `Verified:` line on the unit. Read-only on code; never fixes — FAILED routes to `ss-diagnose`.

## Codebase health

Not feature work.

- **`ss-improve-codebase-architecture`** — run when you have spare time and the codebase is resisting you. Scans hot spots, produces an HTML report of deepening candidates, grills the chosen one. Picking a candidate **generates an idea** for the main flow: go run `ss-grilling` on it, then `ss-prd` → `ss-spec` → `ss-ticket`.

## Vocabulary layer (consulted by the skills above, not a stage)

- **`ss-domain-modeling`** — the active discipline of keeping `GLOSSARY.md` and ADRs current. Driven by `ss-grilling` and `ss-triage`; reach for it directly when a term is fuzzy or a hard-to-reverse decision just got made.
- **`ss-codebase-design`** — the deep-module vocabulary (module, interface, depth, seam, adapter, leverage, locality). `ss-tdd` and `ss-improve-codebase-architecture` both speak it; consult it when the question is *where* the seam goes or *how deep* the interface should be.
- **`ss-tdd`** — the rules of the red → green → refactor loop: tests only at pre-agreed seams, vertical slices, no tautological or implementation-coupled tests. Driven by `ss-implement` and `ss-diagnose`.
- **`ss-code-review`** — the closing lens: Clean Code (Robert C. Martin) + strategic DDD, read-only, reports Violations / Suggestions. Runs at the end of `ss-implement`; reachable any time you want a design-quality read on a diff.

## Context hygiene

Keep steps 1–4 in **one unbroken context window** so the grilling, PRD, spec, and tickets all build on the same thinking. `ss-implement` then starts fresh per ticket, working from the ticket file alone. `ss-pm` enforces this: every worker it dispatches gets a fresh context, and the reviewer never shares the implementer's context. If a session nears the token limit before `ss-ticket`, compact at the nearest phase boundary — don't push on degraded.

## Navigation: graphify

Every stage that explores the codebase (grilling fact lookups, triage exploration, ticketing, TDD, diagnose, code review, architecture scan) checks for `graphify-out/graph.json` first. When it exists, structural questions — what calls what, where a symbol lives, how two areas relate — are answered with `graphify query "<question>"` (or `graphify path "A" "B"`) before falling back to file reads, grep, or a sub-agent walk. No graph → the fallback is unchanged.
