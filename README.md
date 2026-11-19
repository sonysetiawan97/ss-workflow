```
npx skills add sonysetiawan97/ss-workflow
npx skills add sonysetiawan97/ss-workflow -g
```

```
ss-grilling  →  ss-prd  →  ss-spec  →  ss-ticket  →  ss-implement  →  ss-commit
   (per feature)      (per PRD)     (per spec)      (per spec)      (per ticket)        (per unit)
```

- `ss-code-review`: review a diff for Clean Code and strategic DDD; reports Violations and Suggestions.
- `ss-codebase-design`: shared vocabulary for designing deep modules and placing seams.
- `ss-commit`: commit the working tree locally as Conventional Commits; never pushes.
- `ss-diagnose`: diagnosis loop for hard bugs and performance regressions.
- `ss-domain-modeling`: keep `GLOSSARY.md` and ADRs current.
- `ss-grilling`: interview that sharpens an idea into shared understanding.
- `ss-implement`: implement one ticket test-first and verify its acceptance criteria.
- `ss-implementator`: worker that implements one ticket for `ss-pm`.
- `ss-improve-codebase-architecture`: scan for deepening opportunities and grill the one you pick.
- `ss-pm`: implement, review and commit many ready tickets in one run.
- `ss-prd`: create or update `docs/prd.md` and its companion docs (schemas, API list, page routes) from settled decisions.
- `ss-review-commit`: worker that reviews one ticket for `ss-pm` and commits it when clean.
- `ss-spec`: turn settled decisions into `docs/spec/<feature-slug>.md`.
- `ss-tdd`: test-driven development, red-green-refactor.
- `ss-ticket`: break a spec into vertical-slice tickets under `docs/plan/tickets/`.
- `ss-triage`: triage backlog requests into agent-ready briefs.
- `ss-verify`: prove a ticket marked done is genuinely done.
- `ss-workflow`: map of which skill runs when and how they hand off.

MIT License. See [LICENSE](LICENSE).
