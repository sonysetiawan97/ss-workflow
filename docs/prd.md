# PRD: Publish the ss workflow Bundle

- **Date:** 2026-11-15
- **Source:** the grilling session of 2026-11-15, `GLOSSARY.md`, `docs/adr/0001-workers-without-agent-files.md`

## Problem

The ss workflow skills exist only on one machine, as local `ss-*-001` folders under `~/.agents/skills/`. They are not in git, cannot be installed online, and carry local-only details: a Dev suffix, hardcoded `~/.agents/skills/` paths, Agent files under `~/.claude/agents/`, office (Sagara) skill names, and Indonesian strings. Nobody else, and no other machine, can install the workflow.

## Goals / Non-Goals

**Goals**

- Publish all 18 Skills as one Bundle from a public GitHub repository, installable with `npx skills add sonysetiawan97/ss-workflow`, globally (`-g`) or per project.
- Ship Skills under their Published names (no Dev suffix).
- Make the PM and its Workers (`ss-pm`, `ss-implementator`, `ss-review-commit`) work from any install location, without Agent files.
- Release it as version 1.0.0.

**Non-Goals**

- No npm package.
- No Claude Code plugin and no Agent files (see `docs/adr/0001-workers-without-agent-files.md`).
- No dev-install script; the local `-001` copies stay as they are and are not regenerated.
- No Sagara version. That will be a separate rewrite after this Bundle has launched and been tried.

## Users / Actors

- **Maintainer**: the repo owner, who edits the Skills in this repo from now on.
- **Installer**: anyone who installs the Bundle on a Host with `npx skills add`.

## Requirements

1. The repo `sonysetiawan97/ss-workflow` is public on GitHub.
2. The repo contains all 18 Skills under `skills/<published-name>/`, copied once from `~/.agents/skills/ss-*-001/` with their reference files.
3. Every Skill drops the Dev suffix in its folder name, its `SKILL.md` frontmatter `name:`, and every reference to any Skill in the Bundle.
4. No Agent file is in the repo.
5. `ss-pm` dispatches each Worker through the Host's generic subagent (Claude Code `general-purpose`, opencode `task`) or a headless process, never through a named Agent file.
6. No Skill hardcodes `~/.agents/skills/`. A Skill that points to another Skill's `SKILL.md` uses a sibling path (`../ss-<name>/SKILL.md`), resolved to an absolute path from its own base directory before it goes into a subagent prompt. This applies to `ss-implement`, `ss-implementator`, `ss-pm` and `ss-review-commit`.
7. No Skill names a Sagara skill (`sagara-ship`, `sagara-review`, `sagara-code-reviewer`); each is replaced by a generic phrase.
8. Every user-facing and marker string is in English. Markers that other Skills read (`Selesai` → `Done`, `Terverifikasi:` → `Verified:`) change in every Skill that writes or reads them, in the same change.
9. `README.md` holds only: the install command (with the `-g` variant), the workflow diagram, a one-line list of the 18 Skills, and the MIT license.
10. `LICENSE` is MIT.
11. `CHANGELOG.md` has a `1.0.0` entry for the initial release, and the release commit is tagged `v1.0.0`.
12. The local `~/.agents/skills/ss-*-001` copies are left untouched.
13. Tickets can carry `Status: ready-for-human` (a Human ticket). `ss-ticket` lets the user mark a ticket for a person during the quiz step. `ss-pm` never dispatches a Human ticket. Its run report lists every Human ticket whose blockers are all `done`. The person sets `done` by hand.
14. The PRD, spec and ticket templates each live in one Format file: `ss-prd/PRD-FORMAT.md`, `ss-spec/SPEC-FORMAT.md` and `ss-ticket/TICKET-FORMAT.md`. Each `SKILL.md` says to use its template exactly: the same headings in the same order, nothing added or renamed, and "None decided." for an empty section.
15. All three templates open with the same header block, `Date` and `Source`. Tickets add `Status` and `Blocked by`.
16. `ss-workflow` links to the Format files instead of listing their sections.
17. `docs/prd.md`, `docs/spec/publish-bundle.md` and the `publish-bundle` tickets are rewritten to the new templates. The spec loses its line numbers.
18. Publishing (push and tag `v1.0.0`) is the last ticket, a Human ticket.

## Acceptance Criteria

1. `npx skills add sonysetiawan97/ss-workflow -g` lists 18 Skills, all with Published names.
2. A project install (`npx skills add sonysetiawan97/ss-workflow` in a repo) installs the same 18 Skills.
3. `grep -rE 'ss-[a-z-]+-[0-9]{3}' skills/` returns nothing.
4. `grep -r '~/.agents/skills' skills/` returns nothing.
5. `grep -ri 'sagara' skills/` returns nothing.
6. `grep -rE 'Selesai|Terverifikasi|Lanjut|Kerjakan|tiket|seluruh|sebut|semua|jumlah lain' skills/` returns nothing.
7. Each Skill's frontmatter `name:` equals its folder name.
8. On Claude Code, `/ss-pm` on one `ready` ticket dispatches both Workers through `general-purpose` subagents and produces one local commit.
9. `README.md`, `LICENSE` (MIT) and `CHANGELOG.md` (with `1.0.0`) exist, and tag `v1.0.0` exists on GitHub.
10. `~/.agents/skills/ss-*-001` are unchanged.
11. With one `ready` ticket and one Human ticket whose blockers are `done`, `/ss-pm` works only the `ready` ticket, and its report names the Human ticket as workable.
12. Each of the three Format files exists, and its `SKILL.md` contains no inline copy of the template.
13. `docs/prd.md`, `docs/spec/publish-bundle.md` and every `publish-bundle` ticket match their Format file heading for heading.

## Constraints / Assumptions

- skills.sh has no publish step; it lists Skills from `npx skills` install telemetry.
- `npx skills add` does not install from npm; it reads `skills/<name>/SKILL.md` from a git repo.
- Hosts install to different directories (Claude Code `~/.claude/skills/`, pi `~/.agents/skills/`, opencode `~/.config/opencode/skills/`, or `.agents/skills/` per project), with every Skill of the Bundle side by side. Sibling paths rely on that.
- **Unverified**: requirement 6 assumes the Host tells `ss-pm` its own base directory. Claude Code does ("Base directory for this skill"); pi and opencode are not checked.
- The repo is personal (`ss`) and kept separate from office work.

## Changelog

<!-- newest first -->

- **2026-11-15**: Rewritten to `ss-prd/PRD-FORMAT.md`: the `Date` and `Source` header block is new. No decision changed.

- **2026-11-15**: Added Human tickets (`ready-for-human`, skipped by `ss-pm`, listed in its report once unblocked) and strict, shared Format files for the PRD, spec and ticket templates. Requirements 13–18 and acceptance criteria 11–13 are new. Publishing becomes the last ticket, worked by a person. Decided after the ticket breakdown.

- **2026-11-15**: Acceptance criterion 6 widened to catch every Indonesian string found in `ss-verify` and `ss-pm` (`seluruh`, `sebut`, `semua`, `jumlah lain`). Found while writing the spec.

- **2026-11-15**: Initial PRD for publishing the ss workflow Bundle via `npx skills add`. Settled in the grilling session: GitHub-only distribution, Published names without the Dev suffix, Workers without Agent files (ADR 0001), sibling paths, no Sagara references, English strings, v1.0.0 with a changelog.
