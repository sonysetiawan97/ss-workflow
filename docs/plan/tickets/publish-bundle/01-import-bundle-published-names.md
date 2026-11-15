# 01 — Import the Bundle with Published names

- **Date:** 2026-11-15
- **Status:** done · unverified: actual `npx skills add ./ -g` not run (global side effect); `npx skills add ./ --list` found 18 Skills with Published names
- **Blocked by:** None — can start immediately
- **Source:** docs/spec/publish-bundle.md
- **Selesai:** 2026-11-15 · ss-workflow@uncommitted

## What to build

All 18 Skills now live in this repo under their Published names. Each is copied once from the Maintainer's local Dev suffix copies, with its reference files. The Dev suffix is gone everywhere: folder names, frontmatter `name:`, prose, slash commands, relative paths, and the bare "ss-001" wording, which becomes "ss". An Installer who installs the Bundle from a local checkout gets 18 Skills that sit beside the Maintainer's `-001` copies without clashing. No Agent file is copied.

## Acceptance criteria

- [ ] The repo holds exactly 18 Skill folders, matching the spec's inventory table, each with its listed reference files.
- [ ] No Skill name, reference, or path in the Bundle carries a Dev suffix (spec static check 1 is empty).
- [ ] Every Skill's frontmatter `name:` equals its folder name (spec static check 5 is empty).
- [ ] No Agent file is in the repo.
- [ ] A local global install from the repo checkout lists 18 Skills with Published names.
- [ ] The Maintainer's local `-001` copies are byte-identical before and after (checksum over the tree).
