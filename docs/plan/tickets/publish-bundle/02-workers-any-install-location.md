# 02 — Workers that run from any install location

- **Date:** 2026-11-15
- **Source:** docs/spec/publish-bundle.md; docs/adr/0001-workers-without-agent-files.md
- **Status:** done · unverified: AC4 end-to-end `/ss-pm` run on Claude Code with the Bundle installed (needs a main session and an install; not runnable from a Worker) · suggestion: [clean:names] skills/ss-pm/SKILL.md:39 — "because the fresh context does not know it" has an unclear referent for "it"; name it ("does not know this Skill's base directory")
- **Blocked by:** 01 — Import the Bundle with Published names
- **Done:** 2026-11-15 · /Volumes/workspace/ss/skills/ss-workflow@uncommitted

## What to build

An Installer can install the Bundle globally or per project, on any Host, and the PM still runs its Workers. No Skill points at a fixed home-directory install path any more. A Skill that needs another Skill's instructions points at it as a sibling of its own folder. Before such a path goes into a subagent or headless prompt, it is resolved to an absolute path from the Skill's own base directory. The PM dispatches each Worker through the Host's generic subagent (Claude Code `general-purpose`, opencode `task`), or else through a headless process. It never dispatches through a named Agent file, and no Worker Skill mentions one.

## Acceptance criteria

- [ ] No Skill in the Bundle references the fixed `~/.agents/skills/` path (spec static check 2 is empty).
- [ ] `ss-implement`, `ss-implementator`, `ss-pm` and `ss-review-commit` reference other Skills by sibling path, and say to resolve it to an absolute path before putting it into a prompt.
- [ ] `ss-pm`'s dispatch order is: generic subagent first, headless process second, never inline. No Agent file is named.
- [ ] On Claude Code, with the Bundle installed from this repo and no Agent file present, `/ss-pm` on one `ready` ticket in a scratch repo dispatches both Workers as `general-purpose` subagents and leaves exactly one local commit.
