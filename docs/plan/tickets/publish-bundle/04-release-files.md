# 04 — Release files

- **Date:** 2026-11-15
- **Status:** done
- **Selesai:** 2026-11-15 · ss-workflow@uncommitted
- **Blocked by:** 01 — Import the Bundle with Published names
- **Source:** docs/spec/publish-bundle.md; docs/prd.md

## What to build

A visitor to the repo can install and understand the Bundle from the front page alone. The README holds only these four things: the install command (project install and the `-g` global variant for `sonysetiawan97/ss-workflow`), the main-flow diagram from `ss-workflow` with Published names, one line per Skill for all 18, and the MIT license line. The repo carries an MIT license with the repo owner as copyright holder, and a changelog whose single `1.0.0` entry records the initial release.

## Acceptance criteria

- [ ] The README contains the install command in both forms, the main-flow diagram, one line for each of the 18 Skills, and the MIT license line. It contains nothing else.
- [ ] Every Skill name in the README is a Published name that exists as a folder in the Bundle.
- [ ] The repo has an MIT `LICENSE` naming the repo owner.
- [ ] The changelog has exactly one entry, `1.0.0`, marked as the initial release.
