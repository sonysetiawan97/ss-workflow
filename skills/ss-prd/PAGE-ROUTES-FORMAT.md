# Page Routes Format

Companion of the PRD, at `docs/page-routes.md`. Written only when `ss-prd` chooses it for the repo's kind.

## Template

```md
# Page Routes: <feature name>

Companion of [PRD](prd.md). The PRD holds the rules and the reasons; this file holds the routes and what each page shows. Changes are logged in the PRD changelog.

| Route | Page | Access | Purpose | PRD req |
|---|---|---|---|---|
| `/<path>/:<param>` | <page name> | <public / role / guard> | <one line> | <N> |

## `<route>`

<One line: what the user does here.>

**Params / query:** `<name>` — <type, default, what it filters or selects>.

**Data:** <endpoint it calls, e.g. `GET /api/v1/<resource>` from the backend's API list>.

**States:** loading, empty, error, <no access> — what the page shows in each.

**Navigation:** <where its links and actions go>.

<!-- Repeat one section per route. Undecided state or data source → write "None decided." -->
```

## Rules

- Use the template exactly: the same headings and bold lead-ins, nothing added or renamed. Repeat the `##` section once per route.
- A cell or field with no settled decision behind it holds only "None decided."
- No changelog here: every change goes in the PRD's changelog line.
