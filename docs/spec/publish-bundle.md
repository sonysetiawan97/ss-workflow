# Spec: Publish the ss workflow Bundle

- **Date:** 2026-11-15
- **Source:** the grilling session of 2026-11-15, `docs/prd.md` (same feature), `GLOSSARY.md`, `docs/adr/0001-workers-without-agent-files.md`

## Problem Statement

The 18 ss workflow Skills live only as local `ss-*-001` folders in `~/.agents/skills/`, outside git. They carry local-only details (Dev suffix, hardcoded `~/.agents/skills/` paths, Agent files, Sagara skill names, Indonesian strings), so nobody can install them online.

## Solution

Copy the 18 Skills once into `skills/` of this repo, strip the Dev suffix everywhere, and make them install-location independent: sibling paths instead of `~/.agents/skills/`, and Workers dispatched through generic subagents instead of Agent files. Clean out Sagara names and Indonesian strings, add `README.md`, `LICENSE` and `CHANGELOG.md`, then push to the public repo `sonysetiawan97/ss-workflow` and tag `v1.0.0`. Installers then use `npx skills add sonysetiawan97/ss-workflow` (add `-g` for global).

## User Stories

1. As an Installer, I run `npx skills add sonysetiawan97/ss-workflow -g` and get all 18 Skills under their Published names on my Host.
2. As an Installer, I install the Bundle into one project instead, and the PM still finds its Workers.
3. As an Installer on Claude Code, I run `/ss-pm` on a `ready` ticket and get one local commit, with no Agent file installed.
4. As an Installer, every prompt and marker a Skill writes is in English.
5. As the Maintainer, I edit the Skills in this repo, while my local `-001` copies keep working untouched.

## Implementation Decisions

**Repo layout**

```
skills/ss-<name>/SKILL.md (+ reference files)   # 18 folders
docs/prd.md, docs/spec/, docs/adr/
GLOSSARY.md
README.md
LICENSE
CHANGELOG.md
```

`npx skills add` discovers `skills/<name>/SKILL.md` without extra config.

**The 18 Skills and their reference files**

| Published name | Reference files |
|---|---|
| `ss-code-review` | none |
| `ss-codebase-design` | `DEEPENING.md`, `DESIGN-IT-TWICE.md` |
| `ss-commit` | none |
| `ss-diagnose` | none |
| `ss-domain-modeling` | `ADR-FORMAT.md`, `GLOSSARY-FORMAT.md` |
| `ss-grilling` | none |
| `ss-implement` | none |
| `ss-implementator` | none |
| `ss-improve-codebase-architecture` | `HTML-REPORT.md` |
| `ss-pm` | none |
| `ss-prd` | `PRD-FORMAT.md` |
| `ss-review-commit` | none |
| `ss-spec` | `SPEC-FORMAT.md` |
| `ss-tdd` | `mocking.md`, `tests.md` |
| `ss-ticket` | `TICKET-FORMAT.md` |
| `ss-triage` | none |
| `ss-verify` | none |
| `ss-workflow` | none |

**Suffix removal**

Rewrite every `ss-<name>-001` token to `ss-<name>`. This covers folder names, frontmatter `name:`, prose, `/ss-*-001` commands, and relative paths such as `../ss-domain-modeling-001/ADR-FORMAT.md`. The bare "ss-001" wording (for example "every ss-001 skill" in `ss-workflow`) becomes "ss".

**Sibling paths and Worker dispatch**

These are the hardcoded `~/.agents/skills/` references today:

- `ss-implement`, review step: the review subagent reads `ss-code-review`.
- `ss-implementator`, procedure: reads `ss-implement`.
- `ss-pm`, Dispatch section: the Worker prompts for `ss-implementator` and `ss-review-commit`.
- `ss-review-commit`, procedure: reads `ss-code-review` and `ss-commit`.

Each becomes a sibling path, `../ss-<name>/SKILL.md`, relative to the Skill's own base directory. Any Skill that puts such a path into a subagent or headless prompt resolves it to an absolute path first, because the fresh context does not know the base directory.

In `ss-pm`'s Dispatch section, preference 1 becomes the Host's generic subagent (Claude Code `general-purpose`, opencode `task`). The `agent ss-implementator-001 / ss-review-commit-001` wording goes away. Preference 2 stays: a headless process (`pi -p --no-session`, `opencode run`, `claude -p`).

The Worker skills no longer mention an Agent file. `~/.claude/agents/ss-*-001.md` is not copied.

**Sagara names**

- `ss-commit`, opening paragraph: "a shipping skill such as `sagara-ship`" becomes "a shipping skill".
- `ss-code-review`, opening paragraph: "(e.g. `sagara-review`, `sagara-code-reviewer`)" is dropped, leaving "the repo's own tooling and governance reviewer".

**English strings and markers**

