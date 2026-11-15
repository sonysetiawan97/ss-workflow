# 07 — Publish v1.0.0

- **Date:** 2026-11-15
- **Source:** docs/spec/publish-bundle.md; docs/prd.md
- **Status:** ready-for-human
- **Blocked by:** 02 — Workers that run from any install location; 03 — Public-clean text; 04 — Release files; 05 — Human tickets; 06 — Format files for PRD, spec and ticket

## What to build

An Installer anywhere can install the Bundle online. The Maintainer runs every static check from the spec, and then makes the repo `sonysetiawan97/ss-workflow` public on GitHub. The Maintainer pushes the branch, tags the release commit `v1.0.0`, and pushes the tag. Pushing to a public repo cannot be undone, so a person does this step, not the PM.

## Acceptance criteria

- [ ] Every static check in the spec returns its expected result immediately before the push.
- [ ] `sonysetiawan97/ss-workflow` is public, and tag `v1.0.0` is on GitHub.
- [ ] `npx skills add sonysetiawan97/ss-workflow -g` lists 18 Skills with Published names.
- [ ] A project install in a scratch repo installs the same 18 Skills.
