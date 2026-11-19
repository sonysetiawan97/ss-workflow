# Commit Format

Every commit `ss-commit` writes is a [Conventional Commits 1.0.0](https://www.conventionalcommits.org/en/v1.0.0/) message in this one shape.

## Template

```
<type>[(<scope>)][!]: <subject>

<body>

BREAKING CHANGE: <what breaks and the migration>
Refs: <ticket, request or spec path>

<attribution trailer>
```

- **type** — `feat` (new behaviour), `fix` (bug fix), `refactor` (no behaviour change), `perf`, `test`, `docs`, `style` (formatting only), `build`, `ci`, `chore`, `revert`. Pick the type the change honestly carries.
- **scope** — only when the repo already uses scopes.
- **subject** — imperative, starts lowercase, no trailing period, header ≤ 72 characters.
- **body** — one or two paragraphs: why the change exists and what it trades off. The diff already shows what. Wrap at 72.
- **BREAKING CHANGE** — what breaks and the migration; also put `!` after the type/scope.
- **Refs** — a repo path to the ticket (`docs/plan/tickets/…`), request (`docs/plan/backlog-review/…`) or spec the change serves. One path per line, one `Refs:` per path.
- **attribution trailer** — whatever the environment requires (e.g. `Co-Authored-By:`), always last.

## Rules

- Use the template exactly: header, body, footers, attribution, in that order, separated by one blank line.
- The body is always present, even for a one-line change.
- A footer with nothing behind it is left out, never filled with "None decided.". No breaking change → no `BREAKING CHANGE:` line; no ticket, request or spec → no `Refs:`.
- A commit made with `unit=<path>` always carries `Refs: <path>`.
- The repo's written rules (`CONTRIBUTING.md`, a commitlint config, …) decide the type list, scope usage and subject language. They never change the shape.

## Report

`ss-commit` ends with exactly this report:

```
<short-sha> <header>
<short-sha> <header>
Left out: <paths and why | none>
Convention mismatch: <what and where | none>
```