| Where | Today | Becomes |
|---|---|---|
| `ss-implement`, `ss-review-commit` (completion note) | `**Selesai:**` | `**Done:**` |
| `ss-verify` (every mention) | `Selesai` | `Done` |
| `ss-verify` (verified stamp) | `**Terverifikasi:** … · seluruh AC <pass\|unverified-sebut-siapa>` | `**Verified:** … · all AC <pass\|unverified-name-who>` |
| `ss-workflow` (`ss-verify` on-ramp) | `Selesai`, `Terverifikasi:` | `Done`, `Verified:` |
| `ss-pm` (confirm with bare `max`) | "Akan mengerjakan <count> tiket: <list>. Lanjut?" | "About to work <count> tickets: <list>. Continue?" |
| `ss-pm` (ask with no `max`) | "Ada <count> tiket ready: <list>. Kerjakan berapa? (a) 3, (b) semua, (c) jumlah lain." | "<count> tickets ready: <list>. How many? (a) 3, (b) all, (c) another number." |

The writers (`ss-implement`, `ss-review-commit`) and the reader (`ss-verify`) change in the same commit. This keeps the `Done:` marker contract consistent.

**Release files**

- `README.md`: install command (project and `-g`), the main-flow diagram from `ss-workflow`, one line per Skill, MIT license line. Nothing else.
- `LICENSE`: MIT, copyright holder the repo owner.
- `CHANGELOG.md`: one `## 1.0.0 - <release date>` entry, "Initial release".
- Tag `v1.0.0` on the release commit, pushed with the branch.

**Human tickets**

- The ticket status list becomes `ready · ready-for-human · in-progress · done · blocked — <reason>`.
- `ss-ticket`'s quiz step asks which tickets a person works. Those tickets are written with `Status: ready-for-human`.
- Whether a ticket is workable is worked out, never written: its status is `ready` or `ready-for-human`, and every blocker is `done`. No status changes when a ticket unblocks.
- `ss-pm` picks only `ready` tickets. Its run report adds one line per Human ticket whose blockers are all `done`: `<NN> — <title>: ready-for-human, all blockers done`.
- The person sets `done` by hand.
- `ss-workflow` describes Human tickets in its `ss-ticket` and `ss-pm` stages.

**Format files**

- Each template moves out of its `SKILL.md` into its Format file: `ss-prd/PRD-FORMAT.md`, `ss-spec/SPEC-FORMAT.md` and `ss-ticket/TICKET-FORMAT.md`. This is the same pattern as `ss-domain-modeling/ADR-FORMAT.md`.
- Each `SKILL.md` keeps no inline copy of the template. It says to use the template exactly: the same headings in the same order, nothing added or renamed, and "None decided." for an empty section.
- All three templates open with one header block: `Date` and `Source`. Tickets add `Status` and `Blocked by`.
- `ss-workflow` names the Format file for each document instead of listing its sections.
- The existing `docs/prd.md`, this spec and the `publish-bundle` tickets are rewritten to the new templates. This spec drops its line numbers. The static checks under Testing Decisions define what must change.

## Testing Decisions

The seams are the published file tree and one live install. There is no code, so there are no unit tests.

1. **Static checks** over `skills/`, run before release. They map to PRD acceptance criteria 3 to 7.
   ```sh
   grep -rE 'ss-[a-z-]+-[0-9]{3}' skills/                     # expect nothing
   grep -r '~/.agents/skills' skills/                          # expect nothing
   grep -ri 'sagara' skills/                                   # expect nothing
   grep -rE 'Selesai|Terverifikasi|Lanjut|Kerjakan|tiket|seluruh|sebut|semua|jumlah lain' skills/  # expect nothing
   for d in skills/*/; do n=$(basename "$d"); grep -q "^name: $n$" "$d/SKILL.md" || echo "MISMATCH $n"; done  # expect nothing
   ls skills | wc -l                                           # expect 18
   ```
2. **Local install** before pushing: `npx skills add ./ -g` from the repo lists 18 Skills with Published names. They sit beside the `-001` copies without clashing.
3. **Online install** after pushing (PRD AC 1, 2): `npx skills add sonysetiawan97/ss-workflow -g`, and a project install in a scratch repo.
4. **End-to-end PM run** on Claude Code (PRD AC 8): in a scratch repo with one `ready` ticket, `/ss-pm` dispatches both Workers through `general-purpose` and leaves one local commit.
5. **Local copies untouched** (PRD AC 10): compare `~/.agents/skills/ss-*-001` before and after, for example with `shasum` over the tree.

## Out of Scope

- npm package, Claude Code plugin, Agent files.
- A dev-install script or regeneration of the `-001` copies.
- Migrating `Selesai:` markers in tickets already written in other repos. Those repos keep using the local `-001` Skills.
- A Sagara version.
- Verifying pi and opencode end-to-end. Only Claude Code is tested (story 3).

## Further Notes

- **Defect found**: `~/.agents/skills/ss-spec-001/SKILL.md` has only its frontmatter and one graphify line (6 lines). It has no instructions for the spec sections or the process. Publishing it as is ships a near-empty Skill. The user must decide whether to restore its body before release.
- **Unverified**: sibling-path resolution assumes the Host gives a Skill its own base directory. Claude Code does. pi and opencode are not checked. If a Host does not, the PM cannot build an absolute Worker path there.
- After this, a Claude Code Installer with both the local `-001` copies and the published Skills sees each Skill twice, once per name. This is expected on the Maintainer's machine.
