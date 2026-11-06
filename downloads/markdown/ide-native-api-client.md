# IDE-Native API Client

An API client connected to (or part of) the IDE that discovers endpoints from the application — regardless of language — builds a runnable collection with body, auth, headers, query, and related request content, and lets the user hit **Run**.

Goal: reduce friction between API clients and developers by making discovery + context + execution one surface, instead of manually typing every endpoint into Postman-style tools.

---

## Concept

Treat the IDE as the source of truth for “what APIs exist,” then make calling them one click.

The product should:

1. **Discover** endpoints from the app (any language/framework)
2. **Build a collection** with method, path, params, body shape, auth, headers
3. **Let the user hit Run** against a live or local server

Friction drop: *code → discover → run*, not *code → copy into Postman → fill everything by hand*.

---

## Core thesis

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

## How discovery can work (language-agnostic enough)

No single parser covers every stack. The practical approach is a **pipeline of sources**, ranked by reliability:

| Source | What you get | Strength |
|--------|--------------|----------|
| **OpenAPI / Swagger / AsyncAPI** | Paths, schemas, auth, examples | Best when present |
| **Framework metadata** | Nest decorators, FastAPI routes, Spring mappings, Rails routes, Express routers | High signal if you know the framework |
| **AST / LSP / symbols** | Route handlers, path strings, DTOs | Works without OpenAPI; harder |
| **Runtime / proxy** | Traffic the app actually serves | Captures real shapes; needs a running app |
| **Tests / client SDKs** | Example payloads and expected status codes | Great for “Run” presets |

“Works for any language” usually means: **plugin per ecosystem + OpenAPI as the universal exchange format**, not one magic scanner.

---

## What the collection must include

For each endpoint, the IDE should prefill:

- **Method + URL** (with path params editable)
- **Query** (typed from schema when possible)
- **Headers** (Content-Type, Accept, custom)
- **Auth** (Bearer, API key, Basic, cookies/session — reusable env profiles)
- **Body** (JSON from schema/examples; form/multipart when needed)
- **Env** (`dev` / `staging` / `local` base URLs, secrets from `.env` / vault — not committed)

Then **Run** = send request, show status, latency, response body, and optionally “save as example.”

---

## Why this reduces friction vs Postman-style clients

- No context switch: stay in the editor next to the handler
- No drift: regenerate when routes change (or warn on mismatch)
- Auth/env shared with the project instead of copy-pasted tokens
- Body comes from types/DTOs/OpenAPI, not from memory

Closest existing pieces: JetBrains HTTP Client, VS Code REST Client / Thunder Client, Postman/Insomnia/Bruno + codegen, FastAPI/Nest Swagger UIs — but none fully own “scan my whole app in the IDE → runnable collection” as a first-class, language-agnostic loop.

---

## Hard parts (worth designing for early)

1. **Ambiguous routes** — dynamic paths, middleware, mounts (`/api/v1`), multi-app monorepos
2. **Auth that’s not in the route** — JWT from cookies, OAuth redirects, signed headers
3. **Incomplete schemas** — “endpoint exists” ≠ “body is correct”
4. **Security** — never auto-run destructive endpoints against prod; scope secrets carefully
5. **Stale collections** — regenerate or diff on save so the collection doesn’t lie

---

## Normalized endpoint model

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

## Discovery layers (ranked)

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

## Collection + env format

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

## Auth model

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

## The Run pipeline

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

## Sync & drift

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

## Confidence & UX honesty

Never pretend a guessed Express route is as solid as OpenAPI.

| Confidence | UI |
|------------|----|
| 3 | Full form, Run enabled, schema validation |
| 2 | Prefill body, warn “schema inferred” |
| 1 | Method + path only; empty body scaffold |
| 0 | Candidate list; user confirms before adding |

That keeps trust. Over-confident auto-collections feel broken fast.

---

## Product shape

```text
[Workspace] → Discoverers (OpenAPI | Nest | FastAPI | Express | …)
                    ↓
              Normalized API Graph
                    ↓
         Collection + Env profiles
                    ↓
              Run / History / Diff
```

User experience:

1. Open project → “Found 47 endpoints”
2. Pick one → body/headers/auth already filled from schema or last run
3. Choose env → **Run**
4. Optionally: “Update from code” when the handler or DTO changes

---

## Cursor-specific fit

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

## Security constraints

- Never auto-target prod without explicit env confirmation
- Mark destructive methods (`DELETE`, `POST` that mutates) with confirm if env ≠ local
- Secrets only via env refs; redact in history/logs
- Don’t commit `history/` or captured cookies
- Sandbox: optional allowlist of hosts (`localhost`, configured baseUrls only)

---

## MVP vs full vision

### MVP (useful in weeks, not years)

1. OpenAPI import → collection files
2. Env + bearer/API key
3. Run + response panel
4. CodeLens “Run” if OpenAPI `operationId` maps to a symbol (best-effort)
5. Manual edit of body/headers/query

### V1

6. One framework adapter (Nest *or* FastAPI — pick by audience)
7. Merge/regen with drift UI
8. Cookie session
9. Export Postman

### V2

10. Runtime capture proxy
11. Multi-adapter monorepo
12. Response schema validation + test assertions
13. Agent: “add endpoint to collection when you scaffold a route”

Don’t start with “any language perfectly.” Start with **OpenAPI + one framework + excellent Run/auth/env**, then widen adapters.

---

## Why this wins product-wise

Manual API clients lose on **time-to-first-successful-authenticated-request**.

This product wins if a developer can:

1. Open a repo
2. See endpoints without typing
3. Pick `local` + auth already wired from `.env`
4. Hit Run on `POST /users` with a valid body
5. Jump to the handler when something 500s

That’s the whole friction reduction: **discovery + context + execution in one surface**.

---

## Design decisions to lock early

1. **Source of truth:** repo files (`.api/`) vs cloud sync — prefer files first
2. **Exchange format:** OpenAPI internally even if UI is richer
3. **Adapter API:** stable so community can add Rails/Spring later
4. **Idempotent regen:** merge strategy before shipping sync
5. **Host allowlist:** local-first by default

---

## Possible next slices

- Concrete `.api/` schema + Nest discovery algorithm (step-by-step)
- Cursor UI/IA: panel, CodeLens, agent commands, and what *not* to put in chat
