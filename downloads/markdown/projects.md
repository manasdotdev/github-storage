

# SQL schema aware extension

cweijan’s client already solves connections, browsing, and query UX. The gap is **schema → AI context**. Building “the same product + awareness” means treating metadata as a first-class runtime for the model, not as UI only.

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

1. One engine first (Postgres is enough given your `pg-practice` setup).
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

Given your complaint is specifically the AI tab not knowing tables/fields, option **2 or 3** hits the pain fastest.

## Practical stack sketch

- Extension: VS Code Extension API (webview for results/chat)
- Drivers: `pg`, `mysql2`, better-sqlite3
- Context: JSON schema cache + embedding search optional later
- AI: tool-calling against OpenAI/Anthropic **or** expose MCP and let Cursor own the model
- Security: never put passwords in prompts; mask PII in samples; allowlist schemas

---

**Bottom line:** don’t start by cloning every cweijan feature. Build a **shared introspection + context service**, then surface it as MCP tools (and optionally an AI chat). Schema awareness is retrieval + tools on a live catalog, not a smarter prompt.

If you want to go deeper next, we can sketch either (a) the MCP tool contracts + prompt policy, or (b) a Postgres-only MVP architecture against your local `pg-practice` Docker setup.


----



Here’s a deeper cut: architecture, discovery, collection format, auth, sync, Cursor fit, and a realistic build path.

## 1. Core thesis

Treat the workspace as an **API graph**, not a list of URLs.

```text
Source code + OpenAPI + runtime traffic
            ↓
     Normalized Endpoint Model
            ↓
   Collection + Env + Auth profiles
            ↓
        Runner + History + Diff
```

Everything else (UI, Run button, Postman export) is a view over that graph.

---

## 2. Normalized endpoint model

This is the contract every discoverer must emit:

```ts
type Endpoint = {
  id: string;                    // stable hash: method + normalized path + mount
  method: "GET" | "POST" | ...;
  path: string;                  // /users/{id}
  mount?: string;                // /api/v1
  operationId?: string;
  summary?: string;
  tags?: string[];

  pathParams: Param[];
  query: Param[];
  headers: Param[];

  body?: {
    contentType: string;
    schema?: JsonSchema;
    example?: unknown;
    required: boolean;
  };

  auth?: AuthRef[];              // references shared auth profiles
  responses?: ResponseShape[];

  source: {
    kind: "openapi" | "framework" | "ast" | "runtime" | "test";
    confidence: 0 | 1 | 2 | 3;   // 3 = OpenAPI/example, 0 = guessed
    locations: CodeLocation[];   // file + line for "Go to handler"
  };
};
```

**Stability of `id` matters.** If regenerating renames everything, saved runs and favorites break. Prefer something like:

`sha1("POST" + "/api/v1" + "/users/{id}")`

not framework-specific names.

---

## 3. Discovery layers (ranked)

### Layer A — Spec-first (highest quality)

If `openapi.yaml` / `swagger.json` / generated docs exist:

- Full paths, schemas, security schemes, examples
- Language-agnostic immediately
- Hook: watch file + “Regenerate from OpenAPI”

**Gap:** many apps never publish OpenAPI, or it drifts.

### Layer B — Framework adapters

Small, high-signal plugins:

| Stack | Signals |
|-------|---------|
| NestJS | `@Controller`, `@Get/@Post`, DTOs + `class-validator` |
| FastAPI / Flask | decorators + Pydantic / type hints |
| Express / Fastify | `app.get/post`, router mounts, zod/joi if present |
| Spring | `@RestController`, `@RequestMapping`, records/DTOs |
| Rails / Laravel | `routes.rb` / `routes/*.php` + controllers |
| ASP.NET | `[ApiController]`, `[HttpGet]`, minimal APIs |
| Go (chi/gin/echo) | route registration + struct tags |

Each adapter outputs the **same** `Endpoint` model. Don’t invent a different collection per language.

### Layer C — AST / LSP fallback

When there’s no OpenAPI and no adapter:

- Find string literals that look like routes (`"/api/..."`, `` `/users/${id}` ``)
- Find nearby HTTP method identifiers
- Infer body from adjacent type / interface / struct

Confidence stays low; UI should show “guessed” and let the user promote/edit.

### Layer D — Runtime capture (optional but powerful)

Local reverse proxy or middleware:

- Intercept `localhost` traffic while the app runs
- Learn real headers, auth cookies, body shapes
- Merge into the graph as `source.kind = "runtime"` with high confidence for examples

This is how you get auth that isn’t declared on the route.

### Layer E — Tests as examples

Parse Playwright/Supertest/pytest/HTTP files:

- Seed “happy path” bodies
- Expected status codes for assertions after Run

---

## 4. Collection + env format

Keep it **plain files in the repo** (Bruno/HTTP Client style), not a proprietary cloud DB.

Suggested layout:

```text
.api/
  api.config.yaml          # base URLs, discoverers, secrets refs
  envs/
    local.yaml
    staging.yaml
  auth/
    bearer.yaml
    cookie-session.yaml
  collections/
    users.http.yaml        # or .bru / .http
  history/                 # gitignored
```

Example env:

```yaml
# .api/envs/local.yaml
name: local
baseUrl: http://localhost:3000
vars:
  userId: "42"
secrets:
  token: env:API_TOKEN      # from process/.env, never inlined
```

Example request (derived, editable):

```yaml
# .api/collections/users.http.yaml
id: post:/api/v1/users
method: POST
url: "{{baseUrl}}/api/v1/users"
auth: bearer
headers:
  Content-Type: application/json
  Accept: application/json
query: {}
body:
  type: json
  value: |
    {
      "email": "a@b.com",
      "name": "Ada"
    }
meta:
  source: nest@src/users/users.controller.ts:41
  confidence: 3
```

