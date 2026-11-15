# 05 — Human tickets

- **Date:** 2026-11-15
- **Status:** done · unverified: AC5 scratch-repo `/ss-pm` run (needs a main session that can dispatch Workers and commit; not runnable from a Worker). Rule text checked by reading: `ss-pm` eligibility excludes `ready-for-human`, report adds the Human ticket line
- **Review suggestion:** [clean:duplication] skills/ss-pm/SKILL.md:13 — "explicit ticket files → work every one named" reads against line 18 (Human ticket never dispatched, even when named explicitly); point the explicit-files line at the exception.
- **Selesai:** 2026-11-15 · /Volumes/workspace/ss/skills/ss-workflow@uncommitted
- **Blocked by:** 01 — Import the Bundle with Published names; 03 — Public-clean text
- **Source:** docs/spec/publish-bundle.md; docs/prd.md

## What to build

A person can own a ticket inside the same breakdown as the agent-worked tickets. During its quiz step, `ss-ticket` asks which tickets a person works and writes those as Human tickets (`Status: ready-for-human`). `ss-pm` never dispatches a Human ticket. When a run ends, its report names every Human ticket whose blockers are all `done`, so the person knows it is their turn. Workability is worked out from the blockers and never written as a status change. The person sets `done` by hand. The workflow map describes Human tickets in its ticket and PM stages.

## Acceptance criteria

- [ ] `ss-ticket`'s status list includes `ready-for-human`, and its quiz step asks which tickets a person works.
- [ ] `ss-pm` selects only `ready` tickets whose blockers are `done`, and never dispatches a `ready-for-human` ticket.
- [ ] `ss-pm`'s run report adds `<NN> — <title>: ready-for-human, all blockers done` for each such Human ticket.
- [ ] `ss-workflow` describes Human tickets in its `ss-ticket` and `ss-pm` stages.
- [ ] In a scratch repo with one `ready` ticket and one Human ticket whose blockers are `done`, `/ss-pm` works only the `ready` ticket, and its report names the Human ticket as workable.
