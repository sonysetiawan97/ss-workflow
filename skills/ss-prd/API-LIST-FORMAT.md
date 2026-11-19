# API List Format

Companion of the PRD, at `docs/api-list.md`. Written only when `ss-prd` chooses it for the repo's kind.

## Template

~~~md
# API List: <feature name>

Companion of [PRD](prd.md). The PRD holds the rules and the reasons; this file holds the endpoint contract. Changes are logged in the PRD changelog. Once the repo generates an OpenAPI document, link it here and keep only what it cannot show: who calls each endpoint and why.

| Method | Path | Auth | Caller | Purpose | PRD req |
|---|---|---|---|---|---|
| `GET` | `/api/v1/<resource>` | <role / token> | <client app or actor> | <one line> | <N> |

## `<METHOD> <path>`

<One line: what it does.>

**Request:** path params, query (name, type, default, allowed values), body fields.

**Response `<status>`:**

```json
{ "<field>": "<type>" }
```

**Errors:**

| Status | Code | When |
|---|---|---|
| 400 | `<CODE>` | <condition> |

<!-- Repeat one section per endpoint. Undecided field or error → write "None decided." -->
~~~

## Rules

- Use the template exactly: the same headings and bold lead-ins, nothing added or renamed. Repeat the `##` section once per endpoint.
- A cell or field with no settled decision behind it holds only "None decided."
- No changelog here: every change goes in the PRD's changelog line.
