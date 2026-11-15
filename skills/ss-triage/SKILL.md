---
name: ss-triage
description: Triage backlog requests into agent-ready tickets under <project-root>/docs/plan/backlog-review/ — verify, grill, dedup against .out-of-scope/, apply five triage labels. No issue tracker; tickets are markdown files in the repo.
disable-model-invocation: true
---

Move pending **backlog requests** through a state machine of five triage labels and write the outcome into the repo — no external issue tracker. The "tracker" is a set of markdown files under `docs/plan/backlog-review/`, one file per request, each carrying this label vocabulary:

- **category** (one of): `bug` · `enhancement`
- **state** (one of): `needs-triage` · `needs-info` · `ready-for-agent` · `ready-for-human` · `wontfix`
- **execution state** (set by `ss-implement`, never by triage): `in-progress` · `done`

Every request file carries exactly one category and one state. Triage owns the five triage states; once `ss-implement` picks up a `ready-for-agent` file it moves the state to `in-progress`, then `done`. Triage skips `in-progress` and `done` files unless the user re-opens one, which puts it back to `needs-triage`. A `ready-for-agent` file contains a full [agent brief](#agent-brief-template) that a fresh session can pick up and implement without shared context.

## Inputs

A "request" is one of:

1. A row in a `docs/plan/backlog-*.md` backlog whose status is `todo` — the most common source.
2. A standalone request the user describes in the conversation (a bug report, a feature ask, an external idea).
3. An existing `docs/plan/backlog-review/*.md` file being re-opened or re-triaged.

## Process

### 1. Survey the queue

- List every `todo` row across `docs/plan/backlog-*.md` and every `docs/plan/backlog-review/*.md` whose state is `needs-triage`, `needs-info` (with new information since the last note), or unlabeled.
- Present three buckets, oldest first:
  1. **Untriaged**: no state label yet.
  2. **`needs-triage`**: evaluation in progress.
  3. **`needs-info` with new input**: reporter/user added something since the last triage note.
- One line per item: id · title · current state. Let the user pick, or if the user names a specific item, go straight to step 2 for it.

### 2. Triage one request

For the chosen request, in order:

**a. Gather context.** Read the request's full text (backlog row + any linked files; or the conversation context for a described request). Parse prior triage notes so resolved questions are not re-asked. Explore the codebase with the project's domain vocabulary, respecting ADRs in the area being touched. If `graphify-out/graph.json` exists, prefer it over grepping: `graphify query "<concept>"` finds related code paths by domain concept.

Run two checks against the codebase:

- **Redundancy**: search for an existing implementation of the requested behavior *by domain concept*, not just the request's wording. Report where you looked. Found → this is a `wontfix` of the "already implemented" kind (step 2f).
- **Prior rejection**: read every file under `.out-of-scope/`. Surface any that resembles this request.

**b. Verify the claim.** Before recommending a state, check that the request holds up. For a bug, attempt to reproduce from the described steps and report confirmed / failed / insufficient detail. For an enhancement, confirm the current behavior the feature builds on actually exists as described. Verification makes the eventual brief stronger; "insufficient detail" is a strong `needs-info` signal.

**c. Recommend.** State your recommended category and state with reasoning, plus a brief codebase summary relevant to the request. Wait for the user's direction.

**d. Grill (if needed).** If the request needs fleshing out to reach `ready-for-agent`, invoke the `ss-grilling` skill — one round of the frontier at a time — sharpening domain terms. If `ss-domain-modeling` territory is crossed, update `GLOSSARY.md` / ADRs inline as decisions land.

**e. Apply the state.** Write or update `docs/plan/backlog-review/<request-slug>.md` (kebab-case slug, one file per request; if a `todo` backlog row spawned it, record the source row id inside the file but do not edit the backlog row's status — that is the execution skills' job):

- `ready-for-agent` → the file must contain the full [agent brief](#agent-brief-template).
- `ready-for-human` → same structure, plus a **Why not an agent?** section naming what a human must decide (judgment calls, external access, design trade-offs, manual testing).
- `needs-info` → append a [triage notes block](#needs-info-template); do not repeat already-resolved points.
- `wontfix` → see step 2f.
- `needs-triage` → apply the label; optional partial-progress comment.

**f. `wontfix` handling.** The closing note depends on *why*:

- **Already implemented**: point to where the behavior lives. Do **not** write to `.out-of-scope/` — that KB is for rejected requests, not built ones.
- **Rejected bug**: a short polite explanation of why it will not be actioned.
- **Rejected enhancement**: write or append to `.out-of-scope/<concept>.md` (see [Out-of-scope KB](#out-of-scope-knowledge-base)), link it from the request file, then mark `wontfix`.

Update the state line in the request file. Report what changed.

### 3. Quick state override

If the user says "move <request> to `ready-for-agent`", trust them: apply the state directly, skipping grilling. Confirm the exact changes (state line, brief, any `.out-of-scope/` write) before acting. If moving to `ready-for-agent` without a grilling session, ask whether to write the brief now or leave it for the implementer to fill in.

### 4. Resume

If a request file already carries triage notes, read them, check whether the user has answered any outstanding questions, and present the updated picture before continuing. Never re-ask a resolved question.

## Request file layout

```markdown
# <request title>

- **Date:** YYYY-MM-DD
- **Source:** <backlog row id + backlog file, or "conversation YYYY-MM-DD">
- **Category:** bug | enhancement
- **Status:** needs-triage | needs-info | ready-for-agent | ready-for-human | wontfix | in-progress | done
- **Verification:** <confirmed / failed / insufficient detail> — one line of evidence

---
<agent brief, human notes, or wontfix record — see templates below>
```

Every block written to a request file during triage starts with:

```
> *Generated by AI during triage.*
```

## Agent brief template

For `ready-for-agent` requests. The brief is the authoritative contract: a fresh session with no shared context should be able to implement from it.

```markdown
> *Generated by AI during triage.*

## Agent Brief

**Category:** bug / enhancement
**Summary:** one line — what needs to happen

**Current behavior:**
What happens now. For a bug: the broken behavior. For an enhancement: the status quo the feature builds on.

**Desired behavior:**
What should hold after the work is done. Be specific about edge cases and error conditions.

**Key interfaces:**
- `TypeName`: what changes and why
- `functionName()` return type: current vs. desired
- config shape: any new options

**Acceptance criteria:**
- [ ] testable criterion 1
- [ ] testable criterion 2
- [ ] testable criterion 3

**Out of scope:**
- what must NOT change in this request
- adjacent features that look related but are separate
```

Brief rules:

- **Durable over precise**: name interfaces, types, behavioral contracts. Never file paths or line numbers — they go stale.
- **Behavioral, not procedural**: say what the system should do, not how to implement it.
- **Complete acceptance criteria**: each criterion independently verifiable.
- **Explicit scope boundaries**: state what is out of scope so implementers don't gold-plate.

## needs-info template

```markdown
> *Generated by AI during triage.*

## Triage Notes

**Established so far:**
- point 1
- point 2

**Still needed from the owner:**
- specific question 1
- specific question 2
```

Capture everything resolved during grilling under "Established so far" so the work isn't lost. Questions must be specific and actionable — never "please provide more info".

## Out-of-scope knowledge base

`.out-of-scope/` lives at the repo root, one file per **concept**, not per request:

```markdown
# Dark Mode

<2–4 sentences: what is refused and why, written for a first-time reader>

## Reason

Substantive, durable reasoning. Reference project scope, technical constraints, or strategic
decisions. No temporary excuses ("too busy right now") — those are deferrals, not rejections.

## Prior requests

- <request slug / date>: "original one-line ask"
```

- **Check it** in step 2a. A match → surface it: "this resembles `.out-of-scope/dark-mode.md`, rejected because [reason]. Still so?" User confirms → append the request to that file's list, mark `wontfix`. User reconsiders → delete or update the file, proceed with normal triage.
- **Write to it** only when an *enhancement* is rejected as `wontfix`. Never for "already implemented" closures.
- **Delete** the file if the project changes its mind; the old request files stay as historical records.

## It's working if

- The user says "show me what needs attention" and gets the three buckets without the skill querying any external tracker.
- A `ready-for-agent` file stands alone: a fresh session implements from it without asking a single new question.
- Every state transition is a one-line diff in the request file plus, at most, one `.out-of-scope/` write.
- Nothing in the repo outside `docs/plan/backlog-review/`, `.out-of-scope/`, `GLOSSARY.md`, and ADRs is modified.
