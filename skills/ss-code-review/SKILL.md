---
name: ss-code-review
description: Review a diff for design quality — Clean Code (Robert C. Martin) and strategic DDD — against the project's own sources of truth. Read-only; reports Violations and Suggestions. Use when the user says "review the code", "review this diff", or as the closing step of ss-implement.
---

Review **design**, not compliance. Compliance (lint, API contract, migrations, docs, privilege catalog) belongs to the repo's own tooling and governance reviewer (e.g. `sagara-review`, `sagara-code-reviewer`); skip anything they or a linter already enforce. This review asks: is the code honest, small, and shaped like the domain?

Read-only: write the report, change nothing.

## Arguments

- none → uncommitted changes (`git diff HEAD` plus untracked files) if the tree is dirty; otherwise the branch against its Git Flow base (`dev` for work branches, `main` for `hotfix/*`), three-dot diff.
- `<ref>` → `git diff <ref>...HEAD`.
- `unit=<path>` → the unit under review (a ticket from `ss-implement` or an agent-brief request file from `ss-triage`), read for **intent** (what the change is for). Whether the code matches the unit is the executor's check, not this review's.

## Fresh eyes

If the code under review was written in this conversation, dispatch a `general-purpose` subagent to run steps 1–4 with this file's path and the arguments; relay its report. The reviewer must not be the author.

## Steps

1. **Collect.** Get the diff and the list of changed files. Bad ref or empty diff → stop and say so.
   Done when every changed file is listed.

2. **Load the sources of truth.** Read the repo's and the parent project's `CLAUDE.md`, governance docs (`docs/governance/`), and — when the project has them — the glossary (`GLOSSARY.md` or the project's canonical terms doc), the decision log (`docs/adr/`), and the domain-boundary rules. These decide what counts as a Violation and override every heuristic below.
   Done when you can name the project's bounded contexts, its glossary terms touched by the diff, and any rule in the docs that endorses something a heuristic would flag.

3. **Apply both lenses.** For each changed file, read the surrounding code (the whole function, the module's siblings), not just the hunk, and check every item in [Clean Code](#clean-code) and [Strategic DDD](#strategic-ddd). If `graphify-out/graph.json` exists, first ask the graph (`graphify query "<question>"`, `graphify path "A" "B"`) which neighbours matter, then read exactly those.
   Done when every changed file has findings or is declared clean, and every finding is verified in the code — doubt left after rereading goes in the finding.

4. **Report** in the [format](#report), in the project's document language.

## Severity

- **Violation** — breaks a rule written in the project's sources (`CLAUDE.md`, decision log, governance, glossary). Cite the source. Blocks committing the work.
- **Suggestion** — a heuristic from the lenses below, with the principle named. Never escalated to Violation, however strongly felt.

A documented repo rule that endorses what a heuristic would flag suppresses the finding. The framework's idiom wins over textbook patterns: never ask for repositories, entity classes, or value-object types the framework does not use.

## Clean Code

Robert C. Martin, *Clean Code*. Each item: the **smell** → the direction of the fix.

- **Names** — a name that hides intent, needs a comment to explain, or uses a technical word where the domain has one. → rename; no honest name means murky design.
- **Function size and focus** — a function doing more than one thing, or mixing levels of abstraction (business rule next to byte fiddling). → extract until each function does one thing at one level.
- **Arguments** — more than three parameters, boolean flag parameters, output parameters. → introduce a parameter object; split the flagged function in two.
- **Hidden side effects** — a function whose name promises a query but also writes, or mutates its input. → command-query separation: separate the write, or rename to admit it.
- **Error handling** — swallowed errors, error codes returned where the stack throws, `null` returned where a caller must remember to check. → throw (or the repo's error idiom), handle at the boundary.
- **Duplication** — the same knowledge (a rule, a magic value, a shape) written in two places in or around the diff. → one source; call it from both.
- **Comments** — a comment restating the code, or apologising for it. → make the code say it. (The repo's comment policy overrides.)
- **Boundaries** — a third-party API used raw across many call sites. → wrap it once where the system meets it.
- **Single responsibility** — a module edited for several unrelated reasons in one change. → split along the reasons.
- **Speculative generality** — hooks, options, or layers the change does not need. → delete; inline until a real need appears.
- **Tests** — a test asserting more than one concept, depending on order or shared state, or named after code instead of behaviour. → F.I.R.S.T.: one concept per test, behaviour-named.

## Strategic DDD

Eric Evans, *Domain-Driven Design* — the strategic half. Each item: the **leak** → the direction of the fix.

- **Bounded context** — code in one context reading another context's tables, importing its internals, or sharing its models; anything crossing a boundary other than the project's declared contracts. → go through the contract (API, versioned event, shared contracts package).
- **Ubiquitous language** — identifiers, table names, or messages that drift from the glossary, or two names for one concept. → use the glossary term; propose a glossary entry when the concept is new.
- **Ownership** — a row, entity, or field written by two owners. → one writer; the other side records its own fact and references it.
- **Invariants** — a domain rule enforced in a handler, controller, or UI instead of in one place in the domain layer, or enforced in several places. → move it to the single place every write path passes through.
- **Consistency boundary** — one transaction spanning what should be independent aggregates, or one aggregate's invariant split across transactions. → align the transaction with the invariant it protects.
- **Context mapping** — external payloads used as internal models deep inside the code. → translate at the edge (anti-corruption layer); keep the external shape out of the domain.
- **Domain events** — events named as commands or CRUD (`updateVisit`), unversioned, or missing the fact they report. → past-tense facts (`VisitCheckedOut`), versioned, carrying what consumers need.

## Report

```
## ss-code-review: <scope>
Result: CLEAN | HAS SUGGESTIONS | HAS VIOLATIONS

### Violations (n)
1. [ddd:bounded-context] src/visits/sync.js:42 — reads `invitation` table directly
   Source: CLAUDE.md C2 (no domain boundary reads its neighbour's database)
   Direction: consume the invitation snapshot through the sync contract

### Suggestions (n)
1. [clean:arguments] lib/audit.js:18 — `recordAudit(session, ctx, entry, isOverride)` boolean flag
   Direction: separate `recordOverride()`

### Files checked
- lib/audit.js: 1 suggestion
- schemas/AuditLog.js: clean
```

Every finding carries a category (`clean:<item>` or `ddd:<item>`), `file:line`, what is wrong, the source (Violation only), and the direction of the fix.
