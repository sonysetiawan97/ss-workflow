# ss-workflow

The ss engineering workflow, packaged as a set of agent skills and published so it can be installed online.

## Language

**Bundle**:
The full set of `ss-*` skills published together from one repository, because the skills reference each other by name and relative path.
_Avoid_: pack, collection, suite

**Skill**:
One `ss-*` folder containing a `SKILL.md` and its reference files.
_Avoid_: command, prompt

**PM**:
The `ss-pm` skill, which runs in the main session and dispatches Workers ticket by ticket.
_Avoid_: orchestrator, manager agent

**Worker**:
A skill the PM runs in a fresh context: `ss-implementator` or `ss-review-commit`.
_Avoid_: agent, subagent

**Agent file**:
A Host-specific definition that registers a Worker as a named subagent, such as `~/.claude/agents/ss-implementator-001.md`.
_Avoid_: agent, worker

**Human ticket**:
A ticket with `Status: ready-for-human`: a person works it, and the PM never dispatches it. It is workable once all its blockers are `done`.
_Avoid_: manual ticket, human task

**Format file**:
The single reference file that holds a document's template, such as `ss-spec/SPEC-FORMAT.md`.
_Avoid_: template file, schema

**Host**:
The coding-agent program that loads and runs skills, such as Claude Code, pi or opencode.
_Avoid_: agent, runtime, client

**Published name**:
A Skill's name as users install it, with no version suffix, such as `ss-grilling`.
_Avoid_: public name, release name

**Dev suffix**:
The local-only `-NNN` suffix, such as `-001`, added to every Skill name so a test copy can sit beside the published one.
_Avoid_: version, revision