**Why files:**

- Diffable in PRs
- Shareable with team
- Works offline
- Export to Postman/Insomnia as a secondary path, not the source of truth

---

## 5. Auth model (must-have, easy to get wrong)

Auth should be **profiles**, not copy-pasted headers per request.

```yaml
# .api/auth/bearer.yaml
type: bearer
token: "{{secrets.token}}"
inject:
  header: Authorization
  scheme: Bearer
```

Also support:

- API key (header/query)
- Basic
- Cookie jar (persist Set-Cookie across Runs in a session)
- OAuth2 device/code flow (harder; phase 2)
- “Inherit from parent folder”

**Session continuity:** after `POST /login`, subsequent Runs in the same session reuse cookies/token unless cleared. That’s the difference between a toy client and something people actually use for authenticated apps.

---

## 6. The Run pipeline

```text
Resolve env vars
  → Apply auth profile
  → Build URL (path + query)
  → Serialize body
  → Send (fetch/undici/curl)
  → Capture timing + status + headers + body
  → Pretty-print / schema-validate response
  → Optional: assert expected status from OpenAPI/tests
  → Store history entry (gitignored)
```

UI essentials:

- Path/query editors with types
- Body editor with schema hints / “Fill from example”
- Auth picker
- Env switcher
- Response panel (body / headers / timeline)
- **Go to handler** from the request
- **Save response as example**

---

## 7. Sync & drift (where most tools fail)

Regeneration must be **merge**, not wipe.

| Change in code | Behavior |
|----------------|----------|
| New route | Add to collection, mark “new” |
| Removed route | Soft-delete or “orphaned” badge |
| Path/method change | Remap by `operationId` or fuzzy path match; keep user edits to body/headers where possible |
| Schema change | Update example only if user hasn’t customized; else show diff |

User-owned fields (body tweaks, extra headers, notes) should survive regeneration. Machine-owned fields (path, method, schema) update from discovery.

Show a status chip: **In sync** / **Drift** / **Manual only**.

---

## 8. Confidence & UX honesty

Never pretend a guessed Express route is as solid as OpenAPI.

| Confidence | UI |
|------------|----|
| 3 | Full form, Run enabled, schema validation |
| 2 | Prefill body, warn “schema inferred” |
| 1 | Method + path only; empty body scaffold |
| 0 | Candidate list; user confirms before adding |

That keeps trust. Over-confident auto-collections feel broken fast.

---

## 9. Cursor-specific fit

Cursor already has pieces of this puzzle:

| Cursor strength | How it helps |
|-----------------|--------------|
| Workspace index / codebase awareness | Feed discoverers + “find all controllers” |
| Agent | “Generate OpenAPI from this Nest app”, “fix stale collection” |
| Sidebar / panel | Collection browser + Run (like a first-party Thunder Client) |
| `.env` + secrets awareness | Wire auth without pasting tokens into chat |
| Rules / skills | Project convention: “always keep `.api/` in sync” |

A Cursor-native version could look like:

1. **API panel** — tree of endpoints from the graph  
2. **CodeLens** on handlers — `▶ Run` / `Open in API`  
3. **Agent command** — `/api sync` regenerates; `/api run POST /users`  
4. **Chat grounding** — when the agent writes a route, it also offers to add/update the collection entry  

Differentiation vs Postman: **tight loop with the code that defines the API**, not a separate workspace that slowly lies.

---

## 10. Security constraints (non-negotiable)

- Never auto-target prod without explicit env confirmation  
- Mark destructive methods (`DELETE`, `POST` that mutates) with confirm if env ≠ local  
- Secrets only via env refs; redact in history/logs  
- Don’t commit `history/` or captured cookies  
- Sandbox: optional allowlist of hosts (`localhost`, configured baseUrls only)

---

## 11. MVP vs full vision

**MVP (useful in weeks, not years)**

1. OpenAPI import → collection files  
2. Env + bearer/API key  
3. Run + response panel  
4. CodeLens “Run” if OpenAPI `operationId` maps to a symbol (best-effort)  
5. Manual edit of body/headers/query  

**V1**

6. One framework adapter (Nest *or* FastAPI — pick by audience)  
7. Merge/regen with drift UI  
8. Cookie session  
9. Export Postman  

**V2**

10. Runtime capture proxy  
11. Multi-adapter monorepo  
12. Response schema validation + test assertions  
13. Agent: “add endpoint to collection when you scaffold a route”

Don’t start with “any language perfectly.” Start with **OpenAPI + one framework + excellent Run/auth/env**, then widen adapters.

---

## 12. Why this wins product-wise

Manual API clients lose on **time-to-first-successful-authenticated-request**.

Your product wins if a developer can:

1. Open a repo  
2. See endpoints without typing  
3. Pick `local` + auth already wired from `.env`  
4. Hit Run on `POST /users` with a valid body  
5. Jump to the handler when something 500s  

That’s the whole friction reduction: **discovery + context + execution in one surface**.

---

## 13. Design decisions to lock early

1. **Source of truth:** repo files (`.api/`) vs cloud sync — prefer files first  
2. **Exchange format:** OpenAPI internally even if UI is richer  
3. **Adapter API:** stable so community can add Rails/Spring later  
4. **Idempotent regen:** merge strategy before shipping sync  
5. **Host allowlist:** local-first by default  

---

If you want to go one level deeper still, the highest-leverage next slice is either:

- **A)** concrete `.api/` schema + Nest discovery algorithm (step-by-step), or  
- **B)** Cursor UI/IA: panel, CodeLens, agent commands, and what *not* to put in chat  

Say which slice you want.
