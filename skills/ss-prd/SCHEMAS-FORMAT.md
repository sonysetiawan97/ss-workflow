# Schemas Format

Companion of the PRD, at `docs/schemas.md`. Written only when `ss-prd` chooses it for the repo's kind.

## Template

```md
# Schemas: <feature name>

Companion of [PRD](prd.md). The PRD holds the rules and the reasons; this file holds the data shape. Changes are logged in the PRD changelog.

## `<table_name>`

<One line: what one row is, and which app writes it.> Serves PRD requirement <N>.

| Column | Type | Null | Default | Notes |
|---|---|---|---|---|
| `id` | BIGINT UNSIGNED, PK | no | auto | |
| `<column>` | `<type>` | yes/no | `<value>` / - | <enum values, collation, format, ADR link> |

**Keys:** `<UNIQUE / INDEX name (columns)>` — <the query or rule it serves>.

**Relations:** `<column>` → `<other_table>.<column>` (<on delete behaviour>).

<!-- Repeat one section per table. Undecided column or key → write "None decided." in Notes. -->
```

## Rules

- Use the template exactly: the same headings and bold lead-ins, nothing added or renamed. Repeat the `##` section once per table.
- A cell or field with no settled decision behind it holds only "None decided."
- No changelog here: every change goes in the PRD's changelog line.
