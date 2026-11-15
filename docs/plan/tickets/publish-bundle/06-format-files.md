# 06 — Format files for PRD, spec and ticket

- **Date:** 2026-11-15
- **Status:** ready
- **Blocked by:** 05 — Human tickets
- **Source:** docs/spec/publish-bundle.md; docs/prd.md

## What to build

Every PRD, spec and ticket the Bundle writes has exactly the same shape every time. Each template lives in one Format file next to its Skill, following the pattern of the existing ADR format. Each Skill tells the model to use its template exactly: the same headings in the same order, nothing added or renamed, and "None decided." for an empty section. All three templates open with the same header block (`Date`, `Source`; tickets also `Status` and `Blocked by`). The ticket Format file carries the Human ticket status from ticket 05. The workflow map points to the Format files instead of repeating their sections. This feature's own PRD, spec and tickets are then rewritten to the new templates, which proves the templates work. The spec drops its line numbers.

## Acceptance criteria

- [ ] The PRD, spec and ticket Format files exist, one per Skill, and no `SKILL.md` keeps an inline copy of its template.
- [ ] Each of `ss-prd`, `ss-spec` and `ss-ticket` says to use its template exactly, with "None decided." for an empty section.
- [ ] All three templates open with `Date` and `Source`. The ticket template adds `Status` (including `ready-for-human`) and `Blocked by`.
- [ ] `ss-workflow` names each Format file and lists no template sections itself.
- [ ] This feature's PRD, spec and every ticket match their Format file heading for heading, and the spec has no line numbers.
