# Workers run as generic subagents, with no Agent files and no Claude Code plugin

The Bundle is published only through `npx skills add` (skills.sh), which installs Skills but not Host-specific Agent files. `ss-pm` therefore dispatches each Worker through the Host's generic subagent (Claude Code `general-purpose`, opencode `task`) or a headless process, with a prompt that tells it to read the Worker's `SKILL.md`. That path is resolved from `ss-pm`'s own base directory as a sibling (`../ss-implementator/SKILL.md`), so global and project installs both work. We accepted losing the per-Worker `model: opus` / `effort: medium` pin in exchange for one install channel that works the same on every Host.

## Considered Options

- **Claude Code plugin carrying the Agent files**: rejected. It covers Claude Code only, needs a second install channel, and installs Skills to a different directory, which breaks the hardcoded `~/.agents/skills/` paths.
- **Document a manual copy of the Agent files**: rejected. It is an easy step to skip, and `ss-pm` fails without it.
