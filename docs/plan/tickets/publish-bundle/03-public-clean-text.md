# 03 — Public-clean text

- **Date:** 2026-11-15
- **Status:** done
- **Selesai:** 2026-11-15 · ss-workflow@uncommitted
- **Blocked by:** 01 — Import the Bundle with Published names
- **Source:** docs/spec/publish-bundle.md

## What to build

Every prompt, marker, and line an Installer sees from the Bundle is in English, and no Skill names an office (Sagara) skill. The completion marker becomes `Done:` and the verification marker becomes `Verified:`. These change in the same commit in every Skill that writes them (`ss-implement`, `ss-review-commit`) and the one that reads them (`ss-verify`), and also in the workflow map, so the contract between them never breaks. The PM's two count questions are asked in English. The shipping-skill and governance-reviewer mentions use generic phrases instead of Sagara skill names.

## Acceptance criteria

- [ ] No Indonesian string remains in the Bundle (spec static check 4 is empty).
- [ ] No Skill mentions Sagara (spec static check 3 is empty).
- [ ] `ss-implement` and `ss-review-commit` write a `Done:` line in the same shape the old `Selesai:` line had, and `ss-verify` reads `Done:` everywhere it read `Selesai`.
- [ ] `ss-verify` writes a `Verified:` line ending in `all AC <pass|unverified-name-who>`.
- [ ] `ss-pm` asks: "About to work <count> tickets: <list>. Continue?" and "<count> tickets ready: <list>. How many? (a) 3, (b) all, (c) another number."
- [ ] `ss-workflow`'s verify on-ramp names `Done` and `Verified:`.
