# Out-of-Scope Format

An out-of-scope file lives at `.out-of-scope/<concept>.md` at the repo root, one file per **concept**, not per request.

## Template

```markdown
# <Concept name>

<2–4 sentences: what is refused and why, written for a first-time reader>

## Reason

Substantive, durable reasoning. Reference project scope, technical constraints, or strategic
decisions. No temporary excuses ("too busy right now") — those are deferrals, not rejections.

## Prior requests

- <request slug / date>: "original one-line ask"
```

## Rules

- Use the template exactly: the same headings in the same order, nothing added or renamed.
- One file per concept, named `<concept>.md` in kebab-case. A new request for the same concept appends to `Prior requests`.
