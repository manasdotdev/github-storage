# Building a Database Client with AI Schema Awareness

cweijan’s Database Client already solves connections, browsing, and query UX. The gap is **schema → AI context**. Building “the same product + awareness” means treating metadata as a first-class runtime for the model, not as UI only.

## What “database context awareness” actually means

The model needs a **live, structured view** of:

- connections / current DB
- tables, columns, types, nullability, PKs/FKs
- indexes, views, enums
- optional: sample rows, cardinality, comments

Without that, it guesses column names. With it, it writes correct SQL.

## Product shape (two viable paths)

### Path A — Full client + built-in AI tab (cweijan-like)

You own the whole UX: tree, tabs, results grid, **and** an AI chat that injects schema.

Best if you want a standalone product people open for DB work.

### Path B — Thin client + MCP server (fastest “awareness”)

Ship (or reuse) connection + introspection, then expose tools/resources over [MCP](https://code.visualstudio.com/docs/agent-customization/mcp-servers) so Cursor/VS Code Agent can call `list_tables`, `get_table_schema`, `execute_query`.

This is how a lot of “AI knows my DB” tooling ships today. You get awareness without rebuilding Copilot/Cursor.

For a first product, **B for awareness + a good query UI** is usually the right split.

## Core architecture

```text
┌─────────────────────────────────────────┐
│  Extension / App (UI)                   │
│  connections · schema tree · SQL editor │
│  results · AI chat (optional)           │
└───────────────┬─────────────────────────┘
                │
┌───────────────▼─────────────────────────┐
│  Context layer (the missing piece)      │
│  introspect → cache → summarize → inject│
└───────────────┬─────────────────────────┘
                │
┌───────────────▼─────────────────────────┐
│  Drivers (pg / mysql / sqlite / …)      │
└─────────────────────────────────────────┘
```

The **context layer** is the product differentiator.

## How to make the AI schema-aware

### 1. Introspect from the live connection

Don’t parse migrations alone. Query the catalog:

- Postgres: `information_schema` / `pg_catalog`
- MySQL: `information_schema`
- SQLite: `PRAGMA table_info`

Cache per connection; invalidate on DDL or explicit refresh.

### 2. Compress before injecting (token budget)

Full DDL for a large DB blows the context window. Use layers:

| Level | When | Content |
|--------|------|---------|
| L0 | Always | DB name, engine, list of table names |
| L1 | On demand / relevant tables | Columns, types, PK/FK |
| L2 | When writing joins / filters | Indexes, enums, comments |
| L3 | Rarely | 3–5 sample rows (masked) |

Retrieval pattern:

1. User: “top customers by revenue last month”
2. Tool: search tables matching `customer|order|payment`
3. Fetch L1 for those tables only
4. Generate SQL
5. Optionally `EXPLAIN` / run (read-only)

### 3. Give the model tools, not a giant dump

Better than pasting the whole schema into the system prompt:

- `list_schemas` / `list_tables`
- `describe_table(name)`
- `search_schema(query)` (fuzzy over table/column names)
- `get_foreign_keys(table)`
- `sample_rows(table, limit)` (masked)
- `execute_sql(sql)` (default **read-only**, with confirm for writes)

That is the same idea as DB MCP servers (`get_schema`, `get_table_info`, `execute_query`).

### 4. Wire “current editor context”

Awareness isn’t only catalog metadata. Also inject:

- active connection + database
- selected text / open `.sql` file
- last result columns (so “chart this” / “fix this query” works)
- recent query history for that project

That’s what makes it feel like the AI “lives inside” the client.

## Minimal MVP to validate the idea

1. One engine first (Postgres is enough).
2. Connection store (host/user/db; secrets in OS keychain).
3. Schema cache + `describe_table`.
4. Either:
   - MCP server so Cursor Agent can use those tools, **or**
   - a chat panel that calls an LLM API with tool-calling.
5. Read-only SQL runner + result grid.
6. Safety: statement timeout, row limit, no multi-statement by default, write requires confirm.

If that loop feels magical (“write a join across orders and customers”), then expand UI toward cweijan (multi-DB, ERD, export, etc.).

## What makes this better than bolting ChatGPT onto SQL

| Naive | Aware product |
|--------|----------------|
| Paste schema once | Live introspection + cache |
| One big system prompt | Layered retrieval + tools |
| Model invents columns | Model must `describe_table` first |
| Runs anything | Read-only default, policy for DML |
| No link to UI | Same connection the tree uses |

## Positioning options

1. **“Database Client with Agent”** — compete with cweijan on UX; AI is a tab.
2. **“Schema MCP for Cursor”** — compete on awareness; piggyback on Cursor/VS Code Agent (fastest GTM).
3. **Hybrid** — client for humans, MCP for agents; one introspection core, two surfaces.

Given the pain is specifically the AI tab not knowing tables/fields, option **2 or 3** hits the pain fastest.

## Practical stack sketch

- Extension: VS Code Extension API (webview for results/chat)
- Drivers: `pg`, `mysql2`, better-sqlite3
- Context: JSON schema cache + embedding search optional later
- AI: tool-calling against OpenAI/Anthropic **or** expose MCP and let Cursor own the model
- Security: never put passwords in prompts; mask PII in samples; allowlist schemas

## Bottom line

Don’t start by cloning every cweijan feature. Build a **shared introspection + context service**, then surface it as MCP tools (and optionally an AI chat). Schema awareness is retrieval + tools on a live catalog, not a smarter prompt.

## Next steps (optional deeper dives)

- Sketch MCP tool contracts + prompt policy
- Sketch a Postgres-only MVP architecture against a local Docker Postgres setup
