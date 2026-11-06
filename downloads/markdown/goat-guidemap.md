# WEB ENGINEERING MASTER CURRICULUM

One document. Strict order. No shortcuts.

Goal: become a fullstack engineer who owns a system in production and can defend every decision, on the way to senior.
Audience: a student with 30+ hours/week who is willing to practice relentlessly.
Replaces: `web-engineering-guidemap.md`, `web-engineering-plan.md`, `web-engineering-mastery-plan.md` (everything in them is folded in here and broken down further).

How to read this file: do not read it front to back. Read Part A once. Read Part B once. Then work through Part C one module at a time, in order. Parts D, E, F are used alongside.

---

## TABLE OF CONTENTS

- Part A. The Contract (rules, schedule, evidence, AI policy)
- Part B. The Master Order (every module, in order, with hours and prerequisites)
- Part C. The Modules
  - Phase 0. Tooling foundation (M0.1 to M0.3)
  - Phase 1. JavaScript runtime (M1.1 to M1.4)
  - Phase 2. HTTP in depth (M2.1 to M2.6)
  - Phase 3. Browser and performance (M3.1 to M3.4)
  - Phase 4. PostgreSQL (M4.1 to M4.5)
  - Phase 5. Authentication and authorization (M5.1 to M5.6)
  - Phase 6. API as a product (M6.1 to M6.4)
  - Phase 7. Client data: cache and mutations (M7.1 to M7.4)
  - Phase 8. Where code runs: CSR, SSR, RSC (M8.1 to M8.8)
  - Phase 9. Production (M9.1 to M9.12)
  - Phase 10. Systems thinking and engineering habits (M10.1 to M10.3)
- Part D. Capstone: `slotlock`
- Part E. Career track (runs in parallel)
- Part F. Appendices (audit, resource shelf, ignore list, quiz bank, templates)

---

# PART A. THE CONTRACT

## A1. The diagnosis (read once, believe it)

1. Knowing the names of frameworks is not a skill. React, Next.js, Hono, Express, Zustand, TanStack Router, Solid are costumes over the same 12 ideas below.
2. Seniors can explain the mechanism, the failure modes, and the tradeoff they would pick on a real team. Juniors can explain what a tool is for.
3. The field feels infinite because you study products (Next, Remix, tRPC) instead of invariants (HTTP, the browser, data, time, failure).
4. AI can type the JSX. AI cannot sit in the incident channel and say "that job is not idempotent." You must be able to, after the autocomplete is wrong.

The 12 invariants every module in this file is secretly about:

1. A client asks a server for bytes over a network.
2. Bytes become a document, then a render tree, then pixels.
3. Work can happen before the browser, after it, or on both.
4. Server data is the source of truth. The client holds a cache of it.
5. Reads and writes have different caching, consistency and retry rules.
6. The network is slow, lossy and hostile. Latency dominates.
7. The server holds the secrets and the database. The browser holds nothing you trust.
8. Authentication is "who are you". Authorization is "what may you do". Apps get both wrong.
9. Cache invalidation is hard because time and identity are hard.
10. Performance means: what is on the critical path, and how many round trips.
11. Production is where your mental model meets reality: logs, queues, races, 2am.
12. Frameworks are opinions about where to put those ideas. They do not replace them.

## A2. Definition of "learned" (the only definition)

A topic is learned when ALL five are true. Four out of five is a fail.

1. EXPLAIN: you can explain the mechanism out loud for the required time, cold, with no notes, no screen, and you do not use the words "it just" or "magic" or a framework name as an explanation.
2. BUILD: you built the smallest working version from an empty file, with no AI-written first draft.
3. BREAK: you broke it on purpose in each way listed in the module, and wrote down the exact error or symptom you observed.
4. WRITE: you wrote 20 to 40 lines in `NOTES.md` in your own words (mechanism, how it broke, what you would do at work).
5. RETAIN: you pass the same quiz again after 1 day, 3 days, 7 days, and 21 days (see A7).

"I read it" is not on this list. "I watched the video" is not on this list. "I followed the tutorial" is not on this list.

## A3. The hard rules

R1. Order is law. Do not start module N+1 until module N's gate is passed. Phase exit tests exist for a reason.
R2. A failed gate means you repeat that module's Drills and Break sections, not the reading. Protocol in A6.
R3. Frozen stack for the whole curriculum (see A4). You may not substitute tools. You may not add a library "as a reward".
R4. First draft is yours. AI policy in A8 is strict.
R5. NOTES.md or it did not happen. No NOTES entry means no gate.
R6. Primary sources first (RFCs, MDN, Postgres docs, specs, the book). Blog posts and videos only where this file names them.
R7. Every claim you write in NOTES.md must be backed by something you observed (a `curl -v` output, an `EXPLAIN` plan, a DevTools screenshot, a log line). Opinions without evidence are not allowed.
R8. Never copy-paste code you do not understand line by line. If you paste it, you must be able to delete it and rewrite it from memory.
R9. Measure before and after. "Feels faster" is banned. Numbers or it did not happen.
R10. You are allowed to be slow. You are not allowed to skip. If you are behind, drop DSA time first, then Saturday review, never the Break drills.

## A4. The frozen stack (12-month freeze, applies to the whole file)

| Role | Choice | Notes |
| --- | --- | --- |
| Language | TypeScript, strict mode | `"strict": true`, no `any` unless commented |
| Runtime | Bun (current repo) or Node 22 | Pick one per repo, never mix inside a repo. Learn Node's `node:http` API either way |
| UI runtime | React 19 | Solid / SolidStart is optional lab work in M8.7 only |
| Server | Hono | No Express, Fastify, Nest, Elysia |
| Database | PostgreSQL 16+ | No Mongo, Firebase, Supabase-as-brain |
| SQL client | `postgres.js` (or `pg`) | No ORM until M6.2 |
| ORM | Drizzle | Read-only peek in M6.2. Banned before that |
| Validation | Zod | At the HTTP boundary |
| Client cache | TanStack Query | Only after you wrote your own baby cache (M7.1) |
| Router | TanStack Router OR React Router 7 | Pick one in M7.2, freeze it |
| Metaframework | Next.js App Router | Rendering lab only (Phase 8) |
| Tests | Node/Bun test runner + Playwright | No Jest-and-20-plugins |
| Logging | Pino | M9.1 |
| Errors | Sentry (or self-hosted equivalent) | M9.2 |
| Deploy | Fly.io or Railway AND one raw VPS once | You must know what the platform hides |
| Editor | Cursor (with AI autocomplete OFF for lab files, see A8) | |

## A5. Banned until Phase 9 is boring

GraphQL, gRPC, Kafka, RabbitMQ, Kubernetes, microservices, serverless as a default, "edge everything", Redis (except where M10.1 uses it conceptually), MongoDB, Firebase, Prisma as an identity, NextAuth / Clerk / Auth0 / Better Auth before M5.5 is passed, JWT in `localStorage` (never), monorepo tooling (Turborepo, Nx), micro-frontends, any framework launched this month, "clean architecture" folder operas, Twitter/X doomscrolling, tutorial playlists.

If you feel the urge to learn any of these, write the urge in `NOTES.md` under `## Urges` and do the next drill instead.

## A6. Gates, failure, and repetition

Every module ends with a GATE. A gate has three parts:

1. SPOKEN: set a timer, record a voice memo (phone), speak with no notes. The memo is saved as `evidence/M<id>-spoken-<date>.m4a`. A spoken answer fails if you stall for more than 10 seconds three times, or use a banned phrase, or skip a required point.
2. WRITTEN: the same answer written under `## M<id> gate` in `NOTES.md`, from memory, in under 60 minutes.
3. QUIZ: 10 cold questions (a bank is in each module). Pass = 9 of 10 correct for core modules, 8 of 10 for the rest. Have an AI or a friend ask them with the prompt in A8, one at a time, no hints.

Plus: the deliverable is committed and tagged `gate/M<id>`.

Fail protocol:

- Attempt 1 fails: wait 24 hours. Redo the Break drills and re-answer. Retry gate.
- Attempt 2 fails: wait 24 hours. Redo the Build drills from an empty file. Retry gate.
- Attempt 3 fails: redo the whole module including reading. In `NOTES.md` write `## M<id> root cause`: which atom did you not understand, and why did your study method not catch it.

Phase exit tests (marked in Part B) are gates over the whole phase. They are cold: no notes, one sitting. If you fail a phase exit, you repeat the specific modules that failed, then retake it. You do not start the next phase.

## A7. Spaced retrieval (this is how it sticks)

Keep a file `recall/questions.md`. After each gate, append the 10 quiz questions with the module id.

Retrieval schedule per module, counted from the gate pass date:

- +1 day: re-answer all 10, out loud, in 10 minutes.
- +3 days: again.
- +7 days: again, plus explain the module's gate in 3 minutes.
- +21 days: again.
- +60 days: again.

Every Saturday block (2.5h) includes a mixed random draw of 20 questions from all previous modules. Any question you miss goes into `recall/missed.md` and is re-asked the next 3 Saturdays.

## A8. AI policy (strict)

The reason this policy exists: if AI writes your first draft, you learn to read answers, not to produce them. Reading a solution gives the feeling of understanding with none of the memory.

Autocomplete: turn AI inline completion OFF for all `labs/*` code. You may keep it on in `slotlock/` only after M6.1, and never for the Build drills in M6 to M9 that say "no AI".

ALLOWED (after you have a working attempt):
- Quiz you cold. Standard prompt: "Ask me 10 questions about <topic>, one at a time. Wait for my answer. After each, tell me exactly where my answer is wrong or imprecise. Do not give hints before I answer. Do not teach until the end."
- Review your code and point out races, authz holes, and missing error handling. You fix them.
- Generate adversarial test inputs: double submit, stale cache, expired cookie, concurrent edit, malformed JSON, huge payload, unicode, negative numbers.
- Explain an RFC paragraph you attempted first.
- Read an `EXPLAIN` plan or flame chart together with you after you read it yourself.

FORBIDDEN:
- Writing the first draft of any Build drill.
- Producing glue code you cannot redraw from memory.
- "Make it work" in a lab. Fix it with the debugger, logs, `curl -v`, `psql`, DevTools.
- Explaining a concept before you have read the primary source and written 5 sentences of your own.
- Writing your NOTES.md, gate answers, or postmortems.

The self-check: close the chat, close the docs, explain it to a rubber duck for 5 minutes. If you stall, you do not know it.

## A9. The 30 hours per week schedule

Total: 30h/week = Monday to Friday 5.5h each (27.5h) + Saturday 2.5h. Sunday is off. Sleep 7 to 8 hours. No exceptions. Burnout is the number one failure mode of this plan, and "relentless" means consistent, not feral.

Daily blocks (Mon to Fri):

| Time | Block | What happens |
| --- | --- | --- |
| Block 1 (2h) | STUDY | Read primary sources in the order given. After each section, close it and write 5 sentences in your own words in `scratch/`. If you cannot, re-read |
| Break (15 min) | walk | no phone |
| Block 2 (2h) | BUILD | Do the Drills in order. Empty file. No AI first draft |
| Break (15 min) | walk | |
| Block 3 (1h) | BREAK | Do the module's Break list. Record exact errors |
| Close (15 min) | NOTES | 20 to 40 lines in `NOTES.md`. Update `LEARNING-LOG.md` (template in F7) |

Saturday (2.5h): spaced retrieval (A7) 45 min, mixed random quiz 30 min, DSA 60 min (from M10.2, after the start threshold), weekly review 15 min (update tracker, set next week's modules).

Weekly cadence of a module (15h budget example): Day 1 Study + start Build. Day 2 Build. Day 3 Build + Break. Day 4 finish Break, NOTES, start gate prep. Day 5 gate. If you pass early, use the leftover for extra drills listed under "Stretch" or the Saturday retrieval. You do not start the next module early.

If you cannot do 30h in a week: do what you can, keep the order, and extend the calendar. Do not compress modules.

## A10. Repository layout

Create one parent directory `~/code/webeng/` (or keep your existing repo as `labs/http-lab`).

```
webeng/
  LEARNING-LOG.md          daily log (template in F7)
  recall/
    questions.md           all gate quiz questions
    missed.md              questions you missed
  evidence/                voice memos, screenshots, HARs, EXPLAIN plans
  labs/
    tiny-http/             M1.3
    http-lab/              M2.x
    perf-lab/              M3.x
    pg-lab/                M4.x
    auth-lab/              M5.x
    client-lab/            M7.1 scratch only (the real UI lives in slotlock/apps/web)
    render-lab/            M8.x
  slotlock/                the capstone, created at M6.1 (public repo, own git history)
  scratch/                 your 5-sentence notes per section (never committed to slotlock)
```

Every lab has its own `NOTES.md` at the root with sections named exactly `## M<id> gate`, `## Mistakes log`, `## Urges`. Slotlock has `NOTES.md` plus `docs/`.

Git rules (also drilled in M0.1): small commits, imperative messages, one concern per commit, tag every gate `gate/M<id>`, never commit secrets, `.env` in `.gitignore` from the first commit.

## A11. NOTES.md entry format (every session)

```
## <date> M<id> <topic>
Mechanism: <what actually happens, in your words, 3 to 8 lines>
How I broke it: <what you did on purpose, what the exact error/symptom was>
Evidence: <paste of curl -v / EXPLAIN / log line / screenshot filename>
At work I would: <the decision, in 1 to 3 lines>
I was wrong about: <what you believed that turned out false>
```

## A12. The module template

Every module in Part C has these sections in this order:

- Header: id, title, hour budget, prerequisites, phase.
- WHY: why this exists and what goes wrong without it.
- ATOMS: the checklist of single mechanisms you must understand. Each atom is one line. You tick it only when you can state it cold.
- RESOURCES: exact reading, in order, with the section to read and what to skip.
- START HERE: the first 30 minutes, concretely.
- DRILLS: numbered exercises with acceptance criteria. Build from an empty file.
- BREAK: the things you destroy on purpose, with what you must observe.
- QUIZ: 10 cold questions.
- GATE: the spoken script requirements, the written proof, and the pass criteria.
- DELIVERABLE and FAILURE MODES: what is committed, and the usual ways people fake this module.

## A13. The two ways this fails (read when you want to quit)

1. You consume. You read, watch, collect, feel productive, and cannot reproduce anything. Fix: obey A2 and A8. The number that matters is how many gates you passed, not how many hours you studied.
2. You jump. You skip the boring layers to reach Next.js or RSC because that is what the internet argues about. You feel stupid because you are trying to understand the roof without the walls. Fix: R1.

The honest timeline: this file prepares you to interview as a strong mid-level fullstack engineer in about 6 months at 30h/week. The title "senior" also requires time in seat: production incidents, teammates, reviews, consequences. That is years, not months. Part E tells you how to collect those consequences early as a student. Do not let anyone sell you "senior in 6 months".

---

# PART B. THE MASTER ORDER

## B1. Dependency map

```mermaid
flowchart TD
  P0["Phase 0: Shell, git, TypeScript, debugging tools"] --> P1["Phase 1: JS runtime"]
  P1 --> P2["Phase 2: HTTP in depth"]
  P2 --> P3["Phase 3: Browser and performance"]
  P2 --> P4["Phase 4: PostgreSQL"]
  P3 --> P5["Phase 5: Auth"]
  P4 --> P5
  P5 --> P6["Phase 6: API as product (slotlock starts)"]
  P6 --> P7["Phase 7: Client data"]
  P7 --> P8["Phase 8: CSR, SSR, RSC"]
  P3 --> P8
  P8 --> P9["Phase 9: Production"]
  P6 --> P9
  P9 --> P10["Phase 10: Systems thinking and habits"]
  DSA["DSA track (parallel, starts after Phase 4)"] -.-> P10
  CAREER["Career track (parallel, starts at Phase 6)"] -.-> P10
```

## B2. Every module in order

Hours are budgets (study + build + break + gate). If you take double, you are not failing; if you take a third, you are skimming.

| Order | Module | Title | Hours | Prerequisite |
| --- | --- | --- | --- | --- |
| 1 | M0.1 | Shell, Linux, git workflow | 8 | none |
| 2 | M0.2 | TypeScript and toolchain | 12 | M0.1 |
| 3 | M0.3 | Debugging toolkit: curl, jq, debugger, psql, DevTools basics | 8 | M0.2 |
| 4 | M1.1 | Values, scope, closures, `this`, modules | 14 | M0.2 |
| 5 | M1.2 | Event loop, promises, async, cancellation | 16 | M1.1 |
| 6 | M1.3 | Serialization and a raw HTTP server (`tiny-http`) | 14 | M1.2 |
| 7 | M1.4 | Bundlers, threads, long tasks (Phase 1 exit) | 10 | M1.3 |
| 8 | M2.1 | URLs, methods, status codes, headers | 14 | M1.4 |
| 9 | M2.2 | Cookies, origin vs site | 12 | M2.1 |
| 10 | M2.3 | CORS | 14 | M2.2 |
| 11 | M2.4 | HTTP caching | 14 | M2.1 |
| 12 | M2.5 | Transport: latency, TCP, TLS, HTTP/1.1, 2, 3 | 12 | M2.4 |
| 13 | M2.6 | Compression, chunked, SSE, WebSockets (Phase 2 exit) | 14 | M2.5 |
| 14 | M3.1 | Critical rendering path | 14 | M2.6 |
| 15 | M3.2 | Core Web Vitals, fonts, images | 14 | M3.1 |
| 16 | M3.3 | DevTools fluency | 8 | M3.2 |
| 17 | M3.4 | Hydration, streaming, JS cost (Phase 3 exit) | 12 | M3.3 |
| 18 | M4.1 | SQL that is real | 16 | M0.3 |
| 19 | M4.2 | Indexes and EXPLAIN | 16 | M4.1 |
| 20 | M4.3 | Transactions, isolation, lost updates | 18 | M4.2 |
| 21 | M4.4 | Pooling, N+1, migrations | 12 | M4.3 |
| 22 | M4.5 | Window functions, CTEs, partitioning, vacuum (Phase 4 exit) | 16 | M4.4 |
| 23 | M5.1 | Passwords and sessions | 14 | M4.5, M2.2 |
| 24 | M5.2 | XSS and CSP | 10 | M5.1 |
| 25 | M5.3 | CSRF and Fetch Metadata | 10 | M5.2 |
| 26 | M5.4 | Authorization and IDOR | 12 | M5.3 |
| 27 | M5.5 | OAuth 2.0 and OIDC | 12 | M5.4 |
| 28 | M5.6 | RBAC, ABAC, ReBAC, RLS, secrets (Phase 5 exit) | 14 | M5.5 |
| 29 | M6.1 | Resources, errors, validation, idempotency, rate limits | 16 | M5.6 |
| 30 | M6.2 | Pagination, filtering, ORM peek | 10 | M6.1 |
| 31 | M6.3 | Uploads, webhooks, background jobs | 16 | M6.2 |
| 32 | M6.4 | Contract, health, first deploy (Phase 6 exit) | 10 | M6.3 |
| 33 | M7.1 | Server state vs client state, baby query cache | 12 | M6.4 |
| 34 | M7.2 | Query lifecycle, SWR, waterfalls, races | 12 | M7.1 |
| 35 | M7.3 | Mutations, optimistic UI, double submit | 14 | M7.2 |
| 36 | M7.4 | Same UI twice (Phase 7 exit) | 12 | M7.3 |
| 37 | M8.1 | Rendering models and the four questions | 8 | M7.4, M3.4 |
| 38 | M8.2 | Measuring CSR vs SSR on the same page | 12 | M8.1 |
| 39 | M8.3 | React Server Components | 16 | M8.2 |
| 40 | M8.4 | Server functions and actions | 12 | M8.3 |
| 41 | M8.5 | Hydration bugs, streaming, Suspense | 10 | M8.4 |
| 42 | M8.6 | Caching models in Next | 12 | M8.5 |
| 43 | M8.7 | Steelman, plus Solid lab | 6 | M8.6 |
| 44 | M8.8 | Phase 8 exit talk | 6 | M8.7 |
| 45 | M9.1 | Structured logs and request identity | 8 | M8.8 |
| 46 | M9.2 | Error tracking, source maps, one business metric | 8 | M9.1 |
| 47 | M9.3 | RED metrics and one trace | 10 | M9.2 |
| 48 | M9.4 | Deploy like an adult (VPS once) | 14 | M9.3 |
| 49 | M9.5 | Cause an incident, write the postmortem | 10 | M9.4 |
| 50 | M9.6 | Tests that are not theater, plus CI | 14 | M9.5 |
| 51 | M9.7 | The performance loop on slotlock | 12 | M9.6 |
| 52 | M9.8 | Load testing | 12 | M9.7 |
| 53 | M9.9 | Security pass: OWASP API Top 10 | 12 | M9.8 |
| 54 | M9.10 | Delete and simplify | 8 | M9.9 |
| 55 | M9.11 | Technical post and mock interviews | 16 | M9.10 |
| 56 | M9.12 | The hireable artifact (Phase 9 exit) | 12 | M9.11 |
| 57 | M10.1 | System design for web engineers | 24 | M9.12 |
| 58 | M10.2 | DSA track (parallel, 1h/day Saturdays and light days) | 60 | M4.5 |
| 59 | M10.3 | Code review, design docs, reading other people's code | 8 | M9.12 |

Total core (excluding the parallel DSA track): about 712 hours. At 28 productive hours per week plus buffer, that is about 26 to 27 calendar weeks.

## B3. Calendar (at 30h/week)

| Calendar weeks | Modules | Output |
| --- | --- | --- |
| 1 to 3 | M0.1 to M1.4 | `tiny-http`, Phase 1 exit passed |
| 4 to 6 | M2.1 to M2.6 | `http-lab`: 304, CORS, cookie, chunked, SSE demos |
| 7 to 8 | M3.1 to M3.4 | `perf-lab`: before/after LCP, HARs |
| 9 to 11 | M4.1 to M4.5 | `pg-lab`: EXPLAIN.md, overlap constraint, window functions |
| 12 to 14 | M5.1 to M5.6 | sessions, OAuth traced, IDOR tests, RLS |
| 15 to 16 | M6.1 to M6.4 | `slotlock` deployed stub (internship applications start) |
| 17 to 18 | M7.1 to M7.4 | baby cache, Query list/detail/edit |
| 19 to 21 | M8.1 to M8.8 | same page three ways, metrics table, recorded talk |
| 22 to 26 | M9.1 to M9.12 | logs, Sentry, postmortem, load test, public post |
| 26 to 27 | M10.1, M10.3 | system design notes, review habits |
| ongoing | M10.2, Part E | DSA, applications, freelance, OSS |

## B4. Phase exit tests (cold, one sitting, no notes)

- Phase 1 exit: draw the event loop; explain why a 40ms render breaks a click; explain why a function cannot cross the server/client boundary; explain what a bundler does with a server secret in a client module.
- Phase 2 exit: narrate a HAR; write cache headers for four resource shapes; tell CORS + cookies + CSRF as one story; choose a transport for two features.
- Phase 3 exit: LCP improved at least 30 percent on a real page with the lever named; explain why SSR can hurt LCP; narrate a waterfall.
- Phase 4 exit: ERD from memory; EXPLAIN before/after on 100k rows; lost update reproduced and fixed three ways; overlap constraint proven; top-N-per-group with a window function.
- Phase 5 exit (paper, 30 minutes): design `POST /orders` that cannot double-charge, a listing that stays correct during inserts, login plus logout-all, an IDOR test plan, and an authz model doc.
- Phase 6 exit: `docs/api.md`, idempotent double POST proven, cursor pagination under concurrent inserts, job-runs-twice answered in code, `/ready` fails when DB is down.
- Phase 7 exit: narrate the full `/bookings` to `/bookings/88`, edit, save twice, back flow with no framework names.
- Phase 8 exit: 10-minute talk, no slides, recorded.
- Phase 9 exit: the hireable artifact checklist in M9.12.

## B5. When you are tempted to skip

The skip table. Each row is a real trap.

| You want to... | The invariant you are missing | Do instead |
| --- | --- | --- |
| Start Next.js | Where HTML is generated and what JS ships | Finish Phase 3 and Phase 7 first |
| Use Prisma | What SQL it emits and why N+1 happens | Finish Phase 4, then M6.2 peek |
| Use Clerk/NextAuth | What a session row and a cookie are | Finish M5.1 to M5.4 |
| "Just use JWT" | Revocation, storage, XSS blast radius | M5.1 and M5.5 |
| Add Redis | Whether Postgres plus an index already solves it | M4.2, M10.1 |
| Add GraphQL | Whether you measured over/under-fetching | Ignore until Phase 9 is boring |
| Add microservices | You do not have a monolith problem | Ignore |
| Add a new UI library | Server state vs client state | M7.1 |

---

# PART C. THE MODULES

Conventions used in every module:

- "Cold" means no notes, no docs, no AI, no screen.
- "Draw" means on paper or a whiteboard, photographed into `evidence/`.
- Resource links are primary sources. If a link has moved, the named document and section are the authority.
- Drill IDs look like `D1`, `D2`. Acceptance criteria are written so a stranger could check them.
- Every drill is built from an empty file unless it says "extend".

---

# PHASE 0. TOOLING FOUNDATION

Without these you will spend your learning budget fighting your tools. This phase is short, but it has gates, because everything later assumes it.

---

## M0.1 Shell, Linux, git workflow

Hours: 8. Prerequisite: none. Phase: 0.

### WHY
Every later module has you running servers, `curl`, `psql`, and diffing outputs. A weak shell turns each drill into a fight. Git is the evidence trail for every gate, and PR habits are what teams hire for.

### ATOMS
Shell
1. Current working directory, absolute vs relative paths, `~`, `.`, `..`.
2. `ls -la`, `cd`, `pwd`, `mkdir -p`, `cp -r`, `mv`, `rm -r` (and why `rm -rf` with a variable can destroy a disk).
3. Standard streams: stdin (0), stdout (1), stderr (2). Redirection `>`, `>>`, `2>`, `2>&1`, `<`.
4. Pipes: `cmd1 | cmd2`. Exit codes: `$?`, `&&`, `||`, `;`. What `set -euo pipefail` does.
5. `grep`/`rg`, `find`/`fd`, `sort`, `uniq -c`, `wc -l`, `head`, `tail -f`, `xargs`, `tee`.
6. Environment variables: `export`, `env`, `printenv`, per-command `VAR=x cmd`. Why secrets must not be in command history.
7. Processes: `ps aux`, `top`/`htop`, `kill`, `kill -9` vs `kill -TERM`, background `&`, `jobs`, `fg`.
8. Ports: `ss -ltnp` (who is listening on 3000), `lsof -i :3000`, `curl` to localhost.
9. File permissions: `rwx`, `chmod +x`, owner vs group vs other.
10. Writing a bash script: shebang, variables, quoting (`"$var"`), functions, `if`, loops, arguments `$1 $@`.

Git
11. The three areas: working tree, index (staging), repository. What `git add` and `git commit` actually do.
12. A commit is a snapshot with a parent pointer; a branch is a movable pointer; `HEAD` is "where you are".
13. `git status`, `git diff`, `git diff --staged`, `git log --oneline --graph`, `git show`.
14. Branching: `git switch -c`, merge (fast-forward vs merge commit), rebase (rewriting history), when never to rebase (shared branches).
15. Undoing: `git restore`, `git reset --soft/--mixed/--hard`, `git revert`, `git reflog` as the safety net.
16. Remotes: `clone`, `fetch` vs `pull`, `push`, upstream tracking, `origin`.
17. Pull request workflow: branch, small commits, push, PR description, review, squash vs merge.
18. `.gitignore`, why `.env` and `node_modules` never get committed, how to remove a secret that was committed (rotate it first, history rewrite second).
19. Commit message quality: imperative subject under 72 chars, body explains why.
20. Tags: `git tag gate/M0.1`.

### RESOURCES (in order)
1. MIT "The Missing Semester": https://missing.csail.mit.edu/ . Lectures: "The Shell", "Shell Tools and Scripting", "Command-line Environment", "Version Control (Git)". Do the exercises at the bottom of each lecture page. Skip: Vim lecture (optional), Metaprogramming, Security and Cryptography (come back in Phase 5).
2. Pro Git book (free): https://git-scm.com/book . Read chapters 1 (Getting Started), 2 (Git Basics, all), 3 (Branching, all), 7.6 (Rewriting History). Skip: 4 (servers), 8 to 10.
3. `man bash`, section "SHELL GRAMMAR" and "PARAMETERS": skim only; use `man` as a dictionary.
4. "Learn Git Branching" (visual, ~3h): https://learngitbranching.js.org/ . Do the Main and Remote levels. Stop there.

### START HERE (first 30 minutes)
1. Open a terminal. Create `~/code/webeng` and `cd` into it. `git init`. Create `.gitignore` with `node_modules`, `.env`, `.DS_Store`, `evidence/*.m4a` (keep binary evidence out of git; commit text evidence).
2. Make your first commit with a real message. Run `git log --oneline`.
3. Run `ss -ltnp` and identify every listening port on your machine and which process owns each.
4. Run `seq 1 10000 | awk '{print $1 % 7}' | sort | uniq -c`. Explain every stage.

### DRILLS
D1. Write `scripts/ports.sh`: given a port number, print the PID and command that owns it, or `free` if nobody does. Acceptance: exits 0 in both cases; handles a missing argument with a usage message and exit code 2.
D2. Write `scripts/loop.sh` that runs a command N times and reports total seconds (use `date +%s%N`). Acceptance: `scripts/loop.sh 5 sleep 1` prints about 5 seconds.
D3. Log analysis: generate a fake access log of 10,000 lines (`method path status ms`) with a script. With one pipeline each, answer: how many 5xx, top 5 paths by count, average ms per path. Acceptance: three one-liners saved in `NOTES.md`.
D4. Git: make a repo with 3 branches. Create a merge conflict on purpose, resolve it by hand, then do the same with `rebase`. Acceptance: you can explain in `NOTES.md` how the history differs between merge and rebase.
D5. Git recovery: commit, `git reset --hard HEAD~1`, then recover the commit with `git reflog`. Then commit a fake secret, push to a local bare repo (`git init --bare`), and write down the steps you would take (rotate first).
D6. Open a PR on GitHub from a branch into `main` in your own repo. Write a PR description with: what, why, how I tested it, risk. Merge with squash.
D7. Install git hooks: a `pre-commit` hook that refuses to commit if the staged diff contains the string `AKIA` or `-----BEGIN PRIVATE KEY-----`. Acceptance: a test commit is blocked.

### BREAK
- Run `rm -rf "$UNSET_VAR/"*` inside a throwaway directory with `set -u` on and off. Record the difference.
- Pipe a command that fails into another command with and without `pipefail`; record the exit codes.
- Force-push over a branch in your own repo and recover using `reflog`.
- Commit a file, add it to `.gitignore` afterward; observe it is still tracked. Fix with `git rm --cached`.

### QUIZ (cold)
1. What is the difference between `>` and `>>`? Between `2>&1` and `&>`?
2. What does `set -euo pipefail` change, line by line?
3. What does `git add` write, and where?
4. A branch is what, physically?
5. When is `rebase` dangerous?
6. Difference between `reset --hard` and `revert`?
7. You pushed an API key. List the steps in order.
8. What does `git fetch` do that `git pull` adds to?
9. How do you find which process listens on port 3000?
10. What does `kill -9` skip that `kill` does not?

### GATE
Spoken (4 minutes): walk through the lifecycle of one change from edit to merged PR, naming the three git areas and what each command moves. Then explain how you would recover a deleted commit.
Written: the same, plus the 3 one-liners from D3.
Pass: quiz 9/10, hooks installed, one merged PR, tag `gate/M0.1`.

### DELIVERABLE AND FAILURE MODES
Deliverable: `webeng` repo with `scripts/`, hook, merged PR, NOTES.
Faking it: copying shell one-liners without being able to explain each stage. Test: remove a stage and predict the output before running.

---

## M0.2 TypeScript and toolchain

Hours: 12. Prerequisite: M0.1. Phase: 0.

### WHY
You write TypeScript for the whole curriculum. The most important lesson here is what TypeScript is not: types erase at compile time, so they validate nothing at runtime. That fact drives validation (Zod), serialization, and the server/client boundary later.

### ATOMS
1. TypeScript is a type checker plus a transpiler. It emits JavaScript. Types do not exist at runtime.
2. `tsc --noEmit` as a type-check only step. A runner (Bun, tsx, esbuild, swc) strips types without checking.
3. `tsconfig.json` essentials: `strict`, `target`, `module`, `moduleResolution`, `noUncheckedIndexedAccess`, `exactOptionalPropertyTypes`, `skipLibCheck`, `types`, `paths`.
4. Primitive types, literal types, unions, intersections.
5. `type` vs `interface`: declaration merging, extends, when each is idiomatic.
6. Narrowing: `typeof`, `in`, `instanceof`, equality, truthiness, discriminated unions, user-defined type guards (`x is T`), assertion functions.
7. `unknown` vs `any`. Why `JSON.parse` returns `any` and why you must immediately validate.
8. `as` casts and `!` non-null assertions are lies you tell the compiler. When they are acceptable (rarely).
9. Generics: type parameters, constraints (`extends`), defaults, inference.
10. Utility types: `Partial`, `Required`, `Readonly`, `Pick`, `Omit`, `Record`, `ReturnType`, `Awaited`, `Parameters`.
11. `keyof`, `typeof` (type position), indexed access types, mapped types, conditional types (basic), `infer` (read only).
12. `readonly`, `as const`, `satisfies`.
13. Function types: overloads, optional/default parameters, `void` vs `undefined` vs `never`.
14. Declaration files: what `@types/*` packages are, `declare module`.
15. Modules: ESM `import`/`export`, `import type`, `type: "module"`, file extensions in imports under ESM.
16. Package management: `package.json`, `dependencies` vs `devDependencies`, lockfiles (`bun.lock`), semver `^` and `~`, why lockfiles are committed.
17. Scripts: `bun run`, `bun test`, `--hot`/`--watch`, environment variables via `.env`.
18. Linting/formatting: one formatter (Biome or Prettier), one linter (Biome or ESLint). Pick Biome for speed. Configure once, never touch again.
19. Error types: `Error`, `cause`, custom error classes, `instanceof` across module boundaries, why `catch (e)` is `unknown`.
20. Runtime validation: Zod `parse` vs `safeParse`, `z.infer`, why the schema is the source of truth and the type is derived.

### RESOURCES (in order)
1. TypeScript Handbook: https://www.typescriptlang.org/docs/handbook/intro.html . Read in this order: The Basics; Everyday Types; Narrowing; More on Functions; Object Types; Creating Types from Types (all subsections); Modules. Skip: Classes (read later), Decorators, Mixins, JSX.
2. TSConfig reference: https://www.typescriptlang.org/tsconfig . Read the entries for each option in atom 3.
3. Total TypeScript free tutorials (beginner and generics sections), or Matt Pocock's "TypeScript Tips" articles at https://www.totaltypescript.com/ . Do the exercises, not just the videos.
4. Zod docs: https://zod.dev/ . Read: basic usage, primitives, objects, arrays, unions, `parse`/`safeParse`, `z.infer`, `refine`, `transform`.
5. Bun docs, "Runtime" section on TypeScript and "Test runner": https://bun.sh/docs . Node docs for comparison: https://nodejs.org/api/test.html (the built-in test runner).
6. Biome getting started: https://biomejs.dev/guides/getting-started/ .

### START HERE (first 30 minutes)
1. `mkdir labs/ts-drills && cd labs/ts-drills && bun init -y`. Set `"strict": true` and `"noUncheckedIndexedAccess": true` in `tsconfig.json`.
2. Write `a.ts` with `const x: number = JSON.parse('"hello"')`. Run `bunx tsc --noEmit`. Run `bun a.ts`. Note: the type-checker is silent and the program runs. Write one sentence in NOTES about what that proves.
3. Install Zod. Replace the line with a `z.number().parse(JSON.parse('"hello"'))`. Note the runtime error.

### DRILLS
D1. Type a function `getUser(id)` that returns `User | undefined`. Call it and make the compiler force you to handle `undefined` without `!`. Acceptance: zero casts and zero `!`.
D2. Discriminated union: model a fetch state `{status:"idle"} | {status:"loading"} | {status:"error", error:string} | {status:"success", data:T}`. Write `render(state)` with an exhaustive `switch` and a `never` check so adding a new status breaks compilation.
D3. Write your own `Pick`, `Omit`, `Partial`, `Record` using mapped types. Acceptance: each passes type-level tests (use `type Expect<T extends true>` assertions).
D4. Generic `groupBy<T, K extends PropertyKey>(items: T[], key: (item: T) => K): Record<K, T[]>`. Acceptance: inferred types for a list of users grouped by role.
D5. Type guard: `isNote(x: unknown): x is Note` hand-written, then replaced by `NoteSchema.safeParse`. Write 8 inputs (including `null`, arrays, extra fields, wrong types); show both produce identical decisions.
D6. Custom error classes: `AppError(code, status, message, details?)` with `cause`. Write `toResponse(err)` that never leaks a stack trace. Acceptance: tests show an unknown error becomes a generic 500 body.
D7. Write 10 tests with the built-in test runner (`bun test` or `node --test`): at least 3 failing-first (red before green).
D8. Set up Biome. Make the formatter run on save and as a pre-commit hook. Acceptance: committing a badly formatted file fails.

### BREAK
- Cast the result of `JSON.parse` to `Note[]` with `as`, feed it a payload with a missing field, and watch the crash happen later and far from the cast. Record the distance (file/line) between cause and symptom.
- Add `// @ts-ignore` to silence a real error; then remove it and fix it properly.
- Make a type that claims `id: number`, receive `"5"` over the wire, and show `===` comparisons silently failing.
- Turn off `strict`, list 5 bugs now allowed through.

### QUIZ (cold)
1. At runtime, what remains of an `interface User`?
2. Why is `JSON.parse` returning `any` dangerous, and what do you do immediately after?
3. `unknown` vs `any`?
4. What does a discriminated union plus `never` buy you?
5. When is `as` acceptable?
6. What does `satisfies` do that `:` and `as` do not?
7. Why is the schema the source of truth and the type derived, not the reverse?
8. What does `noUncheckedIndexedAccess` change?
9. Why commit the lockfile?
10. What does `tsc --noEmit` do, and why can you run code that fails it?

### GATE
Spoken (4 minutes): explain what TypeScript compiles to, what survives to runtime, why validation at the boundary is mandatory, and how a discriminated union prevents a class of bugs.
Pass: quiz 9/10, D1 to D8 committed, Biome hook active, tag `gate/M0.2`.

### DELIVERABLE AND FAILURE MODES
Deliverable: `labs/ts-drills` with tests green and strict config.
Faking it: using `any` or `as` to make red squiggles go away. The test: grep your drills for `any`, `as `, `!` and justify each occurrence in a comment or remove it.

---

## M0.3 Debugging toolkit: curl, jq, debugger, psql, DevTools basics

Hours: 8. Prerequisite: M0.2. Phase: 0.

### WHY
The guidemap's sharpest line: if you cannot use DevTools, `psql`, and logs, you are a prompt operator, not a web engineer. This module installs the instruments. Every later module assumes them.

### ATOMS
curl
1. `curl URL`, `-i` (include headers), `-I` (HEAD), `-v` (verbose: request and response headers, TLS info), `-s` (silent), `-S` (show errors), `-o`, `-O`, `-L` (follow redirects), `-X METHOD`, `-H`, `-d`, `--data-binary`, `--json`, `-F` (multipart), `-b`/`-c` (cookie jar), `-u`, `-w` (format timings), `--compressed`, `--http1.1`/`--http2`, `-k` (and why never in scripts), `--resolve`.
2. Reading `-v` output: `>` lines are request, `<` lines are response, `*` lines are curl/TLS info.
3. `-w '%{http_code} %{time_namelookup} %{time_connect} %{time_appconnect} %{time_starttransfer} %{time_total}\n'`.
4. Why `-d` implies POST and `application/x-www-form-urlencoded`, and how that bites you when sending JSON.

jq
5. `.`, `.field`, `.[]`, `.[0]`, `select()`, `map()`, `length`, `keys`, `to_entries`, `@csv`, `-r` raw output, `-c` compact.

Debugger
6. Breakpoints, step over/into/out, watch expressions, call stack, conditional breakpoints, logpoints.
7. Running your server under the inspector: `bun --inspect` / `node --inspect-brk` and attaching Chrome DevTools or Cursor's debugger.
8. `console.log` vs debugger: when each is right. `console.table`, `console.time`, `console.trace`.
9. Reading a stack trace top to bottom, finding the first line of YOUR code, and source maps basics.

psql
10. Connect: `psql postgres://user:pass@host:5432/db`, `\l`, `\c`, `\dt`, `\d table`, `\x`, `\timing`, `\i file.sql`, `\copy`, `\q`.
11. Running a transaction by hand, and reading error messages with `SQLSTATE` codes.

DevTools basics (full depth comes in M3.3)
12. Elements, Console, Sources, Network, Application panels: what each shows.
13. Network panel basics: preserve log, disable cache, throttling, reading a single request's Headers, Payload, Response, Timing tabs.
14. Copy as cURL, copy as fetch. Replaying a request from the terminal.

### RESOURCES (in order)
1. Everything curl (free book): https://everything.curl.dev/ . Read: "Basics", "Usage" (HTTP, cookies, redirects, uploads, verbose). Skip: libcurl chapters.
2. jq manual: https://jqlang.github.io/jq/manual/ . Read: "Basic filters", "Types and Values", "Builtin operators and functions" (the first 15 functions). Skip: modules, SQL-style operators.
3. Chrome DevTools docs: https://developer.chrome.com/docs/devtools/ . Read: "Network features reference" (first half), "Console overview", "Sources overview", "Debug JavaScript".
4. Node debugging guide: https://nodejs.org/en/learn/getting-started/debugging . Bun debugger: https://bun.sh/docs/runtime/debugger .
5. psql reference: https://www.postgresql.org/docs/current/app-psql.html . Read "Meta-Commands" selectively for atoms 10 and 11.
6. Postgres install, or Docker: `docker run -d --name pg -e POSTGRES_PASSWORD=pw -p 5432:5432 postgres:16`.

### START HERE (first 30 minutes)
1. Start your Hono notes API (from M2.1 later; for now `bun init` a stub server returning JSON).
2. `curl -v localhost:3000/` and label every line as request, response, or info on paper.
3. `curl -s localhost:3000/ | jq .`
4. Start Postgres. `psql ... -c 'select version();'`.

### DRILLS
D1. For a public API of your choice (for example `https://api.github.com/repos/honojs/hono`), use only `curl` and `jq` to answer: number of open issues, license name, a sorted list of the 5 most recent tag names. Acceptance: three commands saved.
D2. `scripts/timing.sh URL` using `curl -w` to print DNS, connect, TLS, TTFB, total in a table. Run it against three sites; explain why TTFB differs.
D3. Run your server under the debugger. Put a breakpoint inside a handler, hit it with `curl`, inspect `Request` headers, step through, and modify a variable at the breakpoint to change the response. Screenshot for `evidence/`.
D4. Make a deliberately wrong query in `psql`; read the error and SQLSTATE; fix it. Turn on `\timing` and `\x`. Create a table, insert, select, update, and look up each command's output.
D5. DevTools: load any page, Network tab: disable cache, record, find the largest response, the slowest request, and a failed request. Use "Copy as cURL" and replay in terminal; compare the headers to the browser's.
D6. Reproduce one bug three ways: with `console.log`, with a conditional breakpoint, and with a logpoint. Write which was fastest and why.

### BREAK
- Send JSON with `curl -d '{"a":1}'` and no `Content-Type`; see what your server receives. Fix with `-H` or `--json`.
- Use `-L` on a POST to a 301 and then a 307; record the method on the second request.
- Set a breakpoint inside an async callback and observe the call stack shape (the "async stack trace").
- Misspell a column in SQL; read the error carefully; note where it points the caret.

### QUIZ (cold)
1. What do `>`, `<`, `*` mean in `curl -v`?
2. What does `curl -d` imply about method and `Content-Type`?
3. Which `-w` variables show DNS, TCP, TLS, TTFB?
4. How do you send a cookie, and save cookies, with curl?
5. What is the difference between step over and step into?
6. What does a logpoint give you that `console.log` does not?
7. How do you list a table's columns and indexes in psql?
8. What does "Preserve log" do in the Network panel?
9. What does `-k` do and why is it banned in scripts?
10. How do you replay a browser request from the terminal?

### GATE
Spoken (3 minutes): demonstrate a debug session live (screen share to yourself): hit an endpoint with curl, break in the handler, inspect, fix, verify with curl.
Pass: quiz 9/10, D1 to D6 done, evidence committed, tag `gate/M0.3`.

### DELIVERABLE AND FAILURE MODES
Deliverable: `labs/ts-drills/scripts/` and `evidence/` screenshots.
Faking it: only using `console.log`. The test: use the debugger in every module from now on at least once.

---

# PHASE 1. JAVASCRIPT RUNTIME

You cannot reason about SSR, streaming, or server actions if `await`, the event loop, modules and serialization are folklore.

---

## M1.1 Values, scope, closures, `this`, modules

Hours: 14. Prerequisite: M0.2. Phase: 1. Repo: `labs/tiny-http` (exercises in `exercises/`).

### WHY
Half of all JS bugs are a wrong mental model of references, closures, `this` or module evaluation order. React hooks, server components and event handlers all depend on these.

### ATOMS
Values and references
1. Seven primitive types: `string`, `number`, `bigint`, `boolean`, `undefined`, `null`, `symbol`. Everything else is an object.
2. Primitives are immutable values compared by value; objects are compared by reference (identity).
3. Variables hold a value or a reference. Assignment copies the value or the reference, never the object.
4. Mutation vs reassignment. `const` freezes the binding, not the object.
5. Shallow copy (`{...o}`, `Object.assign`, `slice`) vs deep copy (`structuredClone`) and what each shares.
6. Equality: `===`, `==` (coercion table essentials), `Object.is`, why `NaN !== NaN`, why `-0` exists.
7. Coercion traps: `+`, template strings, `[] + {}`, `ToPrimitive`, `valueOf`/`toString`, truthy/falsy list.
8. Numbers are IEEE 754 doubles: `0.1 + 0.2`, `Number.MAX_SAFE_INTEGER`, why money is never a float.

Scope and closures
9. Global, function, block scope. `var` (function-scoped, hoisted) vs `let`/`const` (block-scoped, TDZ).
10. Temporal dead zone: reading a `let` before initialization throws.
11. Hoisting: function declarations vs function expressions vs `var`.
12. Closure: a function plus the live bindings from its lexical environment. Closes over bindings, not snapshots.
13. The classic loop bug: `for (var i...)` with callbacks; why `let` fixes it (one binding per iteration).
14. Closures as private state, as factories, and as memory retention (a closure keeps its whole scope alive).

`this`
15. `this` is determined by how a function is called, not where it is written (except arrows).
16. Four rules in priority order: `new`, explicit (`call`/`apply`/`bind`), implicit (method call `obj.fn()`), default (undefined in strict mode, global otherwise).
17. Arrow functions have no own `this`; they capture it lexically.
18. Extracted method loses `this` (`const f = obj.method; f()`). Event handlers and callbacks.
19. Strict mode and modules: why `this` at module top level is `undefined`.

Prototypes
20. Every object has an internal `[[Prototype]]`; property lookup walks the chain. `__proto__` vs `.prototype`.
21. `class` is syntax over prototypes and constructor functions. `extends`, `super`, `static`, `#private` fields.

Modules
22. ESM: `import`/`export`, named vs default, live bindings (importing gives a live view, not a copy).
23. Module evaluation: each module runs once, in dependency order; the cache is keyed by resolved URL/path.
24. Circular imports: what is `undefined` at evaluation time, and why function declarations survive but `const` exports do not.
25. CJS vs ESM: `require`/`module.exports` vs `import`; why `require()` of an ESM file historically fails; `"type": "module"`, `.mjs`/`.cjs`; `import.meta.url`; top-level `await`.
26. Dynamic `import()` returns a promise; used for code splitting and lazy loading.

### RESOURCES (in order)
1. You Don't Know JS Yet, "Get Started" (all chapters): https://github.com/getify/You-Dont-Know-JS/tree/2nd-ed/get-started . Read everything. Write 5 sentences per chapter.
2. YDKJS Yet, "Scope & Closures" (chapters 1 to 7): https://github.com/getify/You-Dont-Know-JS/tree/2nd-ed/scope-closures . Do not skip chapter 5 (TDZ) or 7 (closure). Skip appendices on first pass.
3. MDN: Closures https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Closures ; `this` https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/this ; Equality comparisons and sameness https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Equality_comparisons_and_sameness ; JavaScript modules https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Modules .
4. Node ESM docs: https://nodejs.org/api/esm.html . Read: "Introduction", "Enabling", "Differences between ES modules and CommonJS", and the "Interoperability with CommonJS" part.
5. javascript.info, "Object references and copying" and "Prototypes, inheritance" chapters: https://javascript.info/object-copy and https://javascript.info/prototypes .
6. Optional: ECMAScript spec, section "ToPrimitive", only when a coercion puzzle fails you.

### START HERE (first 30 minutes)
1. `mkdir -p labs/tiny-http/exercises && cd labs/tiny-http`. `bun init` or an empty `package.json` with `"type":"module"`.
2. Without running: predict the output of `const a = {n:1}; const b = a; b.n = 2; console.log(a.n, a === b, {n:2} === {n:2})`. Write the prediction. Run it. If wrong, write why.
3. Read YDKJS Get Started chapter 1.

### DRILLS
D1. `01-copy.ts`: two variables pointing to the same nested object, mutate through one, predict and observe. Add shallow copy and `structuredClone` variants; show what each shares. Acceptance: a comment table "operation, shares nested objects? yes/no, verified by test".
D2. `02-closure.ts`: loop with `var`, `let`, and an IIFE, each pushing callbacks; predict output first. Then write a `makeCounter`, a once-only function `once(fn)`, and a `memoize(fn)` using closures. Acceptance: tests for each.
D3. `03-this.ts`: define `const obj = { name:'x', hi() { return this.name } }`. Show: method call, extracted call, `.bind`, `.call`, arrow inside, arrow as property, `setTimeout(obj.hi)`, class method passed as callback. Log `this` in each, in module and non-module context. Acceptance: a 9-row table of predicted vs observed.
D4. `04-module/a.ts` and `b.ts`: circular import. Make one case where it works (function declarations) and one where it throws a TDZ error (`const` arrow). Explain evaluation order step by step in a diagram.
D5. `05-deepcopy.ts`: write `deepCopy` that handles primitives, arrays, plain objects, cycles (via a `WeakMap`), and throws a clear error on functions, symbols, DOM nodes. Document that `Date`, `Map`, `Set` need explicit handling; implement `Date` and `Map` and `Set`. Acceptance: 12 tests including cycles.
D6. `06-equality.ts`: tests that pin `NaN`, `-0`, `Object.is`, `[] == false`, `null == undefined`, `"1" == 1`, `"b" + "a" + +"a" + "a"`. For each, a comment naming the coercion rule.
D7. `07-cjs-esm/`: create a CJS file and an ESM file; require ESM from CJS (see the failure), then import CJS from ESM (see the default export shape). Document both errors verbatim.

### BREAK
- Mutate a `const` object field and reassign a `const`; record both outcomes.
- Pass `obj.method` as a React/Solid-style `onClick` without bind; show `this` is `undefined`.
- Create a memory leak with a closure that retains a big array; show it in the heap snapshot (DevTools Memory tab or `process.memoryUsage()`).
- Add a circular import and observe `undefined` in a `console.log` at module top level.
- Use `==` between `0`, `""`, `"0"`, `false`, `null`, `undefined` in a 6x6 grid; write the table of results.

### QUIZ (cold)
1. `const user = {n:1}; const u2 = user; u2.n = 2;` Draw binding vs value. What is `user.n`?
2. Why does the `var` loop print the same number and the `let` loop does not?
3. What exactly does `const` prevent?
4. Four `this` rules in priority order?
5. Why does `this` inside an arrow in a class method differ from a plain function?
6. When module A imports B which imports A, what is already initialized?
7. What is `Object.is(-0, 0)` and why does `-0` exist?
8. Why can `0.1 + 0.2 !== 0.3`, and what do you do for money?
9. What is a live binding?
10. Why can `require()` of an ES module fail?

### GATE
Spoken (5 minutes): explain bindings vs values, closures (with the loop example), the `this` rules with an extracted-method example, and the circular-import evaluation order.
Pass: quiz 9/10, D1 to D7 committed, tag `gate/M1.1`.

### DELIVERABLE AND FAILURE MODES
Deliverable: `labs/tiny-http/exercises/01..07`.
Faking it: running predictions before writing them down. The test: the prediction must be in the file before the run. If you were wrong, the "why" comment is the learning.

---

## M1.2 Event loop, promises, async, cancellation

Hours: 16. Prerequisite: M1.1. Phase: 1.

### WHY
Waterfalls, jank, double-fire bugs, unhandled rejections, and "why does my server freeze" are all event-loop facts. INP in the browser and request latency on the server are the same mechanism.

### ATOMS
Loop model
1. The call stack, the heap, and queues. JavaScript runs one stack at a time on one thread.
2. Macrotask (task) queue: `setTimeout`, `setInterval`, I/O callbacks, UI events, `MessageChannel`, `setImmediate` (Node).
3. Microtask queue: promise reactions (`.then`/`.catch`/`.finally`), `queueMicrotask`, `MutationObserver`, and `await` continuations.
4. Loop rule: run one macrotask, then drain the entire microtask queue, then (in the browser) possibly render, then the next macrotask.
5. Starvation: an infinite microtask chain blocks rendering and timers forever.
6. `setTimeout(fn, 0)` is "at least 0 ms and after all microtasks", with a 1 ms/4 ms clamp in browsers. Timers are minimums, not guarantees.
7. Browser rendering opportunities: `requestAnimationFrame` runs before paint; `requestIdleCallback` after.
8. Node phases: timers, pending callbacks, poll, check (`setImmediate`), close; `process.nextTick` runs before promise microtasks.
9. libuv threadpool (fs, dns.lookup, crypto, zlib) vs the JS thread; network I/O uses the OS (epoll), not the threadpool.

Promises
10. A promise is a state machine: pending, fulfilled, rejected (settled is final). `then` always schedules a microtask, even if already settled.
11. Chaining: each `.then` returns a new promise; returning a value vs a promise vs throwing; forgetting `return` yields `undefined`.
12. `catch` vs `then(null, fn)`; `finally` passes through; error propagation down the chain.
13. Combinators: `Promise.all` (fail-fast, preserves order), `allSettled`, `race`, `any` (AggregateError).
14. Unhandled rejections: Node's default (`--unhandled-rejections`, process crash in modern Node), browser `unhandledrejection` event.
15. Promise constructor executor runs synchronously; `new Promise(resolve => resolve(x))`; the promise-resolution procedure unwraps thenables.

async/await
16. `async` functions return promises; `await` is promise `then` with the rest of the function as the callback (microtask).
17. Code before the first `await` runs synchronously; code after runs in a microtask.
18. `await` in a `for` loop serializes (a waterfall of latency). `Promise.all` starts all operations first; it is concurrency, not parallel CPU.
19. `await` inside `.map(async ...)` returns an array of promises.
20. Errors: `try/catch` around `await`, `await` in `finally`, forgetting `await` on a rejecting call.
21. Top-level `await` in ESM and how it blocks module evaluation.

Cancellation and time
22. Promises cannot be canceled. `AbortController`/`AbortSignal` cancels the underlying operation (`fetch`, streams, timers via `AbortSignal.timeout`).
23. Timeouts: `Promise.race` with a timer vs `AbortSignal.timeout(ms)`; the leaked-timer problem.
24. Backpressure and concurrency limits: why unlimited `Promise.all` over 10,000 items is a bug.
25. Blocking the loop: CPU loops, `JSON.parse` of a huge string, sync `fs`, regex catastrophic backtracking.

### RESOURCES (in order)
1. javascript.info "Event loop: microtasks and macrotasks": https://javascript.info/event-loop . Then "Promises, async/await" chapters in order: https://javascript.info/promise-basics through https://javascript.info/async-await (6 chapters).
2. Jake Archibald, "In The Loop" (JSConf.Asia 2018): https://www.youtube.com/watch?v=cCOL7MC4Pl0 . Watch once with a notebook, write timestamps of each demo. Then do his demos from memory.
3. Jake Archibald, "Tasks, microtasks, queues and schedules": https://jakearchibald.com/2015/tasks-microtasks-queues-and-schedules/ (the runnable demos).
4. Node docs, "The Node.js Event Loop": https://nodejs.org/en/learn/asynchronous-work/event-loop-timers-and-nexttick .
5. Node docs, "Don't Block the Event Loop": https://nodejs.org/en/learn/asynchronous-work/dont-block-the-event-loop .
6. MDN: AbortController https://developer.mozilla.org/en-US/docs/Web/API/AbortController ; AbortSignal.timeout https://developer.mozilla.org/en-US/docs/Web/API/AbortSignal/timeout_static .
7. MDN: `Promise` combinators https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise . Read the four static methods.
8. Optional: ECMAScript spec "Jobs and Host Operations to Enqueue Jobs", to see microtasks formally.

### START HERE (first 30 minutes)
1. Write the 12-line script: `console.log('A'); setTimeout(()=>console.log('B'),0); Promise.resolve().then(()=>console.log('C')); queueMicrotask(()=>console.log('D')); (async()=>{console.log('E'); await null; console.log('F')})(); console.log('G')`.
2. Predict the order on paper before running. Run it. For each mismatch, trace it.

### DRILLS
D1. `ordering.ts`: five scripts of increasing difficulty mixing `setTimeout`, promises, `async/await`, `queueMicrotask`, `process.nextTick`, `setImmediate`. Prediction before each run, recorded in a comment. Acceptance: 100 percent correct on the last two on a cold re-attempt the next day.
D2. `myAll(promises)`: matches `Promise.all` including empty array, non-promise values, reject on first failure, preserved order, and does not leave unhandled rejections for the losers. 10 tests.
D3. `myRace`, `myAllSettled`, `myAny`. Tests against the native versions with a table of edge cases.
D4. `pLimit(n)`: at most `n` async functions run at once, others queued, results returned in order, errors surface. Prove with a test that tracks the peak concurrency and asserts it never exceeds `n`.
D5. `retry(fn, {tries, baseMs, jitter, signal})` with exponential backoff and jitter, honoring `AbortSignal`. Tests with fake timers.
D6. `timeout(promise, ms)` that does not leak a timer when the promise wins. Prove via a counter of active timers.
D7. Waterfall demo: fetch 3 URLs (use a local server that sleeps 300ms) with `await` in a loop, with `Promise.all`, and with `pLimit(2)`. Log total times. Acceptance: numbers in NOTES with explanation of each.
D8. Blocking demo (done in `tiny-http` M1.3, but write the isolated version now): a function that busy-loops 200ms vs one that awaits `setTimeout(200)`; run both while a 10ms `setInterval` logs drift. Acceptance: the logged drift for the busy loop is about 200ms.
D9. Cancellation: `fetch` a slow endpoint with an `AbortController`, abort after 100ms, show the server also notices the closed connection (`req.signal` or `close` event).

### BREAK
- `await` inside `map` without `Promise.all`; record the printed value.
- A `.then` that forgets `return`; record the downstream value.
- A rejected promise nobody awaits; record Node's crash message and exit code.
- An infinite microtask loop (`function f(){ queueMicrotask(f) }`); show timers never fire.
- Throw inside an `async` function called without `await` in a handler.
- Run 10,000 `fetch`es via unbounded `Promise.all` against the local server and watch resource errors (or socket exhaustion); then fix with `pLimit`.

### QUIZ (cold)
1. Order of: `setTimeout(0)`, `Promise.then`, `queueMicrotask`, code after `await`, `process.nextTick`?
2. Why does `await` in a loop create a waterfall of latency but not of CPU?
3. What does `Promise.all` do when one input rejects, and what happens to the others?
4. Can you cancel a promise? What can you cancel?
5. What runs synchronously in an `async` function?
6. How can a microtask chain starve the page?
7. What is the libuv threadpool used for, and what is not?
8. Why are timers "at least N ms"?
9. How do you add a timeout without leaking a timer?
10. How does a 40ms synchronous task in a click handler affect INP?

### GATE
Spoken (5 minutes): draw the event loop with stack, macrotask queue, microtask queue; place `setTimeout(fn,0)` and `Promise.resolve().then(fn)`; explain why a 40ms render breaks a click (name "long task" and "same thread as UI"); explain waterfall vs `Promise.all`.
Pass: quiz 9/10, `myAll`, `myRace`, `pLimit` tests green, ordering cold re-attempt 100 percent, tag `gate/M1.2`.

### DELIVERABLE AND FAILURE MODES
Deliverable: `labs/tiny-http/exercises/async/` with tests.
Faking it: memorizing the ordering of one example. The test: invent a new ordering puzzle and have the AI check your prediction after you commit it.

---

## M1.3 Serialization and a raw HTTP server (`tiny-http`)

Hours: 14. Prerequisite: M1.2. Phase: 1. Repo: `labs/tiny-http`.

### WHY
HTTP is bytes on a TCP stream that you parse. A server is a loop that reads bytes and writes bytes. If you have never written one, every framework is magic. Serialization is why server components cannot pass functions.

### ATOMS
Serialization
1. JSON's whole type system: object, array, string, number, `true`, `false`, `null`. That is all.
2. What JSON cannot hold: `undefined`, `Date` (becomes a string), `Map`/`Set` (become `{}`), `BigInt` (throws), functions, symbols, `NaN`/`Infinity` (become `null`), class instances (lose their prototype), cyclic references (throws).
3. `JSON.stringify` rules: skips `undefined`/function/symbol in objects, converts them to `null` in arrays, calls `toJSON`, replacer and indent arguments.
4. `JSON.parse` reviver; why `Date` round trips as a string; numbers beyond 2^53.
5. `structuredClone` / structured clone algorithm: supports `Date`, `Map`, `Set`, `RegExp`, typed arrays, cycles; still not functions or class prototypes.
6. Hydration implication: server rendered a `Date` value; the client receives a string. Rendering with locale or timezone differences causes mismatch.
7. TypeScript types erase; an HTTP body is `unknown` until validated.

HTTP/1.1 on the wire
8. TCP is an ordered byte stream, no message boundaries. HTTP defines them.
9. Request message: request line (`METHOD target HTTP/1.1`), header fields (`Name: value`), blank line (CRLF CRLF), optional body.
10. Response message: status line (`HTTP/1.1 200 OK`), headers, blank line, body.
11. Body framing: `Content-Length` (exact bytes) or `Transfer-Encoding: chunked`. A request with neither has no body.
12. `Host` header is mandatory in HTTP/1.1 (virtual hosting).
13. Connection management: keep-alive default in 1.1, `Connection: close`, pipelining (obsolete).
14. Case rules: header names case-insensitive; methods case-sensitive.
15. Content negotiation basics: `Content-Type` of the body, `Accept` of the client.
16. Status line plus `Content-Type` plus body is the contract.

Server behavior
17. One process, many concurrent requests; shared module state is shared between requests; a handler must not assume it is alone.
18. Streams: `IncomingMessage` is a readable stream; the body arrives in chunks; you must handle `data`/`end`/errors; limit size.
19. Backpressure: `res.write` returns false when the buffer is full; `drain` event.
20. Timeouts: `server.requestTimeout`, `headersTimeout`, `keepAliveTimeout`; slow-client (Slowloris) basics.
21. Graceful shutdown: stop accepting, finish in-flight, close idle keep-alives, exit on a deadline.
22. Status and error handling: a handler that throws must still produce a response; one bad request must not crash the process.

### RESOURCES (in order)
1. MDN JSON: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/JSON and `JSON.stringify` page, "Description" section for the rules.
2. MDN `structuredClone`: https://developer.mozilla.org/en-US/docs/Web/API/Window/structuredClone and "Structured clone algorithm": https://developer.mozilla.org/en-US/docs/Web/API/Web_Workers_API/Structured_clone_algorithm .
3. MDN HTTP guides: Overview https://developer.mozilla.org/en-US/docs/Web/HTTP/Guides/Overview ; Messages https://developer.mozilla.org/en-US/docs/Web/HTTP/Guides/Messages .
4. RFC 9112 (HTTP/1.1): https://www.rfc-editor.org/rfc/rfc9112.html . Read: section 2 (Message), 3 (Request Line), 4 (Status Line), 5 (Field Syntax), 6 (Message Body), 9 (Connection Management). Skip: 7 chunked details on the first pass (return in M2.6).
5. Node `http` docs: https://nodejs.org/api/http.html . Read: `http.createServer`, `IncomingMessage`, `ServerResponse`, `server.*Timeout`. And `net` for the TCP drill: https://nodejs.org/api/net.html .
6. Node streams basics: https://nodejs.org/api/stream.html (the "Readable" and "Writable" overview only).
7. Bun `Bun.serve` docs for comparison: https://bun.sh/docs/api/http .

### START HERE (first 30 minutes)
1. In `labs/tiny-http/serialize.ts` print `JSON.stringify` of an object holding `Date`, `undefined`, `Map`, `NaN`, a function. Record each result.
2. Run `curl -v http://example.com/` and label the request and response messages byte by byte, including CRLF.
3. Open `node` REPL and run `require('net').createServer(s=>s.end('hi')).listen(4000)`; `curl -v localhost:4000`; see curl's error. Why? (Missing status line.)

### DRILLS
D1. `serialize.ts`: round-trip `Date`, `undefined`, `Map`, `Set`, a function, a circular object, `BigInt`, `NaN` through `JSON` and through `structuredClone`. Write the observed behavior as a table in comments.
D2. Take one real React or Solid component you wrote earlier. List every prop and value that cannot cross a JSON boundary. For each, say how you would serialize it. Acceptance: a list of at least 6 items.
D3. Raw TCP HTTP server in `raw-tcp.mjs` using only `node:net`: accept a connection, read bytes until `\r\n\r\n`, parse the request line and headers by hand, respond with a valid `HTTP/1.1 200 OK` with `Content-Length`. Acceptance: `curl -v` works, and so does a browser. About 40 lines.
D4. `server.mjs` using only `node:http`, at most 80 lines, no framework:
   - `GET /` returns HTML with `Content-Type: text/html; charset=utf-8`
   - `GET /api/time` returns JSON `{ "now": "<ISO string>" }`
   - `POST /api/echo` reads the body (with a 64KB limit and a 413 on overflow), parses JSON, returns it; 400 on invalid JSON; 415 on a non-JSON content type
   - 404 JSON for unknown paths, 405 with `Allow` for wrong methods
   - logs `method pathname status duration_ms` for every request
   - handles two overlapping requests (prove with two `curl` calls against a `GET /slow` that sleeps 2s)
   Acceptance: a script `scripts/concurrent.sh` shows both finish in about 2s total, not 4s.
D5. `GET /block` runs a 200ms busy loop. Show `/api/time` waits behind it (use `curl -w` timings). Replace the loop with `await setTimeout(200)` and show the wait disappears. Save both timing outputs in NOTES.
D6. Robustness: a handler that throws must return a 500 JSON without crashing the process and without leaking a stack. A request with an invalid `Content-Length` returns 400.
D7. Graceful shutdown on `SIGTERM`: stop accepting new connections, finish in-flight requests, exit within 10 seconds. Acceptance: start a `/slow` request, send SIGTERM, observe the request completing, then the process exiting.
D8. Compare: re-implement D4 in Hono in 20 lines. Write in NOTES the three things Hono did for you that you had to write by hand.

### BREAK
- Send `undefined` and `Date` in a JSON response; record exactly what the client receives.
- POST with a wrong `Content-Type` and with a missing `Content-Length`; record what your server parses.
- Send a 1MB body to your 64KB-limited endpoint; confirm 413 and that memory stays flat.
- Send a malformed request line (`printf 'GET\r\n\r\n' | nc localhost 3000`); see how Node reacts.
- Open 50 slow connections (a loop of `nc` sessions that send nothing) and observe the timeouts you configured.
- Kill the server mid-response and watch the client error.

### QUIZ (cold)
1. Recite JSON's types. What can't it represent?
2. What is a `Date` after `JSON.parse(JSON.stringify(...))`?
3. What does `structuredClone` support that JSON doesn't, and what does it still reject?
4. How does HTTP/1.1 know where the body ends?
5. What are the three parts of an HTTP request message and what separates them?
6. Why can two requests interfere through module-level variables?
7. Why does `/block` stall `/api/time`?
8. What does `res.write` returning `false` mean?
9. Why does the server need a body-size limit?
10. Why can't you pass a function from a Server Component to a Client Component unless the framework makes a server function reference?

### GATE
Spoken (5 minutes): trace an HTTP request from bytes on the wire to a parsed `IncomingMessage`; explain the `/block` demo; explain why JSON cannot carry a function and what a "server function by reference" actually is (an RPC endpoint with an ID).
Pass: quiz 9/10, `server.mjs` at or under 80 lines with all behaviors, concurrency script, `/block` evidence in NOTES, tag `gate/M1.3`.

### DELIVERABLE AND FAILURE MODES
Deliverable: `labs/tiny-http/{raw-tcp.mjs,server.mjs,serialize.ts,scripts/}`.
Faking it: writing the server with Hono or with AI. If any line is unexplainable, delete the server and write it again tomorrow.

---

## M1.4 Bundlers, threads, long tasks (Phase 1 exit)

Hours: 10. Prerequisite: M1.3. Phase: 1.

### WHY
Why does a server secret end up in the browser? Why does a click feel broken? Why does hashing a password freeze your server? The answers live in bundlers and the single-threaded model.

### ATOMS
1. Module graph (files you wrote and their imports) vs bundle graph (what the browser gets after bundling and splitting).
2. A bundler does: resolve imports, build a graph, transpile (TS, JSX), tree-shake (remove unused exports; only works for ESM and side-effect-free modules), split chunks, minify, hash filenames, emit source maps.
3. `sideEffects` in `package.json` and why tree-shaking can fail.
4. Dev server vs production build differences (HMR, unbundled ESM in dev with Vite, bundled in prod).
5. Environment variable leakage: Vite exposes `VITE_*` to the client; `NEXT_PUBLIC_*` in Next; anything else is replaced at build time only if referenced. A secret with a public prefix is public.
6. Server-only code in a client bundle is a security bug, not a DX warning. `import 'server-only'` and the `exports` conditions (`browser`, `node`, `react-server`) in package.json.
7. Source maps: map minified positions to source; why production errors need them and why serving them publicly leaks source.
8. Code splitting: dynamic `import()`, route-level chunks, the cost of the waterfall it can create.
9. The browser's JS thread is also the UI thread (style, layout, paint, input all queue behind it). A task over 50ms is a "long task".
10. INP in plain terms: delay from input to the next paint, composed of input delay, processing time, presentation delay.
11. `requestAnimationFrame`, `requestIdleCallback`, `scheduler.yield()`/`setTimeout` chunking for breaking long tasks.
12. Web Workers: separate thread with its own event loop; communicate by `postMessage` with structured clone; no DOM access.
13. Node/Bun: `worker_threads`; CPU-bound work (hashing, image resize, big JSON) should be offloaded; I/O-bound work should not.
14. Threadpool vs worker: `crypto.pbkdf2`/`scrypt` async variants use the libuv threadpool; sync variants block.
15. Memory: heap snapshots, leaks (closures, listeners, caches without eviction), `process.memoryUsage()`.
16. Profile before optimizing: flame chart, Bottom-Up, self time vs total time.

### RESOURCES (in order)
1. web.dev, "Optimize long tasks": https://web.dev/articles/optimize-long-tasks .
2. web.dev, "Optimize Interaction to Next Paint": https://web.dev/articles/optimize-inp (read the "Long tasks" and "Input delay" parts now, finish in M3.2).
3. Addy Osmani, "The Cost of JavaScript" (search for the latest version on his blog and the Chrome Dev Summit talk): https://v8.dev/blog/cost-of-javascript-2019 .
4. Chrome DevTools, Performance panel reference: https://developer.chrome.com/docs/devtools/performance/reference .
5. Vite docs: "Features", "Env Variables and Modes", "Building for Production": https://vite.dev/guide/ . Read only these three plus "Server-Side Rendering" intro.
6. esbuild or Rollup "Tree shaking" docs: https://rollupjs.org/introduction/#tree-shaking .
7. Node `worker_threads`: https://nodejs.org/api/worker_threads.html ; MDN Web Workers: https://developer.mozilla.org/en-US/docs/Web/API/Web_Workers_API/Using_web_workers .
8. Chrome DevTools "Memory" panel basics: https://developer.chrome.com/docs/devtools/memory-problems .

### START HERE (first 30 minutes)
1. `bun create vite throwaway --template react-ts` (or `npm create vite@latest`). Do not install anything else.
2. Add `src/secret.ts` exporting `export const API_KEY = "sk_live_FAKE_12345"`. Import it in `App.tsx`. Run `vite build` and `grep -r sk_live_FAKE dist/`.
3. Record that it is in the bundle.

### DRILLS
D1. Leak and fix: (a) the `secret.ts` import in the client; confirm in `dist/`; (b) move the secret behind a Hono `/api/data` route that uses it server-side; confirm it no longer appears in `dist/`. Add a `VITE_SECRET` variant and confirm it also leaks by design.
D2. Bundle analysis: `bunx vite-bundle-visualizer` or `rollup-plugin-visualizer`; identify the 3 largest modules in a React app; write what you would do about each.
D3. Tree-shaking: a library module with 5 exports where the app imports 1. Show the build includes 1; then add a side effect at the top and show it all gets included. Explain.
D4. Performance trace: build a page with two buttons. Button A runs a 5ms handler; button B sorts a 200,000-element array synchronously. Record Performance for each click. Name the long task and screenshot the flame chart. Acceptance: you can point at the "Task" bar over 50ms and the function responsible.
D5. Fix button B in two ways: chunk the work with `scheduler.yield`/`setTimeout`, then move it to a Web Worker. Measure INP-like delay for both (the "Event Timing" or "Interactions" track).
D6. Server worker: in `tiny-http`, add `GET /hash?pw=...` that does 100,000 PBKDF2 iterations synchronously; show it blocks `/api/time`. Switch to async `crypto.pbkdf2`; show it no longer blocks. Then do it in a `worker_threads` worker and compare. Write down when each is the right tool.
D7. Memory leak lab: an endpoint that pushes request bodies into a module-level array. Hammer with 1,000 requests; record `process.memoryUsage().heapUsed` growth. Fix with a bounded LRU.
D8. Source maps: build with and without source maps; throw an error in production code; compare the stack traces in the browser console.

### BREAK
- Make a client file import `node:fs` and read the bundler's error.
- Make two modules import each other through a barrel file and observe tree-shaking fail.
- Register a 20ms `setInterval` and block the loop for 300ms; compute the drift from timestamps.
- Add an event listener in a loop without removing it; see the leak in the heap snapshot.

### QUIZ (cold)
1. What does a bundler do, in order?
2. Why is a server secret in a client module a security bug?
3. What does `VITE_` or `NEXT_PUBLIC_` do?
4. Why can tree-shaking fail?
5. What is a long task and why does it hurt INP?
6. Name two ways to break up a long task.
7. What can a Web Worker not do?
8. When do you use `worker_threads` vs async I/O?
9. Why do production errors need source maps, and what's the risk of serving them?
10. How do you tell I/O-bound from CPU-bound work in a flame chart?

### PHASE 1 EXIT TEST (cold, one sitting, 20 minutes, spoken then written)
1. Draw the event loop.
2. Explain why a 40ms render can make a click feel broken.
3. Explain why a function cannot cross the server/client boundary and what a server action actually is.
4. Explain what your bundler would do if you imported `fs` or a secret into a client module.
5. Explain the difference between module graph and bundle graph.
If any answer contains "in Next you just...", you fail the exit and repeat M1.2 to M1.4.

### GATE
Pass: quiz 9/10, D1 to D8 done, Phase 1 exit test passed, tag `gate/M1.4` and `gate/phase1`.

### DELIVERABLE AND FAILURE MODES
Deliverable: `labs/tiny-http` complete: `server.mjs`, worker demo, leak demo, `NOTES.md` with the event loop drawing photo and the serialization table.
Faking it: skipping the profiler drills and trusting intuition. Rule: no optimization claim without a trace screenshot in `evidence/`.


---

# PHASE 2. HTTP IN DEPTH

Repo: `labs/http-lab` (your existing Hono notes API is the start; move it here). Hono is allowed. UI is banned until the end of Phase 2 except tiny static HTML test pages. `curl` and a `.http` file only, plus the browser for cookie/CORS drills that only a browser can do.

---

## M2.1 URLs, methods, status codes, headers

Hours: 14. Prerequisite: M1.4. Phase: 2.

### WHY
HTTP is the contract of the web. Most API bugs (retry duplicates, caches serving the wrong thing, wrong status leading clients to retry forever) are semantics bugs, not code bugs.

### ATOMS
URLs
1. URL anatomy: scheme, userinfo, host, port, path, query, fragment. Default ports (80, 443).
2. The fragment is never sent to the server.
3. Percent-encoding: what must be encoded, `encodeURIComponent` vs `encodeURI`, `+` vs `%20` in queries, the `URL` and `URLSearchParams` classes.
4. Path normalization, trailing slash as a different resource, case sensitivity of paths vs hosts.
5. Origin = scheme + host + port. Same-origin means all three equal. (Same-site comes in M2.2.)

Methods (RFC 9110 section 9)
6. Safe (read-only intent): GET, HEAD, OPTIONS, TRACE. Crawlers, prefetchers and proxies may call these freely.
7. Idempotent (N identical requests have the effect of one): GET, HEAD, OPTIONS, PUT, DELETE (and TRACE). POST and PATCH are not idempotent by default.
8. Why GET must never mutate: prefetch, crawlers, link previews, proxies, retries.
9. POST semantics: process this representation; typically create. PUT: replace the target with this representation. PATCH: partial update (RFC 5789). DELETE: remove the target.
10. HEAD: GET without body, same headers (including `Content-Length`).
11. OPTIONS: capabilities and CORS preflight (M2.3). `Allow` header.
12. Why retries are legal for PUT/DELETE and dangerous for naive POST; the setup for idempotency keys (M6.1).
13. Request bodies on GET/DELETE: legal but unreliable. Don't.

Status codes (RFC 9110 section 15)
14. Classes: 1xx informational, 2xx success, 3xx redirection, 4xx client error, 5xx server error.
15. 200 OK vs 201 Created (with `Location`) vs 202 Accepted (async) vs 204 No Content (success, no body).
16. Redirects: 301 vs 308 (permanent), 302 vs 307 (temporary), 303 See Other (POST then GET). 307 and 308 preserve method and body; 301/302 historically allowed rewriting POST to GET.
17. 304 Not Modified (M2.4). 3xx and `Location`.
18. 400 malformed request vs 401 unauthenticated (must send `WWW-Authenticate`) vs 403 authenticated but forbidden vs 404 not found (also used to hide existence) vs 405 Method Not Allowed (must send `Allow`) vs 406 Not Acceptable vs 409 Conflict (state conflict) vs 410 Gone vs 412 Precondition Failed vs 413 Content Too Large vs 415 Unsupported Media Type vs 422 Unprocessable Content (well-formed but semantically invalid) vs 428 Precondition Required vs 429 Too Many Requests (with `Retry-After`).
19. 500 internal vs 502 Bad Gateway (upstream gave a bad response) vs 503 Service Unavailable (overloaded or down, `Retry-After`) vs 504 Gateway Timeout (upstream too slow).
20. 409 vs 422 for the same failing request: conflict with server state vs payload semantically invalid.
21. A status code is a contract with caches, retries, browsers, monitors and clients. Wrong codes cause wrong behavior (200 with `{error}` hides failures from every monitor).

Headers
22. Request headers: `Host`, `User-Agent`, `Accept`, `Accept-Language`, `Accept-Encoding`, `Content-Type`, `Content-Length`, `Authorization`, `Cookie`, `Origin`, `Referer`, `If-None-Match`, `If-Match`, `Range`.
23. Response headers: `Content-Type`, `Content-Length`, `Content-Encoding`, `Set-Cookie`, `Cache-Control`, `ETag`, `Location`, `Allow`, `Retry-After`, `WWW-Authenticate`, `Vary`.
24. Custom headers: `X-Request-Id` (you add; echo and log it). Naming (no `X-` prefix rule for new standards, but widely used for app-specific).
25. Header syntax: case-insensitive names, comma-joined multiple values (except `Set-Cookie`, which must be separate lines).
26. Hop-by-hop vs end-to-end headers (`Connection`, `Keep-Alive`, `Transfer-Encoding`, `Upgrade`). Proxies add `Via`, `X-Forwarded-For`, `Forwarded`.
27. Content negotiation: `Accept` quality values (`q=`), `Content-Type` charset. One URL, multiple representations vs separate URLs; pick one policy per API.
28. Representation vs resource: the URL identifies the resource; JSON/HTML are representations.

### RESOURCES (in order)
1. MDN HTTP Guides in order: Overview, Messages, Methods, Status (reference pages: read Methods and Status fully): https://developer.mozilla.org/en-US/docs/Web/HTTP/Guides/Overview , https://developer.mozilla.org/en-US/docs/Web/HTTP/Reference/Methods , https://developer.mozilla.org/en-US/docs/Web/HTTP/Reference/Status .
2. RFC 9110, HTTP Semantics: https://www.rfc-editor.org/rfc/rfc9110.html . Read: section 3 (terminology), 6 (message abstraction, headers), 8 (representation metadata), 9 (methods, full), 12.5 (content negotiation, skim), 15 (status codes, full). Read RFC 9110 section 9.2 (safe and idempotent) twice.
3. MDN reference lists to skim as dictionaries: Request headers, Response headers: https://developer.mozilla.org/en-US/docs/Web/HTTP/Reference/Headers .
4. RFC 5789 (PATCH): https://www.rfc-editor.org/rfc/rfc5789 (just the introduction and section 2).
5. HPBN chapter 1, "Primer on Latency and Bandwidth": https://hpbn.co/primer-on-latency-and-bandwidth/ .
6. Hono docs: routing, context (`c.json`, `c.text`, `c.header`, `c.status`), middleware, HTTPException: https://hono.dev/docs/ . Read only these pages.

### START HERE (first 30 minutes)
1. Move `http-protocol` into `labs/http-lab`. Run it. `curl -v localhost:3000/notes`.
2. Write a `requests.http` file (the REST Client format) with 6 requests. Run them from the editor and compare with curl.
3. Read RFC 9110 section 9.2.1 (Safe Methods) and 9.2.2 (Idempotent Methods). Write one sentence for each in your own words.

### DRILLS
D1. Notes API in Hono with an in-memory store: `GET /notes`, `GET /notes/:id`, `POST /notes`, `PUT /notes/:id` (replace), `PATCH /notes/:id` (partial), `DELETE /notes/:id`, `HEAD /notes`, `OPTIONS /notes`. Ids from a monotonic counter (never reused). Acceptance: every route returns the correct code per the table in D2.
D2. A table in `docs/status-table.md`: for each route and each outcome, the code and why (200, 201 + `Location`, 204, 400 malformed JSON, 404, 405 + `Allow`, 409 on an ETag/version conflict, 415 wrong `Content-Type`, 422 validation with per-field errors, 429 simulated). Acceptance: `scripts/curl.sh` triggers every row with `curl -v` and shows it.
D3. Error contract: every error returns `{ "error": { "code": "NOTE_NOT_FOUND", "message": "...", "details": {...} } }`. Stable `code`. No stack traces. `X-Request-Id` on every response (reuse incoming, generate otherwise) and logged.
D4. Redirect lab: `/old` redirects to `/new` with 301, 302, 303, 307, 308. For each, with `curl -v -L -d '{}'` (no `-X`), record the method used on the second request; do not use `-X POST`, which forces the method. Then repeat from a browser form (a tiny HTML page with `<form method=post>`) and record what the browser did, from DevTools Network.
D5. Method lab: prove idempotency empirically. Call `PUT /notes/1` five times with the same body and show one state. Call `POST /notes` five times and show five rows. Call `DELETE /notes/1` twice and decide what the second returns (404 or 204) and defend it in NOTES.
D6. Content negotiation: `GET /notes/1` returns JSON when `Accept: application/json`, HTML when `Accept: text/html`, 406 for anything else; add `Vary: Accept`. Then rewrite with separate URLs (`/notes/1.json`) and write which you prefer and why.
D7. Hop-by-hop: put a tiny reverse proxy in front (`node:http` proxy of 30 lines or `caddy reverse-proxy`). Add `X-Forwarded-For`. Return a 502 by killing the upstream and a 504 by making the upstream sleep past the proxy timeout. Record both responses.
D8. URL lab: a function `buildUrl(base, path, query)` using `URL`/`URLSearchParams`; tests for spaces, unicode, `&`, `#`, repeated keys, and an attacker-controlled `//evil.com` path (open redirect awareness).
D9. `HEAD` lab: verify `HEAD /notes` returns the same headers as `GET /notes` including `Content-Length` but with an empty body (`curl -I`); note what Hono does and what it sets.

### BREAK
- Implement create as `GET /notes?title=x`. Write 3 concrete production failures (link preview bots, prefetch, proxy retries).
- Return 200 for every create; switch to 201 + `Location`; diff in `curl -v`.
- Return 200 with `{"error":...}` for a failure and show what your own monitoring (the logs) shows. Fix.
- Return 403 vs 404 for another user's note; write the product reasoning for each (leak existence?).
- Send a POST with a `GET`-only route; confirm 404 vs 405 and fix `Allow`.

### QUIZ (cold)
1. Safe vs idempotent vs neither, with methods and the reason for each.
2. Why can't GET mutate? Name three actors that will call it.
3. 301 vs 302 vs 307 vs 308 on a POST.
4. 401 vs 403 vs 404-to-hide: when do you pick each?
5. 400 vs 422 vs 409?
6. 502 vs 503 vs 504 in a reverse-proxy story.
7. What must a 405 response include? A 401? A 201?
8. Is the URL fragment sent to the server?
9. What does `Vary: Accept` tell a cache?
10. Why is `200 {"error":...}` harmful?

### GATE
Spoken (5 minutes): recite safe/idempotent with reasons; justify 401/403/404-to-hide as a product decision; tell the 502 vs 504 reverse-proxy story; choose between 409 and 422 for one concrete failing request.
Pass: quiz 9/10, D1 to D9, `docs/status-table.md`, `scripts/curl.sh`, tag `gate/M2.1`.

### DELIVERABLE AND FAILURE MODES
Deliverable: `labs/http-lab` notes API, status table, curl script, NOTES.
Faking it: picking statuses that "feel right". Rule: every status in the table has a one-line reason citing RFC 9110.

---

## M2.2 Cookies, origin vs site

Hours: 12. Prerequisite: M2.1. Phase: 2.

### WHY
Cookies are attached automatically by the browser. That single fact causes CSRF, session bugs, and half of auth mistakes. You cannot reason about CORS without separating origin from site.

### ATOMS
Cookie mechanics
1. A cookie is set by `Set-Cookie` in a response and sent back via `Cookie` in later requests matching its scope.
2. One `Set-Cookie` header per cookie (multiple headers, not comma-joined).
3. Name=value; attributes are metadata, not sent back. The server only receives `name=value` pairs.
4. `Domain`: absent means host-only (exact host only); present means that domain and all its subdomains. Prefer absent.
5. `Path`: prefix match on the URL path. Not a security boundary (any path on the same origin can read via `document.cookie` unless HttpOnly).
6. `Expires` (absolute date) vs `Max-Age` (seconds; wins if both). Session cookie = no expiry (cleared when the browser session ends, but restore-session behaviors exist).
7. `Secure`: only sent over HTTPS (localhost is treated as secure by modern browsers).
8. `HttpOnly`: hidden from `document.cookie`. XSS can still make requests that carry it; it prevents theft of the value.
9. `SameSite=Strict|Lax|None`: whether the cookie is sent on cross-site requests. `None` requires `Secure`. Browsers default to `Lax` when unspecified.
10. `Partitioned` (CHIPS): cookie keyed by top-level site; know it exists.
11. Prefixes: `__Secure-` (requires Secure), `__Host-` (requires Secure, no Domain, Path=/). Use `__Host-` for session cookies.
12. Deletion: re-send with the same name, domain, path and `Max-Age=0` or a past `Expires`.
13. Size and count limits (about 4KB per cookie, about 50 per domain; varies).
14. Cookies are sent on every matching request including images and static assets: cost and privacy.
15. Third-party cookies and browser restrictions (blocked or partitioned in most browsers). Know the vocabulary: first-party, third-party.

Origin vs site
16. Origin = scheme + host + port. Same-origin: all equal.
17. Site = scheme + registrable domain (eTLD+1, from the Public Suffix List). `a.example.com` and `b.example.com` are same-site, cross-origin. `example.com` and `example.org` are cross-site. `github.io` is a public suffix: `alice.github.io` and `bob.github.io` are cross-site.
18. Schemeful same-site: `http://x.com` and `https://x.com` are cross-site in modern browsers.
19. Which mechanism uses which: CORS and `postMessage` and `localStorage` and the same-origin policy use origin; SameSite cookies and `Sec-Fetch-Site: same-site` use site.
20. What the same-origin policy blocks (cross-origin reads of responses, DOM access) vs allows (embedding images/scripts/stylesheets, form submissions, navigation, sending requests). Sending is allowed; reading is blocked.

SameSite behavior in practice
21. `Strict`: not sent on any cross-site request, including top-level link navigation.
22. `Lax`: sent on cross-site top-level navigations with safe methods (clicking a link from another site) but not on cross-site subresource requests or cross-site POST forms.
23. `None; Secure`: sent always (embeds, widgets, SSO).
24. Lax has a "Lax+POST" two-minute exception in Chrome for cookies without an explicit SameSite; know it exists.
25. Why SameSite is a defense layer against CSRF, not a full solution (same-site attackers, subdomain takeover, GET with side effects).

Storage tradeoffs
26. `localStorage` / `sessionStorage`: JS-readable, any XSS steals them permanently, not sent automatically. A bearer token in localStorage is stolen by XSS and used from anywhere. A HttpOnly cookie can only be used from the victim's browser through the page.
27. Where to put a session identifier for a first-party web app: HttpOnly, Secure, SameSite=Lax cookie holding an opaque ID.

### RESOURCES (in order)
1. MDN, "Using HTTP cookies": https://developer.mozilla.org/en-US/docs/Web/HTTP/Guides/Cookies . Full page.
2. MDN, `Set-Cookie` header reference: https://developer.mozilla.org/en-US/docs/Web/HTTP/Reference/Headers/Set-Cookie . Every attribute.
3. web.dev, "Understanding 'same-site' and 'same-origin'": https://web.dev/articles/same-site-same-origin . Full article; reproduce the table.
4. web.dev, "SameSite cookies explained": https://web.dev/articles/samesite-cookies-explained .
5. MDN, "Same-origin policy": https://developer.mozilla.org/en-US/docs/Web/Security/Same-origin_policy .
6. RFC 6265bis (current cookie spec draft): https://httpwg.org/http-extensions/draft-ietf-httpbis-rfc6265bis.html . Read: sections 4.1 (Set-Cookie syntax), 5.5 (storage model; read the SameSite and prefix parts), 8 (security considerations). Skim the rest.
7. Public Suffix List: https://publicsuffix.org/ . Read "About" and browse a few entries.
8. OWASP Session Management Cheat Sheet, only the "Cookies" section for now: https://cheatsheetseries.owasp.org/cheatsheets/Session_Management_Cheat_Sheet.html .

### START HERE (first 30 minutes)
1. Add to `/etc/hosts` (needs sudo): `127.0.0.1 shop.test evil.test app.shop.test api.shop.test`. Chrome resolves these to localhost.
2. In Hono add `GET /set` that sends `Set-Cookie: a=1` and `GET /read` that returns the `Cookie` header. Visit `http://shop.test:3000/set` then `/read`.
3. Open DevTools Application tab, Cookies. Find `a`. Read each column.

### DRILLS
D1. Session cookie: `POST /login` sets `Set-Cookie: sid=<random>; HttpOnly; Secure; SameSite=Lax; Path=/; Max-Age=3600` (omit `Secure` over plain HTTP locally and write down in NOTES that this is a lie you tolerate only on localhost; or run Caddy for local HTTPS with `caddy run`). `GET /me` reads it. `POST /logout` clears it.
D2. HttpOnly proof: a static page at the same origin runs `console.log(document.cookie)`. Show the session cookie is invisible. Then set a second non-HttpOnly cookie and show it is visible.
D3. Attribute lab, one by one, each observed in DevTools: `Path=/admin` (sent for `/admin/x` but not `/`), `Domain=shop.test` (sent to `app.shop.test`) vs host-only (not sent), `Max-Age=5` (expires), `Expires` in the past (deleted), `Secure` on HTTP (not stored/sent), `__Host-` with a `Domain` attribute (rejected).
D4. Origin vs site table: for each of 10 URL pairs (such as `http://shop.test:3000` vs `http://shop.test:4000`, `https://shop.test` vs `http://shop.test`, `app.shop.test` vs `api.shop.test`, `shop.test` vs `evil.test`), state same-origin? same-site? in a table, then verify by reading `Sec-Fetch-Site` headers at the server.
D5. SameSite lab, with two sites `shop.test:3000` (the API) and `evil.test:4000` (a page with a link, a form, an image, and a `fetch`):
   - set cookies with `SameSite=Strict`, `Lax`, `None; Secure`, and none
   - from evil: click a link to a shop page (top-level GET navigation), submit a cross-site POST form, load an `<img src>` to shop, `fetch(..., {credentials:'include'})`
   - fill a 4x4 table of "cookie sent?" from the server logs
   Acceptance: matches the table from the docs; any mismatch is explained.
D6. Same-site, cross-origin: `app.shop.test:3000` calls `api.shop.test:3001` with `credentials:'include'` (this requires CORS from M2.3; do it after M2.3 and return to this drill). Record how `SameSite=Strict` cookies behave here (same-site, so sent).
D7. Cookie jar via curl: log in with `-c jar.txt`, replay with `-b jar.txt`, inspect the jar file format, and show a `Secure` cookie not sent over HTTP.
D8. Storage comparison page: store a token in `localStorage` and a session in an HttpOnly cookie; run a pretend XSS (`<img src=x onerror=...>` injected into a note field rendered with `innerHTML`); show the token is exfiltrated to a listener on `evil.test` while the HttpOnly cookie is not readable. Then show what the XSS can still do with the HttpOnly cookie: `fetch('/api/transfer', {method:'POST'})`.

### BREAK
- Give `SameSite=None` without `Secure`; record the browser warning.
- Set a cookie larger than 4KB; record behavior.
- Set `Domain=.shop.test` and show a sibling subdomain can overwrite it (cookie tossing); explain why `__Host-` prevents it.
- Set two cookies with the same name but different `Path`s and show the order they arrive in.
- Remove `HttpOnly` and run the XSS drill again to see the theft.

### QUIZ (cold)
1. Origin vs site: define each and give a pair that is same-site but cross-origin.
2. What does `HttpOnly` stop, and what does it not?
3. What does `__Host-` require and what attack does it stop?
4. `Strict` vs `Lax` vs `None`, on link click, cross-site form POST, image embed.
5. Does `Max-Age` or `Expires` win?
6. Why are localStorage tokens worse than HttpOnly cookies against XSS?
7. What is a host-only cookie?
8. Which mechanism uses origin and which uses site?
9. Why must `Set-Cookie` be separate header lines?
10. What does the SOP block: sending or reading?

### GATE
Spoken (4 minutes): explain cookie scoping (`Domain`, `Path`, host-only), SameSite on three request types, the origin/site difference with two example pairs, and why a session ID belongs in an HttpOnly cookie.
Pass: quiz 9/10, D1 to D8, table committed, tag `gate/M2.2`.

### DELIVERABLE AND FAILURE MODES
Deliverable: `labs/http-lab/cookies/`, the 4x4 SameSite table, NOTES.
Faking it: reading a blog table of SameSite behavior instead of observing it. The table must come from your server logs.

---

## M2.3 CORS

Hours: 14. Prerequisite: M2.2. Phase: 2.

### WHY
CORS is the most misunderstood browser mechanism. "Enable CORS" scripts that set `*` are everywhere. CORS gates reading a response from JavaScript on another origin. It does not stop requests from being sent. It is not a security layer for your server and not CSRF protection.

### ATOMS
Foundations
1. CORS is a browser policy implemented by browsers reading response headers. `curl`, servers, and mobile apps ignore it.
2. The default (same-origin policy): JavaScript on origin A may send a request to origin B, but may not read the response (for `fetch`/XHR).
3. CORS is a relaxation of the SOP: the server of B says "origin A may read my response".
4. The error "blocked by CORS policy" always means a browser blocked JavaScript from reading a response. The request may have reached your server and executed.
5. `Origin` request header: set by the browser on cross-origin requests (and on same-origin non-GET/HEAD). Never settable by page JS (forbidden header).
6. `Access-Control-Allow-Origin` (ACAO): a single origin, or `*`, or `null`. Not a list.
7. Opaque responses: `fetch(url, {mode:'no-cors'})` yields an opaque response (status 0, no body readable). Used for fire-and-forget. Not a fix for CORS errors.

Simple requests
8. A "simple" request needs no preflight: method `GET`, `HEAD`, or `POST`; only CORS-safelisted headers (`Accept`, `Accept-Language`, `Content-Language`, `Content-Type` limited to `application/x-www-form-urlencoded`, `multipart/form-data`, `text/plain`, plus `Range` simple values); no event listeners on `XMLHttpRequest.upload`; no `ReadableStream` body.
9. `Content-Type: application/json` makes a request non-simple, triggering a preflight.
10. A custom header (`X-Request-Id`, `Authorization`) triggers a preflight.
11. A non-simple method (`PUT`, `DELETE`, `PATCH`) triggers a preflight.
12. For a simple request the browser sends it, then checks the response headers; if ACAO is missing or wrong, JS can't read the response (but the server already acted).

Preflight
13. The preflight is an `OPTIONS` request the browser sends first, with `Origin`, `Access-Control-Request-Method`, and `Access-Control-Request-Headers`. It carries no cookies and no body.
14. The server answers with status 2xx (200/204) and `Access-Control-Allow-Origin`, `Access-Control-Allow-Methods`, `Access-Control-Allow-Headers`, optionally `Access-Control-Max-Age`.
15. If the preflight fails, the actual request is never sent.
16. Preflight caching: `Access-Control-Max-Age` (seconds; browsers cap it: Chrome 2h, Firefox 24h); without it Chrome defaults to 5 seconds. The `Vary` header interplay.
17. Preflight is a round trip: a custom header can double your request count and latency. Design APIs to avoid unneeded preflights (same-origin proxy, avoid custom headers).
18. The preflight is a protocol check for old servers, not an auth check. Don't treat it as CSRF protection: a simple cross-site request needs no preflight and the cookie still rides along.

Credentials
19. `fetch(url, {credentials: 'include'})` (or `withCredentials`) sends cookies/auth cross-origin. Default is `same-origin`.
20. With credentials, the server must send `Access-Control-Allow-Credentials: true` and ACAO must be an explicit origin, not `*`. `*` with credentials is rejected.
21. Same for `Access-Control-Allow-Headers: *` and `Allow-Methods: *`: wildcards are not honored for credentialed requests.
22. Reflecting the request `Origin` back without an allowlist is a vulnerability (any site can read credentialed responses).
23. `Access-Control-Allow-Origin: null`: sent by sandboxed iframes and `file://`; never allow it.
24. Third-party cookie restrictions interplay with CORS plus `SameSite=None` cookies.

Response exposure and caching
25. `Access-Control-Expose-Headers`: by default JS can read only CORS-safelisted response headers (`Cache-Control`, `Content-Language`, `Content-Length`, `Content-Type`, `Expires`, `Last-Modified`, `Pragma`). Custom ones (`X-Request-Id`, `ETag`) need exposing.
26. `Vary: Origin` is required whenever ACAO varies by request origin, so shared caches don't serve a response with the wrong ACAO. Missing `Vary: Origin` is a real bug.
27. Preflight and `Vary: Access-Control-Request-Headers`.

Boundaries and related
28. CORS vs CSRF: CORS limits who can read a response. CSRF abuses automatic credentials to cause a state-changing request. A cross-site `POST` form needs no CORS at all.
29. CORS vs CSP `connect-src`: CSP restricts what your page may call; CORS restricts who may read your server's response.
30. Private Network Access / Local Network Access (public sites calling `localhost`): know it exists.
31. Cross-Origin-Resource-Policy (CORP), Cross-Origin-Opener-Policy (COOP), Cross-Origin-Embedder-Policy (COEP): know the names and purposes (isolation, Spectre mitigations); not drilled.
32. WebSockets are not subject to CORS; the server must check `Origin` itself (M2.6).
33. Debugging method: read the console error text exactly, find the failing request in the Network tab (the preflight is a separate `OPTIONS` row), compare request headers with the response's `Access-Control-*`.
34. A same-origin proxy (`/api` routed by the dev server or the CDN to the API) removes CORS entirely; how real apps avoid CORS.

### RESOURCES (in order)
1. MDN, CORS guide: https://developer.mozilla.org/en-US/docs/Web/HTTP/Guides/CORS . Full page twice: once for flow, once for each header.
2. MDN, CORS errors reference (one page per error message): https://developer.mozilla.org/en-US/docs/Web/HTTP/Guides/CORS/Errors . Read all of the entries.
3. WHATWG Fetch Standard, "CORS protocol" and "CORS-preflight fetch" sections: https://fetch.spec.whatwg.org/#http-cors-protocol . Read: 3.2 CORS protocol, 3.2.2 HTTP requests, 3.2.3 HTTP responses, 3.2.4 HTTP new-header syntax, 3.2.5 CORS protocol and credentials, 3.2.6 Examples, and the "CORS-safelisted request-header" definitions.
4. web.dev, "Cross-Origin Resource Sharing": https://web.dev/articles/cross-origin-resource-sharing (if moved, search the title on web.dev).
5. MDN: `Origin` header, `Access-Control-*` header pages, forbidden request headers: https://developer.mozilla.org/en-US/docs/Glossary/Forbidden_request_header .
6. MDN `fetch()` `mode`, `credentials` options: https://developer.mozilla.org/en-US/docs/Web/API/Window/fetch#options .
7. Hono CORS middleware source (read it in `node_modules/hono/dist/middleware/cors/index.js`): note what it reflects, when it sets `Vary`, how it handles `OPTIONS`.
8. OWASP, CORS misconfiguration notes: https://cheatsheetseries.owasp.org/cheatsheets/HTML5_Security_Cheat_Sheet.html (the "Cross Origin Resource Sharing" section).

### START HERE (first 30 minutes)
1. Run two origins: the API on `http://localhost:3000`, a static page on `http://localhost:4000` (use `bunx serve -p 4000 static/`).
2. In the page: `fetch('http://localhost:3000/notes').then(r=>r.json()).then(console.log)`. See the error. Copy it into `NOTES.md` verbatim.
3. In DevTools Network, find the request; note the server did respond (status 200). In your server log, confirm it ran.
4. Run the same from `curl`. It works. Write one sentence on why.

### DRILLS
D1. Zero CORS: serve no CORS headers at all. From the static page run a simple `GET` and show: the request was sent, the server logged it, the browser blocked the read. Add a counter that increments on the server to prove the side effect (use a `GET /hit` endpoint that increments), then do the same with a simple `POST` form-encoded request: the counter increments even though JS cannot read the response.
D2. Hand-written CORS, no middleware: add `Access-Control-Allow-Origin: http://localhost:4000` to responses. The `GET` now works. Add `Vary: Origin`. Confirm in DevTools. Explain in NOTES why `Vary: Origin` matters.
D3. Preflight trigger matrix: from the page send 8 requests: `GET` plain, `GET` with `X-Request-Id`, `POST` with `text/plain`, `POST` with `application/json`, `PUT`, `DELETE`, `PATCH`, `GET` with `Authorization`. For each, record "preflight? yes/no" and "why" from the Network tab (the extra `OPTIONS` row). Acceptance: matches the safelist rules in atoms 8 to 11.
D4. Handle the preflight by hand: implement `OPTIONS /notes` returning 204 with `Access-Control-Allow-Origin`, `Access-Control-Allow-Methods: GET,POST,PUT,PATCH,DELETE`, `Access-Control-Allow-Headers: content-type,x-request-id`, `Access-Control-Max-Age: 600`. Log every `OPTIONS` with the `Access-Control-Request-*` values. Show the preflight is cached (second call has no `OPTIONS`) and show Chrome's cap by setting `Max-Age: 86400`.
D5. Allowlist: implement `origin => allowList.has(origin) ? origin : null`. Test with allowed, disallowed, missing (`curl`), and `null` origin (sandboxed iframe: `<iframe sandbox srcdoc="<script>fetch(...)</script>">`). Show the `null` origin is rejected.
D6. Credentials: log in on `localhost:3000` (cookie), then from `localhost:4000` call `fetch('/me', {credentials:'include'})`. Observe: fails without `Access-Control-Allow-Credentials`. Add it with an explicit origin; works. Then try `ACAO: *` with credentials; record the exact browser error (this is a "break").
D7. Cookie plus CORS plus SameSite: with `SameSite=Lax` the cross-site `fetch` from `localhost:4000` to `localhost:3000`: note that localhost ports are same-site (same host), so the cookie IS sent; repeat using `shop.test` and `evil.test` from M2.2 so the sites differ, and record which cookie variants are sent for `fetch`. This ties the origin/site axes together.
D8. Expose headers: return `X-Request-Id` and `ETag`; from the page read `response.headers.get('X-Request-Id')` and see `null`; add `Access-Control-Expose-Headers`; read it.
D9. Error reading: for each of 6 broken configurations (missing ACAO, wildcard with credentials, missing allowed header, missing allowed method, preflight returns 404, redirect on preflight), write the exact console error, then the MDN "CORS errors" entry that matches, then the fix.
D10. Same-origin proxy: add a `/api/*` proxy in the static server (or Hono) to the API so the page calls `/api/notes` with no CORS at all. Write when you'd use the proxy approach vs CORS in production.
D11. CSRF through CORS misunderstanding: from `evil.test`, submit a cross-site `<form method=POST action="http://shop.test:3000/transfer">` with a `SameSite=None; Secure` cookie. The server-side action happens regardless of CORS. Write one paragraph, in your own words, why CORS is not CSRF protection. (The fix comes in M5.3.)

### BREAK
- Reflect the `Origin` header blindly with credentials on, and from `evil.test` read `/me` JSON (this is the vulnerability). Fix with an allowlist.
- Forget `Vary: Origin`, put a cache in front (the in-process cache from M2.4 or `caddy` with cache), request from origin A then origin B, and observe B receives A's ACAO and fails.
- Return the preflight with a 301 redirect; see the failure.
- Return 200 on `OPTIONS` but omit `Access-Control-Allow-Headers` for `content-type`; read the exact message.
- Add `Access-Control-Allow-Origin: *` on an endpoint that returns private data without cookies (e.g., an internal-network IP); explain how any website can read it from a victim's browser (the router/intranet problem).

### QUIZ (cold)
1. Who enforces CORS, and who ignores it?
2. What does CORS actually restrict: sending or reading?
3. What makes a request "simple"? What triggers a preflight? Give three triggers.
4. What does a preflight request contain and what must the response contain?
5. Why can't `Access-Control-Allow-Origin: *` be used with credentials?
6. What is wrong with reflecting `Origin` without a list?
7. What does `Vary: Origin` prevent?
8. Why is a preflight not CSRF protection?
9. Which response headers can JS read by default, and how do you expose others?
10. How do you remove CORS from an architecture, and when is that better than configuring it?

### GATE
Spoken (4 minutes): tell CORS, cookies, and CSRF as one continuous story: browser auto-attaches cookies to matching requests; an attacker page can cause a state-changing request that carries them; `SameSite` reduces this; CORS is irrelevant to that request because it only gates reading the response; preflight only protects non-simple requests, and the attacker uses simple ones.
Written: the trigger matrix (D3), the credentials story (D6), and the six error messages (D9) from memory for at least three.
Pass: quiz 9/10, D1 to D11, tag `gate/M2.3`.

### DELIVERABLE AND FAILURE MODES
Deliverable: `labs/http-lab/cors/` with the two-origin setup, trigger matrix, error table.
Faking it: using `cors()` middleware and moving on. The module is passed only when you can predict, before running, whether each request preflights and what the response must contain.

---

## M2.4 HTTP caching

Hours: 14. Prerequisite: M2.1. Phase: 2.

### WHY
The fastest request is the one never made. A wrong cache header either serves stale or, worse, serves user A's data to user B. "Next is refetching twice" is almost always two caches interacting.

### ATOMS
Cache types and freshness
1. Private cache (the browser) vs shared cache (CDN, reverse proxy, corporate proxy). Shared caches must never store user-specific responses.
2. A stored response is fresh if its age is less than its freshness lifetime. Age comes from `Age` header and `Date`.
3. Freshness lifetime: `s-maxage` (shared only), then `max-age`, then `Expires`, then heuristic freshness (typically 10 percent of time since `Last-Modified`) when neither is present. Never rely on heuristics.
4. `Cache-Control` request directives (client side): `max-age=0`, `no-cache`, `no-store`, `only-if-cached`. Browser reload types: normal reload (revalidates the main resource), hard reload (bypasses cache), back/forward (may use bfcache or stale).
5. bfcache (back/forward cache): whole-page snapshot; `Cache-Control: no-store` and `unload` handlers historically disabled it.

`Cache-Control` response directives
6. `max-age=N`: fresh for N seconds in any cache.
7. `s-maxage=N`: shared caches only, overrides `max-age` there.
8. `no-store`: do not store (anywhere). Use for sensitive responses.
9. `no-cache`: you MAY store it, but MUST revalidate before every reuse. It does not mean "do not cache".
10. `must-revalidate`: once stale, must revalidate; do not serve stale on error.
11. `proxy-revalidate`: shared-cache version.
12. `private`: only the browser may store. `public`: shared caches may store even if the request had `Authorization`.
13. `immutable`: do not revalidate during freshness even on reload (RFC 8246). Pair with fingerprinted URLs.
14. `stale-while-revalidate=N`: serve stale for N seconds while revalidating in the background (RFC 5861).
15. `stale-if-error=N`: serve stale if origin errors.
16. `no-transform`: intermediaries must not modify the body.
17. Defaults and conflicts: `Pragma: no-cache` (legacy), `Expires` (legacy), what happens when headers conflict.

Validation
18. Validators: `ETag` (opaque identifier) and `Last-Modified` (time).
19. Strong ETag (`"abc"`) vs weak (`W/"abc"`): weak means semantically equivalent, not byte-identical.
20. Conditional GET: `If-None-Match: "abc"` (preferred) or `If-Modified-Since`. Server responds `304 Not Modified` with no body (headers like `ETag`, `Cache-Control`, `Date` refresh freshness) or `200` with the new body.
21. Conditional writes: `If-Match` on `PUT`/`PATCH`/`DELETE` returns `412 Precondition Failed` if the ETag changed: an HTTP-native lost-update guard (and `428` when the precondition is required but missing). Connects to M4.3.
22. ETag generation: hash of the body (cheap to implement, costs CPU), or a version/updated_at (cheap). Gzip/Brotli variants must have different ETags or use weak ETags.
23. `Range` requests and `206 Partial Content`, `If-Range`: know the mechanism (video, resume downloads).

`Vary` and cache keys
24. A cache key is method plus URL plus the request headers named in `Vary`.
25. `Vary: Accept-Encoding` is normal; `Vary: Accept` for content negotiation; `Vary: Origin` for CORS; `Vary: Cookie` effectively disables shared caching (a cache entry per unique cookie) and is nearly always a smell.
26. `Vary: *` means uncacheable.

Policies by resource shape
27. Fingerprinted static assets (`app.9f3a.js`): `public, max-age=31536000, immutable`.
28. HTML documents: `no-cache` plus `ETag` (always revalidate, rarely re-download) or short `max-age` plus `stale-while-revalidate` for public pages.
29. User-specific JSON/HTML: `private, no-store` (or `private` with short `max-age` if staleness is acceptable); never `public`.
30. Public API responses cached at a CDN: `public, s-maxage=60, stale-while-revalidate=300` with invalidation or short TTL.
31. Error responses and caching: 404 can be cached (negative caching); 5xx usually shouldn't. `Cache-Control: no-store` on auth responses.
32. Responses with `Set-Cookie` and shared caches: CDNs often refuse to cache or strip it; don't put session cookies on cacheable responses.
33. Side effects: a successful unsafe request (`POST`/`PUT`/`DELETE`) invalidates cached responses for the same URL (RFC 9111 section 4.4); other URLs stay stale. This is the design problem.

Invalidation and CDNs
34. You cannot purge a user's browser cache. You change the URL (fingerprint/versioned path) or wait out the TTL.
35. CDNs can be purged by URL, tag (surrogate keys), or wholesale; headers like `Surrogate-Control`, `CDN-Cache-Control`, `Cache-Tag` (vendor specific).
36. Service workers and the Cache API are separate programmable caches (know they exist; avoid for now).
37. Why caches hide bugs: you test locally with cache disabled and the bug exists only for returning users.
38. `Cache-Control: no-store` vs deleting cached data after logout (`Clear-Site-Data` header).

### RESOURCES (in order)
1. web.dev, "Prevent unnecessary network requests with the HTTP Cache": https://web.dev/articles/http-cache . Full article.
2. Jake Archibald, "Caching best practices & max-age gotchas": https://jakearchibald.com/2016/caching-best-practices/ . Full article.
3. MDN, "HTTP caching": https://developer.mozilla.org/en-US/docs/Web/HTTP/Guides/Caching . Full page; then `Cache-Control` https://developer.mozilla.org/en-US/docs/Web/HTTP/Reference/Headers/Cache-Control and `ETag` https://developer.mozilla.org/en-US/docs/Web/HTTP/Reference/Headers/ETag and `Vary` pages.
4. RFC 9111, HTTP Caching: https://www.rfc-editor.org/rfc/rfc9111.html . Read: section 1.2 (terms), 3 (storing), 4 (constructing responses, 4.2 freshness, 4.3 validation, 4.4 invalidation), 5.2 (Cache-Control). Skip: 6 onward.
5. RFC 8246 (immutable) https://www.rfc-editor.org/rfc/rfc8246 and RFC 5861 (SWR, SIE) https://www.rfc-editor.org/rfc/rfc5861 . Short; read fully.
6. RFC 9110 section 13 (Conditional Requests), 8.8 (validators): https://www.rfc-editor.org/rfc/rfc9110.html#name-conditional-requests .
7. HPBN, HTTP caching sections of "HTTP/1.X": https://hpbn.co/http1x/ .
8. Chrome DevTools: "Network features reference" sections on cache disabling, "Size" column (`(disk cache)`, `(memory cache)`), and the "Timing" tab.

### START HERE (first 30 minutes)
1. Add `GET /public/quote` returning a quote and a counter of how many times the handler ran.
2. `curl -v` it twice; confirm both hit the handler.
3. Add `Cache-Control: public, max-age=30`. In the browser (DevTools, cache enabled) reload twice; read the Size column. Then `curl -v` again: curl does not cache. Write one sentence on why.

### DRILLS
D1. ETag: `GET /public/quote` with `ETag` = weak/strong hash of the body. `If-None-Match` round trip returns 304. Prove with `curl -v -H 'If-None-Match: "<etag>"'`; show the 304 has no body and still carries `Cache-Control` and `ETag`.
D2. `Last-Modified` and `If-Modified-Since` variant, same endpoint; compare precision (seconds) with ETag, and say when you'd choose each.
D3. Policies by shape: implement `GET /assets/app.<hash>.js` with `public, max-age=31536000, immutable`; `GET /` HTML with `no-cache` + `ETag`; `GET /api/me` with `private, no-store`; `GET /public/page` with `public, max-age=60, s-maxage=300, stale-while-revalidate=600`. For each, observe in DevTools the Size and Status columns on: first load, normal reload, hard reload, navigation within the site.
D4. Reload semantics: for the fingerprinted asset with `immutable` and without it, compare what a normal reload sends (does it send `If-None-Match`?). Record in a table per browser (Chrome and Firefox).
D5. In-process shared cache: a tiny reverse-cache (Hono middleware) keyed by method+URL (+ `Vary` headers) with TTL 10s in front of `GET /public/quote`. `POST /public/quote` changes the quote. First, leave it stale and observe up to 10s staleness. Then invalidate on POST and prove immediacy. Then add the user-specific bug in BREAK.
D6. `Vary`: serve gzip vs identity based on `Accept-Encoding`, add `Vary: Accept-Encoding`; test with `curl --compressed` and without; show a cache keyed correctly. Remove `Vary` and show the mistake (a client that can't decode gzip receives gzip).
D7. Conditional writes: implement `PUT /notes/:id` that requires `If-Match` (428 if missing, 412 on mismatch, 200 and a new `ETag` on success). Reproduce a lost update with two `curl` sessions, then show 412 stops it.
D8. `stale-while-revalidate`: implement a cache layer that serves stale and revalidates in the background; show with logs that the user got the stale response immediately and the next request got the fresh one.
D9. CDN simulation: run `caddy` with a cache plugin, or Cloudflare/Fastly free tier, or Bunny CDN trial; cache the public page; observe `Age`, `X-Cache`/`CF-Cache-Status`; purge it; observe re-fetch.
D10. Clear on logout: send `Clear-Site-Data: "cache"` on `/logout`; verify in the Application panel that the cache clears (Chrome).

### BREAK
- Set `max-age=60` on `/api/me` with a naive shared cache keyed by URL only. Log in as two users (two cookies). User B receives user A's JSON. Fix with `private`; separately write why `Vary: Cookie` is a bad fix (a cache entry per cookie value defeats caching and still risks leaks).
- Use `no-cache` where you meant "don't store"; show the data on disk in the DevTools Application > Cache storage or the browser's cache viewer (`chrome://cache` is gone; use `about:cache` in Firefox) and say what you should have used.
- Ship HTML with `max-age=31536000` and a changing asset URL; observe users stuck on an old version. Write the recovery plan (you cannot purge browsers).
- Serve a 404 for a path then create it; show negative caching delay.
- Compute an ETag from the response before compression and serve both variants with the same strong ETag; explain the bug.

### QUIZ (cold)
1. What does `no-cache` mean? `no-store`? `must-revalidate`?
2. `max-age` vs `s-maxage`?
3. What does a 304 contain, and what does the cache do with it?
4. What does `Vary` do? Why is `Vary: Cookie` a smell?
5. Write cache headers for: fingerprinted JS; HTML of a logged-in app; a public marketing page behind a CDN; user-specific JSON.
6. How do you invalidate a user's browser cache?
7. What does `immutable` change about reloads?
8. How can `If-Match` prevent a lost update?
9. What does a successful `POST` do to cached `GET`s of the same URL, and of other URLs?
10. Strong vs weak ETag?

### GATE
Spoken (4 minutes): write the four cache policies from memory, explain `no-cache` vs `no-store` in one sentence each, and tell the shared-cache leak story and its fix.
Pass: quiz 9/10, D1 to D10, tag `gate/M2.4`. If you use `no-cache` meaning "don't cache", the gate fails and you repeat the module.

### DELIVERABLE AND FAILURE MODES
Deliverable: `labs/http-lab/cache/`, a `curl` script that proves 304, SWR, and the leak, NOTES.
Faking it: reciting directives without having seen the Size column and `(disk cache)` entries. Evidence required: screenshots of each reload type.

---

## M2.5 Transport: latency, TCP, TLS, HTTP/1.1, HTTP/2, HTTP/3

Hours: 12. Prerequisite: M2.4. Phase: 2.

### WHY
When someone says "HTTP/2 is faster" or "use the edge", you should know which specific round trip they remove. Waterfalls multiply latency.

### ATOMS
Latency and bandwidth
1. Bandwidth is throughput; latency is the delay per round trip. For small requests latency dominates. A 100ms RTT costs 100ms per sequential round trip regardless of bandwidth.
2. Speed-of-light floor: about 5 ms per 1000 km one way in fiber; Europe to the US is about 80 to 100 ms RTT.
3. Bandwidth-delay product and why throughput grows over time (slow start).
4. Waterfall: N sequential dependent requests cost at least N round trips. 6 calls at 100ms RTT is at least 600ms.

DNS, TCP, TLS
5. DNS resolution: stub resolver, recursive resolver, TTLs, caching, `A`/`AAAA`/`CNAME`, DNS cost on the first connection to a new host, `dns-prefetch`.
6. TCP: SYN, SYN-ACK, ACK (1 RTT before data), reliable ordered byte stream, retransmission, congestion control, slow start.
7. TLS 1.2: 2 extra RTTs; TLS 1.3: 1 RTT (0-RTT resumption possible with replay risks). SNI, ALPN (negotiates `h2`/`http/1.1`), certificates (chain, CA, expiry, hostname match), OCSP stapling (know the term).
8. What TLS gives you: confidentiality, integrity, server authentication. What it does not: a trustworthy server, XSS protection, protection from your own bugs.
9. HSTS (`Strict-Transport-Security`): forces HTTPS for a domain for max-age; preload list; first-visit risk.
10. Connection reuse: HTTP keep-alive, connection pooling, why a cold connection is expensive.

HTTP/1.1
11. One request in flight per connection; browsers open about 6 connections per origin.
12. Head-of-line blocking at the HTTP layer; domain sharding as an old workaround; pipelining is dead.
13. Text protocol with verbose, repeated headers (cookies repeated on every request).

HTTP/2
14. Binary framing, multiple streams multiplexed over one TCP connection; HPACK header compression; stream priorities (replaced by Extensible Priorities); server push (removed from browsers).
15. Fixes HTTP-layer HOL blocking. TCP-layer HOL blocking remains: one lost packet stalls all streams.
16. Practical effects: bundling is less necessary but still helps compression; fewer connections; more important to avoid long dependency chains.

HTTP/3
17. QUIC over UDP: independent streams (no cross-stream HOL blocking), 1-RTT or 0-RTT connect with TLS 1.3 built-in, connection migration (Wi-Fi to cellular), user-space implementation. `Alt-Svc` header for discovery.
18. Why UDP: avoids ossified middleboxes and kernel TCP stack changes.
19. How browsers pick a protocol: ALPN for h2/h1, `Alt-Svc` or HTTPS DNS records for h3; fallback paths.

Practical
20. `Connection: keep-alive`, `Keep-Alive` timeouts, server idle timeouts vs load balancer idle timeouts (a classic 502 source).
21. Proxy and load balancer layers: L4 vs L7, TLS termination, `X-Forwarded-*`, `Forwarded`, real client IP trust.
22. Preconnect, preload, prefetch (preview, drilled in M3.1).
23. Measurement: DevTools Timing breakdown (Queueing, Stalled, DNS, Initial connection, SSL, Request sent, Waiting (TTFB), Content Download); `curl -w`; WebPageTest.

### RESOURCES (in order)
1. HPBN (free): https://hpbn.co/ . Read in order: Chapter 1 "Primer on Latency and Bandwidth"; Chapter 2 "Building Blocks of TCP" (the handshake, slow start, congestion, head-of-line blocking sections); Chapter 4 "Transport Layer Security (TLS)" (handshake, session resumption, certificate chain, performance checklist); Chapter 9 "Brief History of HTTP"; Chapter 11 "HTTP/1.X"; Chapter 12 "HTTP/2" (binary framing, multiplexing, header compression, server push). Skip: UDP, Wi-Fi, mobile networks chapters for now.
2. Daniel Stenberg, "HTTP/3 explained": https://http3-explained.haxx.se/ . Read: "HTTP/3 and QUIC" overview chapters (about an hour).
3. MDN: Evolution of HTTP https://developer.mozilla.org/en-US/docs/Web/HTTP/Guides/Evolution_of_HTTP ; HTTP/2 glossary; QUIC glossary; `Strict-Transport-Security` https://developer.mozilla.org/en-US/docs/Web/HTTP/Reference/Headers/Strict-Transport-Security ; `Alt-Svc`.
4. Cloudflare Learning Center: "What is HTTP/3?" and "What is TLS handshake?" as friendly diagrams: https://www.cloudflare.com/learning/ .
5. RFC 9113 (HTTP/2) sections 1 to 5 skim https://www.rfc-editor.org/rfc/rfc9113 ; RFC 9114 (HTTP/3) intro. Read only the introductions and the section on streams.
6. Chrome DevTools: Network "Timing" tab documentation: https://developer.chrome.com/docs/devtools/network/reference#timing-explanation .
7. Mozilla SSL Configuration Generator for a sane TLS config: https://ssl-config.mozilla.org/ (read, don't copy).

### START HERE (first 30 minutes)
1. `curl -w '%{time_namelookup} %{time_connect} %{time_appconnect} %{time_starttransfer} %{time_total}\n' -o /dev/null -s https://example.com/` three times. Explain every number's difference.
2. `curl -v --http1.1 https://example.com/` and `curl -v --http2 https://example.com/`; compare ALPN lines.
3. `openssl s_client -connect example.com:443 -servername example.com </dev/null` and read the certificate chain.

### DRILLS
D1. `scripts/timing.sh <url>` printing DNS/connect/TLS/TTFB/total in a table, run against 5 hosts at different distances (a local server, a nearby CDN site, a far-away site); explain differences with RTT.
D2. Fake latency: add a 100ms `await sleep` to every handler (or use `tc netem delay 100ms` on loopback with sudo, or `toxiproxy`). Build `GET /page-data` (one payload) and `GET /a`, `/b`, `/c`, `/d`, `/e`, `/f` (six small endpoints). A client that fetches six sequentially vs `Promise.all` vs the single payload; measure all three with Slow 3G throttling and without. Acceptance: table with numbers and a one-line explanation per row.
D3. Waterfall reading: save a HAR of any site you built (disable cache, Slow 3G). Annotate every request: queueing, DNS, connect, TLS, TTFB, download. Mark each bar as blocked-by-what.
D4. HTTP/2 observation: serve 30 small assets from a local HTTP/2 server with TLS (Caddy: `caddy file-server --listen :8443` gets automatic local HTTPS; or `bun`/`node` with `http2.createSecureServer` and a self-signed cert from `mkcert`). In DevTools show Protocol `h2`, one connection ID (enable "Connection ID" column), parallel streams. Then force HTTP/1.1 (`--http1.1` on the server) and show 6-way queueing.
D5. HTTP/3: hit `https://cloudflare-quic.com/` or a CDN site; confirm Protocol `h3` in DevTools and `alt-svc` in the first response; explain how the browser learned it.
D6. TLS inspection: write down for `example.com` the TLS version, cipher, ALPN, certificate issuer, expiry, SANs from `openssl s_client`. Then break a cert (expired badssl.com subdomain `https://expired.badssl.com/`) and read the browser and curl errors.
D7. Idle timeout lab: put Caddy in front of your Hono server with an upstream idle timeout of 2s; hold a keep-alive connection for 3s; observe the 502 or reset on reuse. Explain why load balancers need idle timeouts above the app's keep-alive timeout.
D8. HSTS: serve an HSTS header on a local HTTPS site; visit by `http://` and see the internal 307 redirect; clear it from `chrome://net-internals/#hsts`.

### BREAK
- Run the six-sequential version with `await` in a loop vs `Promise.all` and measure the difference.
- Put a 2 MB response on a 3G-throttled connection and watch slow start (the download rate ramp in the Network waterfall).
- Disable keep-alive on the server (`Connection: close`) and show the extra TCP and TLS handshakes in the timing.
- Serve a certificate for the wrong hostname.

### QUIZ (cold)
1. Latency vs bandwidth: why does a 100ms RTT matter more than a gigabit link for an API?
2. What is the minimum cost, in RTTs, of a first HTTPS request over TLS 1.3 with a cold DNS?
3. What does ALPN do?
4. What does HTTP/2 fix and what doesn't it?
5. Why does HTTP/3 use UDP?
6. What does TLS not protect against?
7. Why do 6 sequential API calls at 100ms RTT cost at least 600ms?
8. What is HSTS and its first-visit weakness?
9. Why must LB idle timeouts exceed the app's keep-alive timeout?
10. Name the Timing tab segments in order.

### GATE
Spoken (5 minutes): narrate a HAR (use a fresh one given cold, or your own): DNS, TCP, TLS, TTFB, download, blocked/queueing; say which bar a given fix (HTTP/2, a CDN, preconnect, removing a redirect) removes.
Pass: quiz 9/10, D1 to D8, tag `gate/M2.5`.

### DELIVERABLE AND FAILURE MODES
Deliverable: `labs/http-lab/transport/`, the 6-call table, annotated HAR.
Faking it: saying "HTTP/2 is faster". The test: name the problem it solves and the one it doesn't.

---

## M2.6 Compression, chunked transfer, SSE, WebSockets (Phase 2 exit)

Hours: 14. Prerequisite: M2.5. Phase: 2.

### WHY
Streaming HTML (M3.4, M8.5) rests on chunked responses. Live features force a transport choice that is a common system design question. Most candidates only know "WebSockets exist".

### ATOMS
Compression
1. Content negotiation: client `Accept-Encoding: gzip, br, zstd`; server picks and sets `Content-Encoding`; add `Vary: Accept-Encoding`.
2. gzip vs brotli vs zstd: brotli beats gzip on text for precompressed static assets (high levels are slow, so dynamic uses low levels); zstd is fast with good ratio (browser support growing).
3. Compression applies to text (HTML, CSS, JS, JSON, SVG). Do not compress already compressed formats (JPEG, PNG, WOFF2, video, gzip).
4. Precompress static assets at build time; compress dynamic responses on the fly at low level.
5. Compression oracle attacks (BREACH/CRIME): secrets reflected in compressed HTTPS responses can leak; know the name and the mitigation (don't compress responses mixing secrets with attacker input, or randomize/mask tokens).
6. Request compression is rare; response compression is standard.
7. `Content-Length` interplay: with compression it describes the compressed body; you cannot know it until you finish compressing, so on-the-fly compression usually means chunked.

Chunked and streaming
8. `Transfer-Encoding: chunked` (HTTP/1.1): body sent as chunks `<hex-size>\r\n<data>\r\n` ending with `0\r\n\r\n`. Used when `Content-Length` is unknown.
9. HTTP/2 and 3 have no chunked encoding; DATA frames serve the same purpose.
10. Streaming response uses: large exports, server-rendered HTML streamed, progress, AI token streams, log tailing.
11. Browser behavior: it parses HTML as it arrives; first paint can happen before the response ends. Intermediaries may buffer (nginx `proxy_buffering`, `X-Accel-Buffering: no`).
12. Backpressure on streams (Web Streams `ReadableStream`, Node streams `pipeline`).
13. Trailers (headers after the body): know they exist.

Real-time transport options
14. Polling: client asks every N seconds. Simplest, stateless, cacheable; wastes requests and adds latency up to N.
15. Long polling: server holds the request until data or timeout; client re-requests. Lower latency; one request per event; holds a connection per client.
16. Server-Sent Events (SSE): `GET` with `Accept: text/event-stream`; response `Content-Type: text/event-stream`, kept open; server-to-client only; text only; automatic reconnection with `Last-Event-ID`; works over plain HTTP, proxies and HTTP/2 (but 6 connections per origin limit on HTTP/1.1).
17. SSE wire format: lines `field: value`; fields `data`, `event`, `id`, `retry`; a blank line ends an event; lines beginning with `:` are comments (heartbeats keep proxies alive).
18. `EventSource` API: `onmessage`, `addEventListener('type')`, `readyState`, `close()`, auto-reconnect after `retry` ms, no custom headers (use cookies or query param tokens), no request body.
19. WebSockets: HTTP/1.1 `Upgrade: websocket` handshake (101 Switching Protocols, `Sec-WebSocket-Key`/`Accept`), then a full-duplex message protocol over the same TCP connection; text and binary frames; ping/pong; close codes (1000, 1001, 1006, 1008, 1011).
20. WebSocket costs: stateful connections you must scale (sticky routing or a pub/sub backplane like Redis so a message reaches the instance holding the socket), no automatic reconnection, heartbeats needed, backpressure (`bufferedAmount`), per-connection memory, load balancer idle timeouts.
21. WebSocket security: CORS does not apply; validate `Origin` on upgrade (Cross-Site WebSocket Hijacking), authenticate (cookie on the upgrade request, or a short-lived ticket), authorize every message, limit message size and rate.
22. WebTransport and HTTP/2 streaming (fetch with streams): know they exist.
23. Decision rule: client never needs to push on the same channel: SSE (or polling if staleness of seconds is OK). Need low-latency bidirectional (chat, collaboration, games): WebSockets. Rare updates: polling is appropriately boring. Mobile push is separate (APNs/FCM).
24. Reconnection strategy: exponential backoff with jitter; resync state after reconnect (snapshot plus deltas; event ids); the "thundering herd" problem on server restart.
25. Delivery semantics: at-most-once by default; at-least-once needs ids and client dedupe; ordering and missed events.

### RESOURCES (in order)
1. MDN, "HTTP compression": https://developer.mozilla.org/en-US/docs/Web/HTTP/Guides/Compression . `Content-Encoding` and `Accept-Encoding` header pages.
2. MDN, `Transfer-Encoding`: https://developer.mozilla.org/en-US/docs/Web/HTTP/Reference/Headers/Transfer-Encoding . RFC 9112 section 7.1 (Chunked Transfer Coding): https://www.rfc-editor.org/rfc/rfc9112.html#name-chunked-transfer-coding .
3. web.dev, "Use text compression" (search "Enable text compression" article) and the Brotli announcement from Google: https://github.com/google/brotli .
4. MDN, Server-sent events: https://developer.mozilla.org/en-US/docs/Web/API/Server-sent_events/Using_server-sent_events . HTML Living Standard, section 9.2 "Server-sent events" (the wire format): https://html.spec.whatwg.org/multipage/server-sent-events.html .
5. MDN, WebSockets API and "Writing WebSocket servers": https://developer.mozilla.org/en-US/docs/Web/API/WebSockets_API . RFC 6455 sections 1 to 5 (introduction, handshake, base framing): https://www.rfc-editor.org/rfc/rfc6455 . Skim: 5.5 (control frames), 7 (closing).
6. Hono docs: Streaming helper (`stream`, `streamText`, `streamSSE`) and WebSocket helper: https://hono.dev/docs/helpers/streaming .
7. "BREACH" attack summary: https://www.breachattack.com/ . One page.
8. System-design lens: Ably's or Phil Leggetter's "WebSockets vs SSE vs long polling" posts, read one (to see how practitioners compare) and write your own summary afterward.

### START HERE (first 30 minutes)
1. `curl -sI -H 'Accept-Encoding: gzip, br' https://developer.mozilla.org/` and note `content-encoding`.
2. `curl --raw -v https://httpbin.org/stream/3` and see the chunk sizes (the hex lines) of a chunked response.
3. Write the 12-line Hono SSE endpoint that sends `data: tick\n\n` every second and `curl -N localhost:3000/events`.

### DRILLS
D1. Compression: serve a 200 KB JSON and a 200 KB JPEG; return gzip when `Accept-Encoding` includes it, brotli if offered, identity otherwise, with `Vary: Accept-Encoding`. Acceptance: a table of sizes and CPU time (use `console.time`) for gzip -6, brotli -4, brotli -11, identity; JPEG is not compressed.
D2. Chunked export: stream a 100,000-row JSON array (`NDJSON` or a JSON array written incrementally) using `Transfer-Encoding: chunked` with no `Content-Length`; memory stays flat while the export runs (check `process.memoryUsage()`). Confirm with `curl -v` that no `Content-Length` appears and `curl --raw` shows chunk sizes.
D3. Streamed HTML: first chunk `<h1>Loading</h1>`, one second later the list; observe in the browser that the heading paints before the list (Performance panel, filmstrip). Then put a buffering proxy in front (nginx default) and show the stream collapses to one paint; fix with `X-Accel-Buffering: no`.
D4. SSE endpoint: `GET /events` sends a "booking confirmed" event every 5 seconds with `id:`, `event:`, `data:`; a heartbeat comment every 15s; consume with `EventSource` in a plain HTML page. Kill the server, watch auto-reconnect with `Last-Event-ID`, and make the server replay missed events from an in-memory ring buffer.
D5. WebSocket server: the same feature with Hono's WS helper or `ws`. Implement: origin check, auth via cookie on upgrade, ping/pong heartbeat every 20s, close on missed pong, message size limit, per-connection rate limit. Client: auto-reconnect with exponential backoff and jitter. Acceptance: kill the server; the client reconnects within bounds; the client's resync fetches the missed events.
D6. Polling and long polling: implement both for the same feature; measure latency to deliver an event (median/p95), requests per minute, and server connections held, for 1, 100, 1,000 simulated clients (use a loop script or `autocannon`).
D7. Comparison write-up: a table with rows (direction, protocol, proxies, reconnection, auth, scaling, cost per idle client, browser connection limits) and columns (polling, long polling, SSE, WebSockets). Fill it from your own measurements, not a blog.
D8. Cross-Site WebSocket Hijacking: a page on `evil.test` opens `new WebSocket('ws://shop.test:3000/ws')`; cookies are sent (SameSite=None) or not (Lax). Show the server must check `Origin`; add the check; prove the attack fails.
D9. Scaling thought experiment: two server instances behind a load balancer; a message arrives for a user connected to the other instance. Draw the architecture with a Redis pub/sub or Postgres `LISTEN/NOTIFY` backplane; implement the Postgres `LISTEN/NOTIFY` version for two local server processes (this one uses Postgres, so do it after M4.1 and come back; mark it deferred in the tracker).

### BREAK
- Kill the SSE server mid-stream and watch reconnect; do the same with WebSockets and note there is none by default.
- Turn off compression and compare a realistic 50 KB+ JSON payload before and after brotli.
- Send a message larger than the limit; observe the close code.
- Let a WebSocket idle past a proxy timeout (Caddy `transport http { read_timeout }`) and see the silent drop; then fix with heartbeats.
- Open 7 SSE connections on HTTP/1.1 from one browser; observe the 6-connection cap blocking the 7th (and not on HTTP/2).

### QUIZ (cold)
1. How does a client negotiate compression and what must the server add?
2. What does `Transfer-Encoding: chunked` do on the wire and when is it needed?
3. Why can streamed HTML paint before the response ends, and what breaks it?
4. SSE wire format: which fields, what ends an event?
5. What does `EventSource` give you for free that `WebSocket` does not?
6. What must a production WebSocket server implement beyond "it connects"?
7. Why does CORS not protect WebSockets and what does?
8. When do you choose polling over WebSockets?
9. How does a message reach a user connected to a different server instance?
10. What is BREACH?

### PHASE 2 EXIT TEST (cold, one sitting, 25 minutes)
1. Read a given HAR cold and narrate every segment.
2. Write cache headers for 4 resource shapes with no hesitation.
3. Tell CORS + cookies + CSRF as one story.
4. Pick and justify a transport for: "live count of confirmed bookings on a dashboard" and "collaborative editor".
5. Explain what `Transfer-Encoding: chunked` is for without saying "streaming" as the mechanism.
6. Explain 301/302/307/308, safe/idempotent, 401/403/404, and 502/503/504 in 90 seconds each.
Failing any of these means repeating the specific modules.

### GATE
Spoken (4 minutes): for each of the two features, name the transport, the failure modes, the reconnection plan and the scaling plan.
Pass: quiz 9/10, D1 to D8 (D9 deferred is allowed once), Phase 2 exit passed, tags `gate/M2.6` and `gate/phase2`.

### DELIVERABLE AND FAILURE MODES
Deliverable: `labs/http-lab` with `scripts/curl.sh` demonstrating 304, CORS preflight, HttpOnly cookie, chunked response, SSE and the WS demo; the transport comparison table; NOTES.
Faking it: using a WebSocket library without implementing heartbeats and reconnect. Those are the module.


---

# PHASE 3. BROWSER AND PERFORMANCE

Repo: `labs/perf-lab`. Static HTML, CSS and JS first. No React until the last drill of M3.4, and only to measure hydration cost. Measure everything with numbers.

Standard measurement protocol (use in every drill in this phase):
- Chrome, a clean profile (or Incognito), extensions disabled.
- DevTools: Network "Disable cache" ON, throttling "Slow 4G" (or Fast 3G where stated), CPU throttling 4x for INP drills.
- Run each measurement 5 times, record the median.
- Serve locally from a server that adds an artificial 100ms TTFB when stated.
- Save HAR and Performance trace in `evidence/`.

---

## M3.1 Critical rendering path

Hours: 14. Prerequisite: M2.6. Phase: 3.

### WHY
Bytes become pixels through a pipeline with blocking steps. Most "performance" wins are removing something from the blocking path, not adding a tool.

### ATOMS
Pipeline
1. Navigation: URL typed, DNS, connect, TLS, request; the response bytes begin to arrive (TTFB).
2. The network thread streams bytes to the renderer. The HTML parser tokenizes bytes into the DOM incrementally.
3. The preload scanner (speculative parser) scans ahead for `<script src>`, `<link>`, `<img>` and starts fetching before the main parser reaches them.
4. CSS bytes become the CSSOM. DOM + CSSOM produce the render tree (only visible nodes; `display:none` excluded, `visibility:hidden` included).
5. Layout (reflow): compute geometry. Paint: produce draw records. Compositing: assemble layers (often on the GPU, with transform/opacity animations running on the compositor).
6. Style recalculation, layout, paint are repeated on changes; layout thrashing (reading layout properties after writing styles in a loop) forces synchronous layout.

Blocking behavior
7. CSS is render-blocking: no paint until the CSSOM of render-blocking stylesheets is built. Media queries make `<link media>` non-blocking when they don't match.
8. A classic `<script>` blocks the HTML parser until it downloads and executes; and it waits for CSS pending before it (scripts can read computed styles).
9. `defer`: download in parallel, execute in order after the document is parsed, before `DOMContentLoaded`.
10. `async`: download in parallel, execute as soon as ready, order not guaranteed, may block the parser during execution.
11. `type="module"`: deferred by default; `async` can be added. `nomodule` is legacy.
12. `DOMContentLoaded` vs `load` events.
13. `document.write()` can block the parser and defeat the preload scanner; avoid it.
14. Fonts: text with a pending web font is either invisible (FOIT), swapped (FOUT) or never swapped, depending on `font-display` (M3.2). Fonts only start downloading when the browser discovers they are used (after CSSOM and render tree), so they are late.
15. Images: `<img>` with `width`/`height` attributes reserve space (aspect ratio) and avoid layout shift; `srcset`/`sizes` select an appropriate candidate; `loading="lazy"` defers offscreen images; `fetchpriority="high"` raises priority for the LCP image; `decoding="async"`.

Resource hints
16. `preload`: fetch this resource now at high priority for this page; needs `as=` and `crossorigin` for fonts. Preloading the wrong thing steals bandwidth.
17. `prefetch`: low-priority fetch for a future navigation.
18. `preconnect`: do DNS+TCP+TLS for an origin early; `dns-prefetch` just DNS.
19. `modulepreload` for ES module graphs.
20. `103 Early Hints`: send hints before the final response is ready (CDNs support it).
21. `fetchpriority` and the browser's priority heuristics (High for CSS, Low for async images etc.; visible in the DevTools Priority column).

Critical path reasoning
22. Critical resources, critical path length (number of round trips), critical bytes. Reducing any of the three speeds first paint.
23. Critical CSS: inline the CSS needed for above-the-fold content; load the rest asynchronously. Trade-off: HTML size and caching (inlined CSS can't be cached separately).
24. Request chains: HTML to CSS to font; CSS `@import` creates chains; JS-injected resources are discovered late.
25. Third-party scripts: a single blocking third-party script in `<head>` can stall the whole page; `async`/`defer`, self-hosting, facades.
26. Redirect chains and their cost (each redirect is a round trip).
27. Server timing: a 1s TTFB moves everything; critical path starts at response arrival.

### RESOURCES (in order)
1. Mariko Kosaka, "Inside look at modern web browsers" (4 parts): https://developer.chrome.com/blog/inside-browser-part1 , part2, part3, part4. Read all four, drawing the diagram of processes and the render pipeline.
2. web.dev, "Critical rendering path" (and its sub articles): https://web.dev/learn/performance/understanding-the-critical-path and https://web.dev/articles/critical-rendering-path .
3. web.dev, "Learn Performance" course: https://web.dev/learn/performance . In this module do: "Introduction", "Understanding the critical path", "Resource loading", "Optimize resource loading" modules. Continue the rest through M3.4.
4. MDN, "Populating the page: how browsers work": https://developer.mozilla.org/en-US/docs/Web/Performance/Guides/How_browsers_work . MDN "Critical rendering path".
5. HTML Living Standard, "Scripting" section on `defer`/`async` with the timeline diagram: https://html.spec.whatwg.org/multipage/scripting.html#attr-script-async .
6. Philip Walton, "Preload, Prefetch and Priorities": https://web.dev/articles/preload-critical-assets . And Addy Osmani "Preload, Prefetch And Priorities in Chrome" for the priority table.
7. web.dev, "Optimize resource loading with the Fetch Priority API": https://web.dev/articles/fetch-priority .
8. Surma and Jake, "HTTP 203" episodes on loading (any two): https://www.youtube.com/playlist?list=PLNYkxOF6rcICgS7eFJrGdlsIGbVxnr1tl .

### START HERE (first 30 minutes)
1. Create `labs/perf-lab/bad.html` with: a `<link rel=stylesheet href="big.css">` where `big.css` is served with a 2 second artificial delay; a synchronous `<script src="slow.js">` with a 1 second delay in `<head>`; an `<img src="huge.jpg">` of 3 MB with no dimensions; text with a Google Font.
2. Open it with DevTools Performance recording, Slow 4G. Watch the filmstrip. Note the first paint time.
3. Add a one-line explanation of what each of the 3 delays did to first paint.

### DRILLS
D1. `bad.html` as above and `good.html` with the same content. Allowed fixes in `good.html` are limited to HTML and network headers (no framework): `defer` the script, preload the LCP image, add width/height, inline critical CSS (and `media` for non-critical), compress and resize the image (AVIF/WebP + `srcset`), `font-display`, preconnect to the font origin, compression and caching headers. Acceptance: a table with TTFB, FCP, LCP, CLS, total JS bytes, total transfer bytes for both, and which single change moved LCP the most (do each fix independently: revert one at a time).
D2. Script loading matrix: five pages with a 1s-delay script loaded as plain, `defer`, `async`, `module`, `async module`. For each, record `DOMContentLoaded`, `load`, and first-paint times and whether the parser blocked (print `performance.now()` in inline scripts before and after the tag). Acceptance: a 5-row table that matches atoms 8 to 11.
D3. CSS blocking: a page with an inline script that reads `getComputedStyle` after a slow stylesheet; show the script waits for the CSS. Then use `media="print"` swap (or `media` queries) to unblock and compare.
D4. Preload scanner: put an `<img>` after a blocking script in HTML; show in the Network panel the image starts downloading before the script finishes (the preload scanner). Then hide it from the scanner (set `src` via JS after the script) and show the late discovery cost.
D5. Request chain: `@import` three CSS files (a chain) vs three `<link>` tags; compare waterfalls. Then add `font` discovered only via CSS and fix with `preload`. Draw both waterfalls annotated.
D6. Hints: use `preconnect` for a third origin, `preload` for a font, `prefetch` for the next page, `fetchpriority=high` on the hero image, `fetchpriority=low` on a carousel image; verify each in the Priority column of the Network panel.
D7. Redirect chain: `http://` to `https://` to `www` to `/home` (4 hops); measure; collapse to 1 hop; HSTS preload concept.
D8. Layout thrashing: a loop that alternates writing `el.style.height` and reading `el.offsetHeight` over 1,000 elements; profile; fix by batching reads then writes. Record the Performance panel's "Forced reflow" warning.
D9. Early Hints: serve `103 Early Hints` with `Link: </main.css>; rel=preload; as=style` from a Caddy or Cloudflare setup (or a hand-written `node:http` `writeEarlyHints`); show the CSS starts before the HTML TTFB.

### BREAK
- Add `loading="lazy"` to the LCP image; watch LCP regress; remove it.
- Preload an image that is never used; read the console warning and measure the wasted bandwidth.
- Make a blocking third-party script in `<head>` fail (blackhole it with a 30s delay); watch the whole page stall; fix with `async`/self-host.
- Put `defer` on a script that another inline script needs synchronously; read the error.
- Use `@import` for the main CSS in a `<style>` block and chart the cost.

### QUIZ (cold)
1. Draw the pipeline from bytes to pixels.
2. What blocks first paint: CSS or JS and why?
3. `defer` vs `async` vs `module`: execution time and order.
4. What does the preload scanner do and what defeats it?
5. `preload` vs `prefetch` vs `preconnect`.
6. Why is the LCP image never `lazy`?
7. What's critical CSS and its trade-off?
8. Why can 2KB of inline CSS beat optimizing a 200KB image below the fold?
9. What is layout thrashing?
10. Why do request chains hurt and how do you flatten them?

### GATE
Spoken (4 minutes): narrate HTML to DOM, CSS to CSSOM, render tree, layout, paint, composite; point at exactly where a synchronous script sits and what it blocks; explain the `bad.html` to `good.html` improvement lever by lever with numbers.
Pass: quiz 9/10, D1 to D9, `bad.html`/`good.html` table, tag `gate/M3.1`.

### DELIVERABLE AND FAILURE MODES
Deliverable: `labs/perf-lab/{bad,good}.html`, tables, traces.
Faking it: applying "best practices" blindly. The rule: each fix has a measured before/after in isolation.

---

## M3.2 Core Web Vitals, fonts, images

Hours: 14. Prerequisite: M3.1. Phase: 3.

### WHY
LCP, INP, CLS are what users feel and what search ranks. Each has parts. Knowing which part is slow decides the fix.

### ATOMS
LCP
1. Largest Contentful Paint: render time of the largest image, text block or video poster in the viewport. Good is at most 2.5s at the 75th percentile (field).
2. LCP is the sum of four sub-parts: TTFB, resource load delay, resource load duration, element render delay. Each has a different fix.
3. Candidates: `<img>`, `<image>` in SVG, `<video>` poster, background image via `url()`, block-level text. Elements removed or lazy-loaded can become LCP.
4. Typical fixes: lower TTFB (cache, CDN, DB, avoid redirects), discover the LCP resource earlier (no lazy, preload, `fetchpriority`, no JS injection), shrink it (AVIF/WebP, `srcset`), remove render delay (no blocking CSS/JS, don't hide behind client rendering).

INP
5. Interaction to Next Paint: latency from a user interaction (click, tap, key) to the next frame, reported as roughly the worst interaction. Good is at most 200ms at the 75th percentile.
6. Three parts: input delay (main thread busy), processing time (your handlers), presentation delay (rendering after handlers).
7. Replaces FID (which measured only the first interaction's input delay).
8. Typical fixes: break up long tasks (`scheduler.yield`, `setTimeout`), less work in handlers, `requestAnimationFrame`/transitions for visual feedback first, avoid large DOM and layout thrash, debounce, move work to workers, avoid hydration blocking.

CLS
9. Cumulative Layout Shift: sum of layout shift scores = impact fraction x distance fraction, grouped in session windows; good is at most 0.1.
10. Causes: images/iframes/ads without dimensions, late-injected content, web fonts swapping, animations that change layout properties.
11. Fixes: width/height or `aspect-ratio`, reserve space, font metric overrides, transform animations, avoid inserting above existing content.

Other metrics
12. TTFB (diagnostic, not core), FCP (first content), TBT (lab proxy for INP), Speed Index. Lab TBT correlates with field INP.
13. Lab data (Lighthouse, DevTools, WebPageTest): synthetic, reproducible, good for debugging causes. Field data (CrUX, RUM via `web-vitals` library): real users, real devices and networks, the truth about your audience. 75th percentile is the target statistic. They disagree frequently.
14. Origin vs URL level CrUX data; the PageSpeed Insights page shows both.
15. Collecting RUM yourself: `web-vitals` library, `navigator.sendBeacon` to an endpoint, attribution build.

Fonts
16. Font loading: the browser discovers fonts after CSSOM and layout; block period then swap period. `font-display`: `auto`, `block` (invisible up to ~3s), `swap` (fallback immediately, swap when ready), `fallback` (short block, short swap), `optional` (tiny block, may never swap).
17. FOIT vs FOUT. Swap causes shifts if metrics differ.
18. Metric-compatible fallback with `size-adjust`, `ascent-override`, `descent-override`, `line-gap-override` in a fallback `@font-face` (Next's `next/font` automates this).
19. Subset fonts (unicode-range), WOFF2 only, self-host (avoid extra connection and cross-origin font CORS requirement), preload the one critical font with `crossorigin`.
20. Variable fonts: one file for many weights; size trade-offs.

Images
21. Formats: AVIF (best compression), WebP, JPEG, PNG, SVG (vectors), GIF (avoid; use video). Choose by content.
22. Responsive images: `srcset` with width descriptors, `sizes`, `<picture>` for formats and art direction; DPR.
23. Dimensions, `aspect-ratio`, `object-fit`; lazy loading for below-the-fold; `decoding=async`; LQIP/blur placeholders.
24. Image CDN/transform URLs; cache headers; avoiding giant originals.

Third parties and JS cost
25. Third-party scripts (analytics, tag managers, chat widgets) compete for the main thread and network; find them in the waterfall and the Performance "Bottom-Up grouped by domain"; facades; delay until interaction; `partytown` concept.
26. JS cost = download + parse + compile + execute, all on the main thread and slower on low-end phones. Use CPU 4x/6x throttling.

### RESOURCES (in order)
1. web.dev, "Core Web Vitals" hub: https://web.dev/explore/learn-core-web-vitals . Read: LCP, INP, CLS articles and the "Metrics thresholds" page.
2. web.dev, "Optimize Largest Contentful Paint": https://web.dev/articles/optimize-lcp (read the four sub-part breakdown carefully), "Optimize INP": https://web.dev/articles/optimize-inp , "Optimize CLS": https://web.dev/articles/optimize-cls .
3. web.dev "Learn Performance" continue: Images, Fonts, JavaScript, Third parties, Measurement modules: https://web.dev/learn/performance .
4. web.dev, "Best practices for fonts" and "Optimize web fonts": https://web.dev/articles/font-best-practices ; Chrome's fallback font metrics article (`size-adjust`): https://developer.chrome.com/blog/font-fallbacks .
5. web.dev, "Responsive images": https://web.dev/learn/images .
6. `web-vitals` library repo README: https://github.com/GoogleChrome/web-vitals .
7. Chrome UX Report docs and PageSpeed Insights: https://developer.chrome.com/docs/crux .
8. Harry Roberts (CSS Wizardry), "The Fastest Site in the World" or "Performance budgets" posts: https://csswizardry.com/ . Read one.

### START HERE (first 30 minutes)
1. Pick `good.html` from M3.1 and a real site you built. Run Lighthouse on both (desktop and mobile). Identify the LCP element for each (Lighthouse "LCP element" audit or DevTools Performance > Timings).
2. In DevTools Performance, record load; open the "LCP by phase" insight (or compute the four sub-parts by hand from the network and trace).

### DRILLS
D1. LCP decomposition: for the real site, compute TTFB, load delay, load duration, render delay in ms. Name the largest. Make a fix aimed at only that part. Acceptance: at least 30 percent LCP improvement (median of 5 runs) with the part named and numbers before/after.
D2. Four fixes, four parts: build a test page where you can toggle each LCP sub-part's problem (slow TTFB via server delay, late discovery via JS-injected image, a 3MB image, a render-blocking stylesheet). Fix each one independently and chart how each moved LCP.
D3. INP lab: a page with a click handler that sorts 300,000 numbers synchronously and a second one with a 50ms busy wait and one with layout thrash. With 4x CPU throttling, record each interaction, compute input delay / processing / presentation from the trace, then fix: yield, chunk with `scheduler.yield`, move to a worker. Acceptance: INP-equivalent under 200ms for each after the fix.
D4. CLS lab: reproduce each CLS cause: image without dimensions, ad slot inserted late, web font swap, banner pushing content, animating `top` vs `transform`. Measure with the Layout Shift track and the "layout shifts" Rendering overlay; compute scores using the `PerformanceObserver('layout-shift')` API; fix each; final CLS under 0.05.
D5. Font lab: a Google Font loaded via CSS `@import` with no `font-display`. Measure CLS and FCP under Slow 4G. Then fix: self-host WOFF2 subset, `preconnect` removed (self-hosted), `preload` the critical weight, `font-display: swap` with a metric-matched fallback (`size-adjust` etc.) and compare with `optional`. Acceptance: CLS contribution of the font under 0.01.
D6. Image lab: one 4000px JPEG hero. Produce AVIF, WebP, JPEG at 400/800/1600 widths; use `<picture>` + `srcset` + `sizes`; verify which one the browser picks at different viewport widths and DPR (DevTools device toolbar). Record bytes before/after.
D7. RUM: add `web-vitals` to a page and `sendBeacon` LCP/INP/CLS with attribution to a `POST /rum` endpoint in Hono; store rows in a JSON-lines file; write a script that prints p50/p75/p95 for each metric from the collected rows. Open the page from three different throttling profiles to generate data.
D8. Lab vs field disagreement: find a concrete example where Lighthouse and CrUX/RUM disagree for a page of yours (or a public page in PSI); explain in NOTES at least three plausible reasons (device mix, network, cache warmth, interactions, geography).
D9. Third parties: add a fake "analytics" script (50ms of CPU on load plus a 300 KB download) and a fake chat widget; show their impact on LCP, TBT and INP; then mitigate with `async`, delaying until idle/interaction, and a facade.

### BREAK
- Lazy-load the LCP image and measure the regression.
- Put the LCP image in a CSS background set by JS; measure.
- Use `font-display: block` on a slow font and show invisible text; compute the time to visible text.
- Remove image dimensions and show CLS rise.
- Add 4x CPU throttle and show an interaction that was fine at 1x become poor.

### QUIZ (cold)
1. Define LCP, INP, CLS by what each measures, with good thresholds.
2. The four parts of LCP and a fix for each.
3. The three parts of INP.
4. Why does the 75th percentile matter?
5. Why can lab and field disagree? Give three reasons.
6. `font-display` values: behavior differences.
7. How do metric-override fallbacks remove CLS?
8. `srcset` vs `sizes` vs `<picture>`.
9. How do third-party scripts hurt INP and LCP?
10. How would you collect RUM yourself?

### GATE
Spoken (5 minutes): define the three metrics by what they measure, decompose LCP for your site with numbers, and give one concrete lab/field disagreement.
Pass: quiz 9/10, D1 to D9, LCP improved at least 30 percent with lever named, tag `gate/M3.2`.

### DELIVERABLE AND FAILURE MODES
Deliverable: `labs/perf-lab/` with the LCP decomposition write-up, INP/CLS labs, RUM collector.
Faking it: quoting Lighthouse scores. The deliverable is the decomposition, not the score.

---

## M3.3 DevTools fluency

Hours: 8. Prerequisite: M3.2. Phase: 3. Deliberately boring.

### WHY
You will use these tools weekly for years. Muscle memory prevents you from being a tourist.

### ATOMS
Network
1. Columns: Name, Status, Type, Initiator, Size (transfer vs resource), Time, Waterfall, Priority, Protocol, Connection ID, Remote Address, Set-Cookie, Domain.
2. Filters: by type, by `larger-than:`, `domain:`, `-domain:`, `status-code:`, `has-response-header:`, `mime-type:`.
3. Controls: Preserve log, Disable cache, Throttling presets and custom profiles, Offline, Block request URL/domain, Override content (local overrides), Replay XHR, Copy as cURL/fetch/HAR.
4. Request detail tabs: Headers (general, response, request), Payload, Preview, Response, Initiator (call stack), Timing, Cookies.
5. Reading a waterfall: gaps (queueing, stalled), long TTFB, chains via Initiator.
6. HAR: export, sanitization (it contains cookies and tokens), import into other tools, WebPageTest.
7. Network conditions panel: user-agent override, cache disable; Request blocking pane; Network log settings.

Performance
8. Recording: reload recording, interaction recording, CPU throttling, network throttling, "Screenshots", "Web Vitals" track, "Memory".
9. Tracks: Main thread flame chart, Frames, Interactions, Timings (FCP, LCP, DCL, Load), Network track, Layout Shifts.
10. Views: Summary, Bottom-Up (self time by activity), Call Tree, Event Log. Self time vs total time. Grouping by domain/URL.
11. Identifying: long tasks (red corner), forced reflow, expensive style recalcs, layout, paint, GC.
12. Performance Insights / Lighthouse panel: lab audits; limitations (one synthetic run, no field).

Other panels
13. Coverage panel: unused JS/CSS percentage; use for exploration, not a KPI.
14. Application: Cookies, Local/Session storage, IndexedDB, Cache storage, Service workers, Back/forward cache test, Manifest.
15. Console: filtering, `$0`, `$_`, `copy()`, `monitor()`, `getEventListeners`, `console.table/time/group`; reading CORS and mixed content errors literally.
16. Sources: breakpoints (line, conditional, logpoint, DOM change, XHR/fetch, event listener), blackboxing, snippets, workspaces, source maps.
17. Rendering pane: Paint flashing, Layout Shift Regions, Core Web Vitals overlay, emulate CSS media features, Frame rendering stats.
18. Memory: heap snapshot, allocation timeline, detached DOM nodes.
19. Elements: computed styles, force state, layout badges, event listeners tab.
20. Lighthouse: modes (navigation, timespan, snapshot), what it does and does not prove.
21. Issues tab: grouping by cookies, CORS, mixed content, deprecations.
22. Remote debugging, Chrome flags, `chrome://net-internals`, `chrome://inspect`.

### RESOURCES (in order)
1. Chrome DevTools docs: Network reference https://developer.chrome.com/docs/devtools/network/reference ; Performance reference https://developer.chrome.com/docs/devtools/performance/reference ; Coverage https://developer.chrome.com/docs/devtools/coverage ; Rendering https://developer.chrome.com/docs/devtools/rendering ; Memory https://developer.chrome.com/docs/devtools/memory-problems ; Sources/Debug JS https://developer.chrome.com/docs/devtools/javascript .
2. "Chrome DevTools Tips" video series by the Chrome team (pick any 6 you do not already know): https://www.youtube.com/@ChromeDevs .
3. Lighthouse docs: https://developer.chrome.com/docs/lighthouse/overview .
4. WebPageTest documentation and one run on your site: https://docs.webpagetest.org/ .
5. Firefox DevTools Network and Performance (a second tool for the same task): https://firefox-source-docs.mozilla.org/devtools-user/ .

### START HERE (first 30 minutes)
1. Open DevTools on a site, set it as a "docked right" layout you will keep.
2. Use only the keyboard: `Ctrl+Shift+P` command menu to open Network, Performance, Coverage, Rendering, Application. Do it 5 times without the mouse.
3. Record the filter syntax list from the Network docs in a file; type each filter once.

### DRILLS
D1. For `bad.html` and `good.html`: export HARs; annotate 5 specific entries in NOTES.md referencing exact waterfall rows (initiator, priority, blocked time).
D2. Record a Performance trace of a single click and a page load. Screenshot the flame chart. Circle and name the long task (function and file) and its self time.
D3. Coverage: report unused JS and CSS percentage on a real page; for the top file explain why unused (not what to delete).
D4. Application panel: find the session cookie from M2.2, confirm every attribute matches intent; clear one cookie and watch the session end; test back/forward cache eligibility with the BFCache test and fix one blocker.
D5. Console puzzle: with only the Console and Sources panels, find which listener fires on a button click in an unknown page (use `getEventListeners`, event-listener breakpoints, call stack) and edit a value with a conditional breakpoint.
D6. Local overrides: override a slow third-party script with an empty file to measure its cost; override response headers to test a cache header change without redeploying.
D7. Request blocking: block a CSS file and a font; record what breaks and what the layout becomes; add what it shows about fallbacks.
D8. Memory: take 3 heap snapshots around an action repeated 10 times; compare to find a leak; find detached DOM nodes.
D9. Firefox and WebKit: reproduce one measurement in Firefox DevTools and note two differences.
D10. WebPageTest: run a test with filmstrip and waterfall on your deployed page (after deploy later) or `good.html` hosted publicly; compare with your local measurement.

### BREAK
- Throttle CPU to 6x and watch which of your interactions turn red.
- Disable cache and enable cache and compare repeat view waterfalls.
- Use "Offline" to check what your page does with no network.
- Make a bug that only appears in a hard reload (cache) and find it with the Network tab.

### QUIZ (cold)
1. What is the difference between Size transferred and resource size?
2. What does Initiator tell you?
3. How do you find the cause of a long task in the flame chart?
4. Self time vs total time?
5. What can a HAR leak?
6. What does Lighthouse not prove?
7. What does the Coverage panel measure and how should you use it?
8. How do you set a logpoint?
9. How do you test a cache header change without redeploying?
10. What are the three heap snapshot comparison uses?

### GATE
Spoken (5 minutes): given a HAR handed to you cold (have someone, or an AI, give you an unseen one from a public site), narrate: DNS, TCP, TLS, TTFB, download, blocked/queueing, which entry is the LCP resource, and where the biggest waterfall gap is.
Pass: quiz 9/10, D1 to D10, tag `gate/M3.3`.

### DELIVERABLE AND FAILURE MODES
Deliverable: `evidence/` with annotated HARs and traces; a one-page cheat sheet of the filters and shortcuts you use.
Faking it: opening DevTools only when something is broken. Rule: for the rest of the curriculum DevTools stays open while you build.

---

## M3.4 Hydration, streaming, JS cost (Phase 3 exit)

Hours: 12. Prerequisite: M3.3. Phase: 3.

### WHY
SSR, hydration and streaming are the core of the rendering debate. If you do not understand their costs mechanically, "server components" will be vocabulary.

### ATOMS
1. CSR: the server sends an almost empty HTML shell; JS downloads, runs, fetches data, renders. First paint of real content is after at least: HTML + JS + API data (a request chain).
2. SSR: the server runs the UI code and sends HTML with content; the browser can paint it before JS loads. The cost: TTFB now includes render time and data fetching.
3. Hydration: the client UI runtime re-runs the component tree, reuses the existing server DOM, attaches event listeners, and rebuilds its internal state. Until then, the page looks interactive but isn't ("uncanny valley").
4. Hydration cost = download + parse + compile + execute + reconcile. The HTML also exists in the payload twice (as HTML and as serialized props/state).
5. Hydration mismatch: the client render output differs from the server HTML (`Date.now()`, `Math.random()`, `toLocaleString`, `typeof window`, user-agent branches, invalid HTML nesting like `<div>` inside `<p>`, browser extensions modifying DOM). React warns or rebuilds the subtree.
6. SSR can hurt LCP if TTFB is high: nothing paints until the first HTML bytes arrive; you moved the data-fetch waterfall from the browser to the server and added render time.
7. SSR can help: HTML is discoverable by the preload scanner (images, CSS) and content arrives in the first response, no JS needed to see it; and SEO/social previews.
8. Streaming HTML: the server flushes the shell immediately and later flushes remaining chunks as data resolves; the browser paints progressively. Needs `Transfer-Encoding: chunked` / HTTP/2 DATA frames and proxies that do not buffer.
9. Out-of-order streaming (React Suspense): placeholders sent first, later `<template>` + inline script chunks replace them in place; no client JS bundle is needed for content to appear, though JS is needed to hydrate.
10. Selective hydration: hydrate boundaries independently, prioritizing the one the user interacts with.
11. Islands: ship JS only for interactive components; static content has no hydration cost (Astro-like model); React Server Components achieves a similar effect via a different mechanism (M8.3).
12. Resumability (Qwik): serialize listeners to HTML to avoid replaying; know the idea.
13. JS cost recap: bytes over the wire are not bytes of CPU; compressed JS decompresses and parses; low-end phones are 4 to 10 times slower than a dev laptop.
14. Caching of SSR output: HTML cached at a CDN for public pages (the data is shared), not for personalized pages.
15. What to measure: TTFB, FCP, LCP, TBT/INP, JS bytes, hydration time (`performance.mark` around `hydrateRoot`), time-to-interactive proxies.
16. Mixed models: SSR the shell, CSR for private widgets; progressive enhancement (works without JS).

### RESOURCES (in order)
1. web.dev, "Rendering on the Web": https://web.dev/articles/rendering-on-the-web . Full article (the table of trade-offs).
2. React docs: `hydrateRoot`: https://react.dev/reference/react-dom/client/hydrateRoot ; "Hydration mismatch" errors: https://react.dev/link/hydration-mismatch ; `renderToPipeableStream`: https://react.dev/reference/react-dom/server/renderToPipeableStream ; `<Suspense>`: https://react.dev/reference/react/Suspense .
3. Dan Abramov, "The two Reacts" and "Making sense of React Server Components" series (skim now; deeper in M8.3): https://overreacted.io/ .
4. Addy Osmani and Jason Miller, "Rendering on the Web" (earlier version) as a second view; Jason Miller's "Islands architecture": https://jasonformat.com/islands-architecture/ .
5. Ryan Carniato, "Why Efficient Hydration in JavaScript Frameworks is so Challenging": https://dev.to/this-is-learning/why-efficient-hydration-in-javascript-frameworks-is-so-challenging-1ca3 .
6. web.dev, "Optimize long tasks" (from M1.4, re-read) and "Hydration" sections of "Learn Performance - JavaScript".
7. Your Hono docs: `c.html`, JSX middleware, streaming helper.

### START HERE (first 30 minutes)
1. In `labs/perf-lab` create `csr/index.html` with an empty `<div id=app>` and a script that fetches `/api/list` (with a 300ms server delay) and renders.
2. Create `ssr/` served by Hono rendering the same list server-side.
3. Run both under Slow 4G; compare the filmstrips.

### DRILLS
D1. CSR vs SSR measurement: for both pages with TTFB 50ms and 800ms (server delays), record TTFB, FCP, LCP, JS bytes. Acceptance: a 2x2 table and one sentence per cell explaining it. You must be able to say when SSR lost.
D2. Streaming: a Hono route that flushes `<head>` and a shell immediately, waits 1s for data, then flushes the list. Observe two visible paints in the filmstrip. Measure FCP vs LCP vs time-to-full. Verify with `curl --raw` the chunk framing.
D3. Hydration with React: render a React list page with `renderToString` on the server plus `hydrateRoot` on the client (a minimal setup with Vite SSR or a hand-built script). Add `performance.mark` around hydration; record hydration duration at 1x and 4x CPU throttling for 100 and 5,000 list items.
D4. Break hydration on purpose: (a) `Date.now()` in render, (b) `new Date().toLocaleString()` with different server/client timezone, (c) `typeof window !== 'undefined'` branch, (d) `<p><div>` invalid nesting, (e) random id. Capture the exact warning for each, the rebuild behavior, and the fix pattern (`useEffect` for client-only, `suppressHydrationWarning` when appropriate, stable ids via `useId`).
D5. Suspense streaming: `renderToPipeableStream` with two Suspense boundaries (200ms and 1500ms data) in Hono or Node; show the HTML arriving as chunks and replacement happening without client JS (disable JS in DevTools and see the content still replaced via inline script... note: the swap scripts are inline; document what remains if JS is disabled).
D6. Islands: static page with one interactive widget; implement by hand (HTML + a tiny script that hydrates only the widget with `hydrateRoot` on a specific element). Compare JS bytes and TBT with full-page hydration.
D7. JS cost lab: take a bundle of at least 1MB uncompressed (a big library); measure parse/compile/execute on 4x and 6x CPU throttle (Performance panel "Evaluate Script"); then code-split and lazy-load the rarely used part; compare TBT.
D8. CDN-cache SSR: cache the SSR HTML of a public page for 60s with `s-maxage`; show TTFB change; then show the failure with a personalized greeting rendered into the cached HTML (leak) and fix by moving the personalized part to the client or an edge include.

### BREAK
- Add 1.2s to server TTFB and show SSR's LCP is worse than CSR for a small page.
- Render on the server using the user's cookie, cache it publicly, and observe a leak.
- Break hydration in 5 ways (D4) and observe each.
- Throttle CPU 6x and compare how SSR content visibility and interactivity diverge (uncanny valley: click before hydration and observe what happens).

### QUIZ (cold)
1. What exactly happens during hydration?
2. Why can SSR make LCP worse?
3. Why does hydration cost more than "the JS download"?
4. Five hydration mismatch causes and their fixes.
5. How does streaming HTML work mechanically?
6. What does a Suspense boundary do on the server?
7. What is an island?
8. Why can SSR output not be shared-cached when personalized?
9. What do you measure to evaluate SSR vs CSR?
10. What breaks streaming at the infrastructure level?

### PHASE 3 EXIT TEST (cold, one sitting, 25 minutes)
1. Narrate a Network waterfall from a HAR you have not seen.
2. Explain precisely why SSR can hurt LCP when TTFB is bad.
3. Write cache headers for HTML, fingerprinted JS, user JSON, CDN public page (repeat from Phase 2: if it fails, Phase 2 did not stick).
4. Explain CORS + cookies + CSRF as one story (same repeat check).
5. Show a real page where LCP improved at least 30 percent with the lever named and numbers.
6. Decompose LCP and INP into their parts.
If any fail, do not start Postgres or Auth. Repeat Phase 3 modules.

### GATE
Spoken (5 minutes): CSR vs SSR vs streaming with your measured table; the hydration mechanism; and how a mismatch arises.
Pass: quiz 9/10, D1 to D8, Phase 3 exit passed, tags `gate/M3.4` and `gate/phase3`.

### DELIVERABLE AND FAILURE MODES
Deliverable: `labs/perf-lab` complete: before/after pages, HARs, traces, metrics tables, the hydration breakage catalog.
Faking it: citing "SSR is better for performance". The table of numbers is your only allowed opinion source.


---

# PHASE 4. POSTGRESQL

Repo: `labs/pg-lab`. Docker Postgres 16+ (or local). Client: `psql` plus `postgres.js`. ORM banned until M6.2 (and then only to read the SQL it emits). Migrations are numbered SQL files in the repo; the repo is the schema.

Schema theme: the `slotlock` domain (tenants, users, memberships, resources, bookings). It starts as a lab schema here and becomes the real app's schema in M6.1.

Standard setup (do once, before M4.1):
```
docker run -d --name pg -e POSTGRES_PASSWORD=pw -p 5432:5432 -v pgdata:/var/lib/postgresql/data postgres:16
psql postgres://postgres:pw@localhost:5432/postgres -c 'create database pglab'
```
`scripts/migrate.sh` applies `migrations/*.sql` in order, tracking applied files in a `schema_migrations(filename text primary key, applied_at timestamptz)` table. You write it (about 25 lines of bash or TypeScript).

---

## M4.1 SQL that is real

Hours: 16. Prerequisite: M0.3. Phase: 4.

### WHY
Fullstack seniors live in Postgres. If you only know an ORM, you do not know what your app does to the database, and every performance and correctness bug is opaque.

### ATOMS
Types
1. `text` (no length limit needed; `varchar(n)` rarely useful), `integer`, `bigint`, `smallint`; `numeric(p,s)` for exact decimals (money); never `float`/`double precision`/`money` for money.
2. `boolean`; `timestamptz` for instants (stores UTC; displays in session time zone); `timestamp` (no time zone) is a bug magnet; `date`, `time`, `interval`.
3. `uuid` (and `gen_random_uuid()`), `bigint generated always as identity` (modern replacement for `serial`); pick one per project.
4. `jsonb` vs `json`; use for genuinely schemaless payloads; operators `->`, `->>`, `@>`; GIN index; do not hide your schema in it.
5. Arrays and enums: when `CHECK (status IN (...))` or a lookup table is better than an `enum` type (enums are painful to change).
6. `NULL`: absence of a value. Three-valued logic: `NULL = NULL` is `NULL`; `x = NULL` is never true; use `IS NULL`, `IS DISTINCT FROM`. `NOT IN` with a NULL in the list returns no rows. `COUNT(col)` skips NULLs. `COALESCE`, `NULLIF`.

Schema design
7. Table, row, column, relation. Normalization in practice: one fact in one place; 1NF/2NF/3NF informally; denormalize deliberately.
8. Primary keys: surrogate vs natural; composite keys; why not use emails as PKs.
9. Foreign keys: referential integrity; `ON DELETE RESTRICT`/`NO ACTION` (default), `CASCADE`, `SET NULL`, `SET DEFAULT`; `ON UPDATE`. A FK does not automatically create an index on the referencing column (you add it).
10. `UNIQUE` (a unique index; NULLs distinct by default; `NULLS NOT DISTINCT` in PG15+), partial unique indexes (`WHERE deleted_at IS NULL`), unique on expressions (`lower(email)`).
11. `NOT NULL`, `DEFAULT`, `CHECK` constraints. Constraints are invariants enforced by the database regardless of which code path writes.
12. Relationships: one-to-many (FK in the many table), many-to-many (join table with composite key), one-to-one (FK + unique).
13. Naming: snake_case, singular or plural consistent, `created_at`/`updated_at timestamptz not null default now()`, FK columns `<table>_id`.
14. Soft delete vs hard delete: costs of soft delete (every query needs the filter, unique constraints need partial indexes). Prefer hard delete plus an audit table unless you must retain.
15. Multi-tenancy columns: `workspace_id` on every tenant-owned table; composite FKs `(workspace_id, id)` to prevent cross-tenant references.

SQL
16. `SELECT` anatomy and logical evaluation order: `FROM/JOIN`, `WHERE`, `GROUP BY`, `HAVING`, `SELECT`, `DISTINCT`, `ORDER BY`, `LIMIT/OFFSET`. Aliases are not visible in `WHERE`.
17. `INSERT ... VALUES`, `INSERT ... SELECT`, `ON CONFLICT DO NOTHING / DO UPDATE` (upsert), `RETURNING`.
18. `UPDATE ... SET ... WHERE ... RETURNING`; `UPDATE ... FROM`; `DELETE ... USING`; forgetting `WHERE`.
19. `JOIN`: inner, left, right, full, cross; join conditions; `USING` vs `ON`; self-join.
20. The left-join trap: a `WHERE right.col = x` on a left join silently makes it an inner join; move the condition into `ON`.
21. Aggregates: `COUNT`, `SUM`, `AVG`, `MIN`, `MAX`, `string_agg`, `array_agg`, `json_agg`, `FILTER (WHERE ...)`; `GROUP BY` with all non-aggregated columns; `HAVING` filters after aggregation, `WHERE` before.
22. Subqueries: scalar, `IN`, `EXISTS` (preferred for existence), correlated; `EXISTS` vs `IN` with NULL.
23. Set operations: `UNION` vs `UNION ALL`, `INTERSECT`, `EXCEPT`.
24. Time handling: `now()`, `current_date`, `date_trunc`, `extract`, `AT TIME ZONE`, ranges with half-open intervals `[start, end)`; why `BETWEEN` on timestamps is a bug.
25. String functions and `ILIKE`; collation; case-insensitive uniqueness via `lower()` index or `citext`.
26. `ORDER BY` determinism: add a unique tiebreaker; `LIMIT` without `ORDER BY` is random.
27. Parameterized queries: values sent separately from SQL text; the only defense against SQL injection; identifiers cannot be parameterized (whitelist them).

Operations
28. Migrations: numbered, immutable once applied anywhere, one logical change each, transactional (Postgres DDL is transactional), idempotent seeds separate from schema, no data edits via GUI.
29. `psql` fluency: `\d+`, `\dt`, `\di`, `\df`, `\x auto`, `\timing`, `\watch`, `\copy`, `\set`, `\gexec`.
30. Seeding with `generate_series`, `random()`, `gen_random_uuid()`.
31. Roles basics (used heavily in M5.6): `CREATE ROLE`, `GRANT`; the default `postgres` superuser is not an app user.

### RESOURCES (in order)
1. PostgreSQL docs, Tutorial (whole): https://www.postgresql.org/docs/current/tutorial.html . Chapters 1 to 3 completely. Type every example into `psql`.
2. PostgreSQL docs, Data Definition (DDL): https://www.postgresql.org/docs/current/ddl.html . Read: Table basics, Default values, Identity columns, Constraints (all), System columns (skim), Modifying tables, Privileges (skim; return in M5.6).
3. PostgreSQL docs, Data Manipulation: https://www.postgresql.org/docs/current/dml.html (Inserting, Updating, Deleting, Returning).
4. PostgreSQL docs, Queries: https://www.postgresql.org/docs/current/queries.html . Read: Table expressions (joins, GROUP BY), Select lists, Combining queries, Sorting, LIMIT.
5. PostgreSQL docs, Data Types: Numeric, Character, Date/Time, Boolean, UUID, JSON: https://www.postgresql.org/docs/current/datatype.html . Read the Date/Time page in full: it is where bugs live.
6. SQLBolt (only if SELECT/JOIN is shaky): https://sqlbolt.com/ . Finish in one sitting.
7. "Postgres: Don't Do This" wiki list: https://wiki.postgresql.org/wiki/Don%27t_Do_This . Read in full; each item is a pitfall.
8. Optional: "SQL Murder Mystery" or Mode SQL tutorial for extra practice reps: https://mystery.knightlab.com/ .

### START HERE (first 30 minutes)
1. `psql ... pglab`. Create `users(id bigint generated always as identity primary key, email text not null unique, created_at timestamptz not null default now())`.
2. Insert three users; select; update one; delete one. Run `\d users`. Read every line.
3. Try `select * from users where email = null;` and `select * from users where email is null;`. Note the first returns nothing even if a null existed.

### DRILLS
D1. `migrations/001_init.sql`: `users`, `workspaces`, `memberships` (`user_id`, `workspace_id`, `role text check (role in ('owner','member','viewer'))`, primary key `(user_id, workspace_id)`), `resources` (`workspace_id`, `name`, unique `(workspace_id, name)`), `bookings` (`resource_id`, `workspace_id`, `created_by`, `start_at`, `end_at`, `status text check (...)`, `check (end_at > start_at)`). Foreign keys with explicit `ON DELETE`. Acceptance: runs from an empty database via `scripts/migrate.sh`; idempotent re-run applies nothing.
D2. Seed script (`seed.sql` using `generate_series`): 10 users, 3 workspaces, memberships (including a user in two workspaces), 8 resources, 20 bookings. Acceptance: re-runnable (truncate-and-reseed or `ON CONFLICT DO NOTHING`).
D3. Query set in `queries.sql` (each with a comment stating the question): bookings for a workspace in a week (half-open interval); bookings overlapping `[a,b)` for a resource using `start_at < b and end_at > a`; members of a workspace with roles; count of bookings per resource, including resources with zero bookings (left join trap); the user with the most confirmed bookings; workspaces with no bookings (`NOT EXISTS`); upsert a membership role.
D4. Invariants: for each constraint, write the insert that violates it and save the exact Postgres error (including `SQLSTATE` and constraint name) in NOTES: duplicate `(workspace_id, name)`, null `resource_id`, `end_at <= start_at`, invalid `role`, deleting a workspace with bookings (restrict), cross-tenant booking where `resource.workspace_id != booking.workspace_id` (then fix with a composite FK `(workspace_id, resource_id)` referencing `resources(workspace_id, id)`).
D5. NULL traps lab: construct tables and queries demonstrating `= NULL`, `NOT IN` with a NULL, `COUNT(col)` vs `COUNT(*)`, `NULL` in a `UNIQUE` column, and `COALESCE`. Write the expected result before running each.
D6. Time lab: store bookings in `timestamptz`; set the session time zone to `UTC`, `Asia/Kolkata`, and `America/New_York`; show the same stored instant rendered three ways; compute "bookings today" for a user's time zone with `AT TIME ZONE`; show a DST boundary day with 23 or 25 hours; demonstrate why `BETWEEN` double-counts a boundary row.
D7. Money lab: store prices as `numeric(12,2)`; show `0.1 + 0.2` in `double precision` vs `numeric`; compute a 18 percent tax on 100 items with rounding policy decided and written down.
D8. SQL injection lab: a naive Node function building SQL with string concatenation; exploit it with `' OR 1=1 --`; then fix with parameters (`postgres.js` tagged templates). Add an `ORDER BY` column parameter and fix it with a whitelist (identifiers cannot be bound).
D9. `postgres.js` client: a script that connects, runs a parameterized query, a transaction (`sql.begin`), handles an error by `SQLSTATE` (`23505` unique violation to a 409), and closes the pool. Acceptance: a unique-violation test that maps to an `AppError` with code `RESOURCE_NAME_TAKEN`.
D10. Migrations discipline: write `002_add_booking_notes.sql` additive (nullable column), run it on a populated DB, then write the `down` plan as comments; practice a failing migration inside a transaction and see it roll back.

### BREAK
- `UPDATE bookings SET status='cancelled'` with no `WHERE` in a transaction; `ROLLBACK`; write why you always type `BEGIN` before manual data changes in a production shell.
- Insert a booking with `end_at` before `start_at` before and after the `CHECK`.
- Delete a user referenced by `memberships` with and without `CASCADE`.
- Cause a unique violation from two concurrent inserts (two psql sessions) and see which wins.
- Change a migration file after applying it and watch your migrate script (add a checksum column) refuse to proceed.

### QUIZ (cold)
1. Why `timestamptz` and what does it actually store?
2. Why `numeric` for money?
3. What does `x = NULL` return, and what about `x NOT IN (1, NULL)`?
4. What is the left-join trap?
5. `WHERE` vs `HAVING`?
6. What does a foreign key not do automatically?
7. How do you make an email unique case-insensitively?
8. Why must queries have an `ORDER BY` with a unique tiebreaker for pagination?
9. Why are parameters safe and what can't be parameterized?
10. Name one invariant a `CHECK` or `UNIQUE` enforces that TypeScript fundamentally cannot.

### GATE
Spoken (5 minutes): draw the ERD from memory (users, workspaces, memberships, resources, bookings, with keys and constraints); explain three NULL traps; explain half-open intervals; explain why invariants live in the database.
Pass: quiz 9/10, D1 to D10 committed, ERD photo in `evidence/`, tag `gate/M4.1`.

### DELIVERABLE AND FAILURE MODES
Deliverable: `labs/pg-lab/{migrations,seed.sql,queries.sql,scripts,client/}` plus a constraint-error catalog in NOTES.
Faking it: running a tutorial's schema. The schema must be yours, and every constraint must have a saved violation.

---

## M4.2 Indexes and EXPLAIN

Hours: 16. Prerequisite: M4.1. Phase: 4.

### WHY
Indexes are the single biggest lever on read performance, and `EXPLAIN` is how you stop guessing. "Feels faster" is banned; plans and timings are evidence.

### ATOMS
Storage and index fundamentals
1. Heap: table rows are stored in pages (8KB) in no particular order. A sequential scan reads all pages.
2. A B-tree index is a sorted tree of keys pointing to heap locations (TIDs). Supports `=`, `<`, `<=`, `>`, `>=`, `BETWEEN`, `IN`, `ORDER BY`, prefix `LIKE 'foo%'` (with C collation or `text_pattern_ops`), `IS NULL`.
3. An index is a second structure the database must keep correct on every `INSERT`/`UPDATE`/`DELETE`: reads faster, writes slower, disk larger. HOT updates and index bloat.
4. Selectivity: fraction of rows matched. High selectivity (few rows) benefits; indexing a low-cardinality boolean is usually useless (but partial indexes can fix this).
5. Primary keys and unique constraints create indexes automatically; foreign keys do not.
6. Composite indexes and the left-prefix rule: `(a, b, c)` serves `a`, `a,b`, `a,b,c` predicates and ordering by a prefix; does not serve `b` alone (except via an expensive full index scan).
7. Equality columns first, then range column, then `ORDER BY`: the usual composite ordering heuristic.
8. Column order and sort direction: `(a ASC, b DESC)` needs a matching index to avoid a sort.
9. Covering indexes: `INCLUDE (col)` and index-only scans (need the visibility map to be up to date: `VACUUM`).
10. Partial indexes: `CREATE INDEX ON bookings (resource_id, start_at) WHERE status = 'confirmed'`; also partial unique indexes.
11. Expression indexes: `CREATE INDEX ON users (lower(email))`; the query must use the identical expression.
12. Other index types: Hash (rare), GIN (jsonb, arrays, full-text, `pg_trgm`), GiST (ranges, geometry, exclusion constraints), BRIN (huge append-only tables ordered by time), SP-GiST. Know when each applies.
13. Text search: `LIKE '%foo%'` cannot use a B-tree; `pg_trgm` + GIN supports it; full-text search with `tsvector`; know the options before reaching for Elasticsearch.
14. Index bloat, `REINDEX`, `CREATE INDEX CONCURRENTLY` (no write lock; can't run inside a transaction; can fail and leave an invalid index).
15. Too many indexes: write amplification, planner time, memory. Drop unused ones (`pg_stat_user_indexes.idx_scan = 0`).

The planner and EXPLAIN
16. The planner picks the cheapest plan by estimated cost using table statistics (`pg_stats`, `ANALYZE`). It guesses; the plan can be wrong when statistics are stale or correlated.
17. `EXPLAIN` (estimates only) vs `EXPLAIN ANALYZE` (executes and shows actuals; wrap writes in a transaction you roll back) vs `EXPLAIN (ANALYZE, BUFFERS)` (adds I/O). Add `FORMAT JSON` for tools. `SETTINGS`, `WAL`, `VERBOSE` options.
18. Plan nodes: Seq Scan, Index Scan, Index Only Scan, Bitmap Index Scan + Bitmap Heap Scan, Sort, Limit, Aggregate, Hash/Nested Loop/Merge Join, Gather (parallel).
19. Reading a plan: the tree is executed from the innermost indented nodes outward; each node shows `cost=startup..total`, `rows` (estimated), `width`, and with ANALYZE `actual time=first..last`, `rows`, `loops`. Total time = actual time x loops for inner nodes.
20. Estimated vs actual rows: a large mismatch signals stale or insufficient statistics, correlated columns, or functions the planner can't see through.
21. `Buffers: shared hit=... read=...`: hit is cache, read is disk. `Rows Removed by Filter` shows wasted work.
22. `Sort Method: external merge Disk` means `work_mem` was too small.
23. Why an index isn't used: low selectivity, small table, function on the column, type mismatch (`::text` vs `int`), left-prefix violation, stale stats, `OR` conditions, `LIKE '%x'`, collation mismatch.
24. Fixing: add or reshape the index, rewrite the predicate to be sargable (`start_at >= x` not `date(start_at) = x`), update stats, raise `default_statistics_target` for a column, extended statistics.
25. Test discipline: use realistic data volume (100k+ rows), a warm and a cold cache run, repeat 3 times, and record the median; `SET enable_seqscan = off` is a diagnostic, never a fix.
26. `pg_stat_statements`: find the slowest and most frequent queries in a real system (enable the extension; drilled in M9.3).
27. Visualizers: https://explain.depesz.com/ and https://explain.dalibo.com/ for reading plans.

### RESOURCES (in order)
1. Markus Winand, "Use The Index, Luke": https://use-the-index-luke.com/ . Read in order: "Anatomy of an Index", "The Where Clause" (all), "Testing and Scalability", "Joins" (skim to the nested loops/hash/merge sections), "Sorting and Grouping", "Partial Results" (pagination, the seek method; you will reuse it in M6.2), "Insert, Delete and Update". Do the exercises on your own data.
2. PostgreSQL docs, Indexes: https://www.postgresql.org/docs/current/indexes.html . Read: Introduction, Index Types, Multicolumn, Indexes and ORDER BY, Combining Multiple Indexes, Unique, Expression, Partial, Index-Only Scans and Covering Indexes, Operator Classes (skim), Examining Index Usage.
3. PostgreSQL docs, "Using EXPLAIN": https://www.postgresql.org/docs/current/using-explain.html . Read in full; this is the manual for reading a plan. And "Planner Statistics": https://www.postgresql.org/docs/current/planner-stats.html .
4. PostgreSQL docs, `CREATE INDEX` (esp. `CONCURRENTLY`): https://www.postgresql.org/docs/current/sql-createindex.html .
5. Depesz, "Explaining the unexplainable" series (parts 1 to 3) for reading plans: https://www.depesz.com/tag/unexplainable/ .
6. `pg_trgm` docs: https://www.postgresql.org/docs/current/pgtrgm.html . Read the Introduction and Index Support.
7. Optional deep dive: Hironobu Suzuki, "The Internals of PostgreSQL", chapters 1, 3 (query processing) and 7 or 8 (heap/indexes): https://www.interdb.jp/pg/ .

### START HERE (first 30 minutes)
1. Seed 100,000 bookings: `insert into bookings (...) select ... from generate_series(1,100000)`. `analyze bookings;`
2. `explain (analyze, buffers) select * from bookings where workspace_id = 1 and start_at >= '2026-03-01' and start_at < '2026-03-08' order by start_at;`
3. Save the plan to `evidence/explain-001-before.txt`. Paste into explain.depesz.com and read each node.

### DRILLS
D1. Seed script producing 100,000 bookings across 50 resources, 3 workspaces, times spread over a year, mixed statuses (about 70 percent confirmed, 20 percent cancelled, 10 percent draft). Acceptance: `select count(*)` returns 100000; `analyze` run.
D2. The slow-query baseline: the workspace/date-range/order-by query. Save `EXPLAIN (ANALYZE, BUFFERS)` before. Add the matching composite index `(workspace_id, start_at)`. Save after. Write the before/after table: plan nodes, execution time, buffers, rows removed by filter.
D3. Left-prefix proof: with `(workspace_id, start_at)`, run queries filtering on (a) `workspace_id`, (b) both, (c) `start_at` alone, (d) `start_at` plus `workspace_id` order reversed in SQL (order in SQL doesn't matter). For each, record whether the index was used. Then add a second index `(start_at)` and explain whether it is justified.
D4. Sort elimination: query with `ORDER BY start_at DESC LIMIT 20`; show a Sort node disappears with a matching index. Show a case where the index direction/column order doesn't match and the Sort returns.
D5. Covering and index-only scan: `INCLUDE (status)` to answer a count by status without touching the heap; run `VACUUM` and show the "Heap Fetches" go down.
D6. Partial index: an index only on `status = 'confirmed'` rows; show size vs a full index (`pg_relation_size`), and show it's used only when the predicate matches.
D7. Expression index: `lower(email)` query; show the seq scan without the expression index and the index scan with it; show that `where email ilike ...` doesn't use it.
D8. Wrong-type and function traps: `where date(start_at) = '2026-03-05'` (seq scan) vs half-open range (index scan); `where id::text = '5'`; `where resource_id = 5::numeric`. Record which don't use the index and rewrite.
D9. `LIKE` lab: `title ILIKE '%q%'` stays a seq scan with a B-tree. Add `pg_trgm` + GIN and show the plan change and the improvement. Note the index size and write cost (time `insert` of 10,000 rows with and without the index).
D10. Write cost: measure insert throughput of 100,000 rows with 0, 2, 5 indexes. Chart it.
D11. Statistics: `select * from pg_stats where tablename='bookings'` — read `n_distinct`, `most_common_vals`, `correlation` for your columns; bulk insert 200,000 skewed rows without `ANALYZE` and see the estimate/actual mismatch; run `ANALYZE` and see the plan correct itself.
D12. `CREATE INDEX CONCURRENTLY`: build one while a second session inserts continuously; show it doesn't block writes (and that plain `CREATE INDEX` does).
D13. Unused index report: query `pg_stat_user_indexes` to find indexes with `idx_scan = 0`.
D14. Write `EXPLAIN.md` in the lab: for 5 queries, the SQL, the plan before, the change, the plan after, and a one-paragraph reading of both plans in your words.

### BREAK
- `SET enable_seqscan = off` to force an index and show a worse plan sometimes; explain why the planner's original choice was right.
- Create an index with the wrong column order and show it is ignored.
- Make a query return 90 percent of the table and show the index isn't used (and is correct not to be).
- Run the same query cold (restart Postgres, or `pg_prewarm` off) vs warm and compare `read` vs `hit` buffers.

### QUIZ (cold)
1. What does a B-tree index support and what can't it support?
2. What is the left-prefix rule? Give an example it fails.
3. Why does the planner sometimes prefer a seq scan?
4. `EXPLAIN` vs `EXPLAIN ANALYZE` vs `BUFFERS`?
5. What does a large estimated/actual row mismatch signal?
6. Name five reasons an existing index isn't used.
7. What's a covering index and what must be true for an index-only scan?
8. Why is `LIKE '%x%'` slow and what are the options?
9. What does `CREATE INDEX CONCURRENTLY` change?
10. What's the cost of an extra index?

### GATE
Spoken (5 minutes): read one of your plans out loud: what was scanned, why that scan type, estimated vs actual rows, where the time went, how the index changed it. Explain the left-prefix rule and the write cost.
Pass: quiz 9/10, D1 to D14, `EXPLAIN.md`, tag `gate/M4.2`.

### DELIVERABLE AND FAILURE MODES
Deliverable: `labs/pg-lab/EXPLAIN.md` with real numbers, `evidence/explain-*.txt`.
Faking it: "I added an index and it felt faster." The only accepted evidence is a plan and a timing.

---

## M4.3 Transactions, isolation, lost updates

Hours: 18. Prerequisite: M4.2. Phase: 4.

### WHY
Money and inventory bugs are concurrency bugs. Two requests that each did nothing wrong can jointly corrupt data. This module is the foundation of idempotency, overlap prevention and every "double booking" story.

### ATOMS
Transactions
1. A transaction groups statements into an atomic unit: all commit or none. Two statements forming one business action without a transaction is a bug.
2. `BEGIN`, `COMMIT`, `ROLLBACK`; autocommit (each statement its own transaction); savepoints (`SAVEPOINT`, `ROLLBACK TO`); an error inside a transaction aborts it until rollback.
3. ACID: Atomicity (all-or-nothing), Consistency (constraints hold), Isolation (concurrent transactions don't see each other's partial work, to a degree), Durability (committed data survives crashes via the WAL, `fsync`).
4. Write-ahead log (WAL): changes are logged before data pages; crash recovery replays the WAL; `synchronous_commit`.
5. Long transactions: hold locks, bloat tables (block `VACUUM`), and idle-in-transaction connections are a common outage cause; set `idle_in_transaction_session_timeout`.

MVCC
6. Multi-Version Concurrency Control: updates create a new row version; readers don't block writers and writers don't block readers; each transaction sees a snapshot.
7. Row visibility: `xmin`, `xmax`; dead tuples are cleaned by `VACUUM`.
8. Snapshot timing: Read Committed takes a new snapshot per statement; Repeatable Read per transaction.

Isolation levels and anomalies
9. Dirty read: reading uncommitted data. (Postgres never allows it, even in Read Uncommitted, which behaves as Read Committed.)
10. Non-repeatable read: re-reading a row yields a different committed value within the same transaction.
11. Phantom read: a repeated range query returns different rows.
12. Lost update: two transactions read-modify-write the same row and one overwrites the other.
13. Write skew: two transactions read overlapping data, each writes a different row based on what it read, and together violate an invariant (the classic "on-call doctor" and the "overlapping booking" case).
14. Read Committed (default): prevents dirty reads; allows non-repeatable reads, phantoms, lost updates (in plain read-modify-write), and write skew.
15. Repeatable Read (snapshot isolation in Postgres): prevents non-repeatable reads and phantoms; detects lost updates on the same row (raises `serialization_failure` 40001); still allows write skew.
16. Serializable (Serializable Snapshot Isolation): prevents all anomalies by aborting some transactions with 40001; you must retry.
17. Retries: any use of RR/Serializable (and deadlock detection, 40P01) requires a retry loop around the entire transaction.

Concurrency control techniques
18. Atomic updates in SQL: `UPDATE stock SET qty = qty - 1 WHERE id = $1 AND qty > 0` (check `rowCount`); `INSERT ... ON CONFLICT`. The simplest, best fix for counters.
19. Optimistic concurrency: `version` column; `UPDATE ... SET ..., version = version + 1 WHERE id = $1 AND version = $old`; zero rows means conflict, return 409 (and tie to HTTP `ETag`/`If-Match`, M2.4).
20. Pessimistic locking: `SELECT ... FOR UPDATE` (lock row until commit), `FOR NO KEY UPDATE`, `FOR SHARE`, `FOR UPDATE NOWAIT`, `SKIP LOCKED` (job queues, M6.3).
21. Lock modes and conflicts: row-level vs table-level locks; `LOCK TABLE`; DDL takes strong locks (`ACCESS EXCLUSIVE`), so risky migrations can block traffic; `lock_timeout` for migrations.
22. Deadlocks: two transactions locking rows in opposite order; Postgres detects and aborts one (40P01). Prevent by consistent lock ordering; keep transactions short.
23. Advisory locks: `pg_advisory_xact_lock(key)` for application-level mutual exclusion (e.g., per-tenant serial processing).
24. Constraint-based correctness: unique indexes and exclusion constraints let the database arbitrate races (the strongest guarantee because it can't be bypassed by a code path).
25. Exclusion constraints and range types: `tstzrange(start_at, end_at, '[)')`, `&&` overlap operator, `EXCLUDE USING gist (resource_id WITH =, during WITH &&)` (requires `btree_gist` extension for the `=` on scalars). Prevents overlapping bookings per resource, atomically.
26. Check-then-insert (`SELECT` to see if free, then `INSERT`) is a race at any isolation level below Serializable; replace with a constraint.
27. Idempotency at the database layer: a unique constraint on `(idempotency_key)` makes retries safe (used in M6.1).
28. Choosing a technique: counter -> atomic update; read-modify-write of one row across user think time -> optimistic version; short critical section needing exclusivity -> `FOR UPDATE`; set-wide invariant (no overlap) -> constraint; multi-row invariant without constraint -> Serializable plus retry.
29. Transactions across systems: no distributed ACID between Postgres and an email API or Stripe; patterns: outbox table, idempotent consumers (M6.3).
30. The transactional outbox: insert the event row in the same transaction as the state change; a worker publishes it later. Prevents "committed but event lost" and "event sent but rolled back".

### RESOURCES (in order)
1. PostgreSQL docs, "Concurrency Control" (whole chapter): https://www.postgresql.org/docs/current/mvcc.html . Read: Introduction, Transaction Isolation (all three levels, with their examples), Explicit Locking (row-level locks, deadlocks, advisory locks), Data Consistency Checks at the Application Level, Caveats.
2. Martin Kleppmann, *Designing Data-Intensive Applications* (2nd edition if you have it), Chapter 7 "Transactions" (1st edition). Read in full, slowly, over this module and M4.4 and M4.5: ACID, single-object and multi-object operations, weak isolation levels, lost updates, write skew and phantoms, serializability (2PL, SSI). Take notes per section. https://dataintensive.net/
3. PostgreSQL docs, "Range Types": https://www.postgresql.org/docs/current/rangetypes.html ; "Exclusion Constraints" in https://www.postgresql.org/docs/current/ddl-constraints.html#DDL-CONSTRAINTS-EXCLUSION ; `btree_gist`: https://www.postgresql.org/docs/current/btree-gist.html .
4. PostgreSQL docs, `SELECT ... FOR UPDATE`: https://www.postgresql.org/docs/current/sql-select.html#SQL-FOR-UPDATE-SHARE ; "Transactions" tutorial https://www.postgresql.org/docs/current/tutorial-transactions.html .
5. Jepsen, "Consistency Models" map: https://jepsen.io/consistency (read the map and the pages for snapshot isolation, serializable, read committed).
6. Peter Bailis, "Highly Available Transactions" or the Berenson et al. paper "A Critique of ANSI SQL Isolation Levels" abstract and intro: https://arxiv.org/abs/cs/0701157 .
7. Alex Petrov / Brandur Leach, "How Postgres Makes Transactions Atomic" and "Transactionally Staged Job Drains in Postgres" (outbox): https://brandur.org/postgres-atomicity and https://brandur.org/job-drain .
8. Optional: "Postgres explicit locking" (pgsql wiki: Lock monitoring): https://wiki.postgresql.org/wiki/Lock_Monitoring .

### START HERE (first 30 minutes)
1. Create `inventory(id int primary key, qty int not null check (qty >= 0), version int not null default 0)`; insert `(1, 1, 0)`.
2. Open two `psql` windows (A and B). In both: `begin; select qty from inventory where id=1;` (both see 1). In A: `update inventory set qty = qty - 1 where id=1; commit;`. In B: write the update as `set qty = 0` (the read-modify-write you computed in app code) and `commit`. Observe: sold twice.

### DRILLS
D1. Lost update, reproduced by hand with two sessions (read-compute-write with app-side math) under Read Committed. Save the session transcripts to `evidence/`.
D2. Fix it six ways, each in its own script, each proven with the same two-session race: (a) atomic `UPDATE ... SET qty = qty - 1 WHERE qty > 0`; (b) optimistic `WHERE version = $old` with 409 on zero rows; (c) `SELECT ... FOR UPDATE`; (d) `REPEATABLE READ` (observe the serialization failure and write the retry); (e) `SERIALIZABLE` with retry; (f) a `CHECK (qty >= 0)` as a backstop. Write for each: failure mode of the technique (retries, lock waits, deadlocks, throughput).
D3. Anomaly catalog: with two sessions and the default isolation level, demonstrate (and save transcripts for) a non-repeatable read and a phantom. Then switch to `REPEATABLE READ` and show both disappear. Demonstrate write skew (two doctors on call) under RR and show Serializable prevents it with a 40001.
D4. Overlapping bookings: first with check-then-insert in application code, run 50 concurrent inserts (Node script with `Promise.all` and separate connections) for the same resource and slot; show the number of rows exceeds one (the bug). Then add `btree_gist`, a generated `during tstzrange`, and `EXCLUDE USING gist (resource_id WITH =, during WITH &&)`. Re-run: exactly one succeeds, others fail with SQLSTATE `23P01`. Map `23P01` to a `409 SLOT_TAKEN` error.
D5. Atomic multi-statement unit: a transaction that inserts a booking and decrements a `credits` balance (with `CHECK (credits >= 0)`); force the second to fail; show no orphan booking after rollback. Write it with `postgres.js` `sql.begin`.
D6. Deadlock: create two transactions locking rows A then B and B then A; observe the 40P01 error and the server log; fix by ordering locks (sort ids). Add a retry wrapper for 40001/40P01 used by all transactional code (`withRetry(fn, {max: 3})`).
D7. `FOR UPDATE SKIP LOCKED` queue: a `jobs` table; three worker processes each claim one job at a time; show no job is processed twice; show a crashed worker's job being re-claimed after a lease (preview of M6.3).
D8. Advisory lock: serialize "monthly invoice generation per workspace" with `pg_advisory_xact_lock(hashtext('invoice:'||workspace_id))`; show two concurrent runs queue rather than interleave.
D9. Long transaction harm: open a transaction with `begin; select ...` and leave it idle; run updates and `VACUUM` in another session; observe dead tuples not removed (`pg_stat_user_tables.n_dead_tup`); set `idle_in_transaction_session_timeout` and show the session is killed.
D10. Migration lock hazard: in session A, `begin; select * from bookings limit 1;` (holds an `ACCESS SHARE`), in session B run `alter table bookings add column x int not null default 0` (or a type change) and observe waiting; in session C, a normal select blocks behind the waiting ALTER; use `lock_timeout` to fail fast. Write the rule you will follow in production.
D11. Outbox: write the booking and an `outbox(id, type, payload, created_at, published_at)` row in one transaction; a poller publishes unpublished rows with `FOR UPDATE SKIP LOCKED` and marks them. Kill the poller mid-publish; show at-least-once delivery and the need for an idempotent consumer.
D12. Paper exercise (30 minutes, cold): "Two browser tabs buy the last unit of the same item simultaneously." Choose and defend a technique, and write the failure mode of each technique you did not choose.

### BREAK
- Run the lost-update race 1,000 times with an app-side script and count wrong results per technique.
- Remove the `WHERE qty > 0` from the atomic update and see negative stock caught only by the `CHECK`.
- Forget to retry on 40001 and observe user-facing failures.
- Run the overlap test with the exclusion constraint but without `btree_gist` and read the error.
- Hold a `FOR UPDATE` lock across a slow external call and measure throughput collapse.

### QUIZ (cold)
1. Define atomicity, isolation, durability with a crash example.
2. What does MVCC mean for readers and writers?
3. What anomalies does Read Committed allow? Repeatable Read? Serializable?
4. What is write skew? Give your own example.
5. Name six fixes for a lost update and one failure mode of each.
6. Why must a Serializable transaction be retried?
7. What does `SKIP LOCKED` enable?
8. Why is check-then-insert a race, and what replaces it?
9. How do you prevent overlapping bookings in the database?
10. What problem does the outbox solve?

### GATE
Spoken (6 minutes): the two-tabs-buy-the-last-item problem: your chosen solution, and the failure mode of the two you didn't choose; then explain write skew with the overlapping booking example and the exclusion constraint.
Pass: quiz 9/10, D1 to D12, overlap proven with 50 concurrent inserts, tag `gate/M4.3`.

### DELIVERABLE AND FAILURE MODES
Deliverable: `labs/pg-lab/concurrency/` scripts and transcripts, retry wrapper, outbox demo.
Faking it: explaining isolation levels from memory without reproducing anomalies. Every anomaly must have a saved two-session transcript.

---

## M4.4 Pooling, N+1, migrations

Hours: 12. Prerequisite: M4.3. Phase: 4.

### WHY
"Works on my machine" with one connection, then dies in production with 50 serverless instances. And the most common ORM bug (N+1) is invisible until you read the SQL log.

### ATOMS
Connections and pooling
1. A Postgres connection is a backend process (about 5 to 10 MB plus work memory). `max_connections` (default 100) is a hard limit. Connections are expensive to open (TCP + TLS + auth).
2. App-level pool: reuse N connections; `postgres.js` `max`; a request waits for a free connection (queueing) when the pool is exhausted; pool timeouts.
3. Pool sizing: more connections does not mean more throughput; a rule of thumb is around `cores * 2 + effective_spindles` for the database; pool across all app instances must stay under `max_connections`.
4. Serverless amplification: 50 function instances x pool of 10 = 500 connections: outage. Use an external pooler (PgBouncer, Supavisor, RDS Proxy) or a pool size of 1 per instance.
5. PgBouncer modes: session pooling (client holds a server connection for its whole session), transaction pooling (server connection assigned per transaction; breaks session state: `SET`, `LISTEN/NOTIFY`, session advisory locks, prepared statements unless protocol-level support), statement pooling.
6. Prepared statements and transaction pooling pitfalls (`prepare: false` in `postgres.js`).
7. Connection leaks: forgetting to release in `try/finally`; `idle in transaction` sessions; `statement_timeout`, `idle_in_transaction_session_timeout`, `lock_timeout`, app-level timeouts.
8. Health checks and reconnect behavior after a DB restart; connection retry with backoff.
9. Read replicas: asynchronous replication lag, read-your-writes problems; know the concept (M10.1).

N+1 and query shape
10. N+1: one query for a list of N rows, then N queries to fetch each row's relation. Latency multiplies by RTT (M2.5) and load multiplies by N.
11. How to see it: log every SQL statement (`log_statement='all'` in a dev DB, or a query counter in your app, or `pg_stat_statements`), watch the count per request.
12. Fixes: a `JOIN` (watch row multiplication), `json_agg` to nest children in one query, `WHERE id = ANY($1)` batching ("dataloader" pattern), a lateral join.
13. Over-fetching and under-fetching: select only needed columns; don't `SELECT *` in hot paths.
14. Pagination in SQL: `LIMIT`/`OFFSET` costs O(offset); keyset pagination (M6.2).
15. Query count budget per endpoint: set and test it (e.g., listing endpoint must run at most 3 queries regardless of N).
16. ORM risk: lazy loading, implicit queries in loops, hiding transactions; why you read the SQL first.

Migrations discipline
17. Expand/contract: add the new column (nullable) -> deploy code that writes both -> backfill in batches -> switch reads -> make `NOT NULL` -> drop the old column in a later migration.
18. Never edit an applied migration; add a new one. Keep a checksum.
19. Dangerous operations: adding a column with a volatile default (rewrite in old versions; PG11+ is fine for constants), `ALTER COLUMN TYPE` (rewrite), `CREATE INDEX` without `CONCURRENTLY`, adding a `NOT NULL` or FK constraint without `NOT VALID` then `VALIDATE CONSTRAINT`, renames (breaks old code).
20. Backfills: batched, idempotent, throttled, resumable; don't run a 50M-row `UPDATE` in one transaction.
21. Rollback strategy: reversible migrations (down), or forward-fix; backups (`pg_dump`, PITR) and a tested restore; a backup you haven't restored is not a backup.
22. Migration tooling: plain SQL files + a tiny runner (yours), or `node-pg-migrate`/`dbmate`/`sqitch`/Drizzle Kit later; run migrations as a separate step with a different DB role than the app.
23. Schema drift, seed data vs migrations, test DBs created from migrations (never from a dump of dev).
24. `jsonb` caution: appropriate for archiving a webhook payload; a smell when it stands in for columns you can't now index or constrain properly.

### RESOURCES (in order)
1. PostgreSQL wiki, "Number Of Database Connections": https://wiki.postgresql.org/wiki/Number_Of_Database_Connections . Brandur, "Postgres connection pool sizing" and the HikariCP "About Pool Sizing": https://github.com/brettwooldridge/HikariCP/wiki/About-Pool-Sizing .
2. PgBouncer docs, "Features" and "Config" (pool_mode): https://www.pgbouncer.org/features.html .
3. `postgres.js` docs: connection options (`max`, `idle_timeout`, `connect_timeout`, `prepare`), transactions, errors: https://github.com/porsager/postgres .
4. PostgreSQL docs, "Server Configuration: Connections and Authentication" and "Client Connection Defaults" (timeouts): https://www.postgresql.org/docs/current/runtime-config-connection.html ; https://www.postgresql.org/docs/current/runtime-config-client.html .
5. Kleppmann, DDIA chapter 7 (continue) and chapter 3 sections on indexes if not yet read.
6. Citus/Braintree/GitLab write-ups on safe migrations: "Zero-downtime Postgres migrations" by Braintree; GitLab "Migration Style Guide": https://docs.gitlab.com/ee/development/migration_style_guide.html . Read the GitLab guide's sections on adding columns, indexes, constraints, and batched background migrations.
7. "Strong Migrations" README (Ruby gem, but the table of unsafe operations is universal): https://github.com/ankane/strong_migrations .
8. PostgreSQL docs: Backup and Restore (`pg_dump`, continuous archiving/PITR overview): https://www.postgresql.org/docs/current/backup.html .

### START HERE (first 30 minutes)
1. Turn on query logging in your lab DB: `alter system set log_statement = 'all'; select pg_reload_conf();` and `tail` the log (`docker logs -f pg`).
2. Write a handler listing workspaces with their members: one query for workspaces, then a loop doing one query per workspace. Count the log lines for a request with 10 workspaces.

### DRILLS
D1. N+1 before/after: a list endpoint for 50 workspaces with their member counts and their latest booking. Version 1 loops (N+1+N). Version 2 uses one aggregate query with `LEFT JOIN`/`json_agg`/a lateral join. Paste both SQL log traces side by side with latency at an artificial 5ms DB RTT (`pg_sleep` or a `toxiproxy` delay). Add a test that fails if the endpoint issues more than 3 queries.
D2. Dataloader pattern: implement a tiny `batch(loadFn)` that collects ids in a tick and issues one `WHERE id = ANY($1)`; use it to remove an N+1 that a JOIN can't express.
D3. Pool experiment: set the pool max to 2; run 20 concurrent slow queries (`select pg_sleep(0.2)`); chart queueing time distribution; set max to 10 and compare; set max to 100 against a low-core container and compare throughput to find the plateau.
D4. Pool exhaustion bug: write a handler that leaks a connection on error (no `finally`); trigger errors until the pool locks up; fix; add a pool-wait timeout alarm.
D5. Serverless math: write a calculator `instances * pool_max` vs `max_connections` in NOTES, then reproduce an actual `too many connections` error by launching 150 separate connections from a script; fix by running PgBouncer (docker) in transaction mode and show the connection count in `pg_stat_activity`. Show a session-level `SET` breaking under transaction pooling.
D6. Timeouts: configure `statement_timeout` (5s), `lock_timeout` (2s), `idle_in_transaction_session_timeout` (10s) on the app role; trigger each and capture the error and SQLSTATE (`57014`, `55P03`, `25P03`).
D7. Expand/contract rollout: rename `bookings.notes` to `bookings.description` using the full 5-step sequence across five numbered migrations with a running app (a small load loop hitting the API continuously); show zero errors. Then do the naive `RENAME` mid-load and show the errors.
D8. Backfill: add a derived column on 1,000,000 rows via a batched, resumable script (`WHERE id > $last ORDER BY id LIMIT 5000`); compare to one giant update's lock/bloat/time.
D9. Safe constraints: add a CHECK constraint with `NOT VALID` then `VALIDATE CONSTRAINT`; show lock levels (`pg_locks`) differ from the direct version on a large table.
D10. Backup and restore: `pg_dump -Fc` your lab DB, drop it, restore from the dump into a new database, and run a verification query script that compares row counts and a checksum. Write the restore runbook.
D11. Migration runner hardening: add checksums, a lock (`pg_advisory_lock`) so two runners can't race, a dry-run flag, and transactional application per file. Tests with two concurrent runners.

### BREAK
- Run 200 requests concurrently against an N+1 endpoint and graph p95 vs the fixed version.
- Kill Postgres under load and observe your pool's reconnection behavior; add retry with backoff.
- Apply a migration that takes `ACCESS EXCLUSIVE` on a hot table while traffic runs and record the stall.
- Edit an applied migration and observe your runner reject it.

### QUIZ (cold)
1. What does a Postgres connection cost and why is `max_connections` finite?
2. How should pool size be reasoned about?
3. Why do serverless functions + Postgres break, and what are the fixes?
4. PgBouncer transaction vs session mode: what breaks?
5. How do you detect an N+1 and how do you fix it three ways?
6. Describe expand/contract in five steps.
7. Which migration operations are dangerous on big tables?
8. How do you backfill safely?
9. Which timeouts do you set and why?
10. Why is a backup you have never restored not a backup?

### GATE
Spoken (4 minutes): explain the connection ceiling with a serverless example and the fix, show the N+1 trace pair and the query-count test, and walk through a zero-downtime column rename.
Pass: quiz 9/10, D1 to D11, tag `gate/M4.4`.

### DELIVERABLE AND FAILURE MODES
Deliverable: `labs/pg-lab/{n-plus-one,pool,migrations-lab}/`, restore runbook.
Faking it: reading about N+1 without a trace. The side-by-side logs are mandatory.

---

## M4.5 Window functions, CTEs, partitioning, vacuum (Phase 4 exit)

Hours: 16. Prerequisite: M4.4. Phase: 4.

### WHY
This is the step from "can write CRUD SQL" to "can own a schema that survives real reporting and real scale". Top-N per group, hierarchies and time-partitioned tables are standard problems.

### ATOMS
Window functions
1. A window function computes per row over a set of related rows without collapsing them (unlike `GROUP BY`). Syntax: `fn() OVER (PARTITION BY ... ORDER BY ... frame)`.
2. Ranking: `ROW_NUMBER()`, `RANK()`, `DENSE_RANK()`, `NTILE(n)`, `PERCENT_RANK()`.
3. Offset: `LAG(col, n)`, `LEAD(col, n)`, `FIRST_VALUE`, `LAST_VALUE` (frame gotcha), `NTH_VALUE`.
4. Aggregates as windows: `SUM(x) OVER (ORDER BY t)` running totals; `AVG(x) OVER (... ROWS BETWEEN 6 PRECEDING AND CURRENT ROW)` moving average.
5. Frames: `ROWS` vs `RANGE` vs `GROUPS`; the default frame with `ORDER BY` is `RANGE BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW`.
6. Use cases: top-N per group, latest per group, gaps and islands, difference from previous row, percentile buckets, de-duplication (`row_number() = 1`).
7. `DISTINCT ON (col)` as the Postgres shortcut for latest per group; trade-offs vs window functions.
8. Windows are evaluated after `WHERE`/`GROUP BY`/`HAVING`; you can't filter on a window function in `WHERE` (wrap in a subquery or CTE).
9. `LATERAL` joins: a subquery per outer row; the efficient "top N per group" for a small number of groups with an index.

CTEs and recursion
10. `WITH name AS (...)`: named subquery for readability; since PG12 non-recursive CTEs are inlined unless `MATERIALIZED` is used or they have side effects.
11. Data-modifying CTEs: `WITH deleted AS (DELETE ... RETURNING *) INSERT INTO archive SELECT * FROM deleted`.
12. Recursive CTE: anchor query `UNION ALL` recursive step referencing itself; terminates when the recursive step returns no rows; add depth limits and cycle detection (`CYCLE` clause in PG14+ or an array path).
13. Use cases: org charts, category trees, resource groups, graph reachability, generating series.

Partitioning
14. Declarative partitioning: `PARTITION BY RANGE (start_at)`, `LIST`, `HASH`; each partition is a table; the partition key must be part of the primary key and unique constraints.
15. Partition pruning: queries filtering on the partition key skip other partitions (see it in `EXPLAIN`); works at plan time and execution time.
16. Benefits: drop old data instantly (`DROP TABLE bookings_2024_01` vs a huge `DELETE` and bloat), smaller indexes per partition, easier maintenance. Costs: more complexity, planner overhead with thousands of partitions, cross-partition unique constraints are not possible.
17. Creating and attaching partitions ahead of time (`pg_partman` extension or a cron job); default partition catch-all.
18. When to partition: usually when tables reach hundreds of millions of rows or have time-based retention; most apps do not need it. Indexes and good queries first.

Materialized views and reporting
19. `CREATE MATERIALIZED VIEW`: stores results; `REFRESH MATERIALIZED VIEW` locks reads; `REFRESH ... CONCURRENTLY` needs a unique index and doesn't block reads. Staleness is a product decision.
20. Alternatives: summary tables updated by triggers/jobs, incremental aggregates, caching in the app.
21. Read replicas for reporting; separation of OLTP and analytics (OLAP, columnar stores) as a concept.

Planner internals and maintenance
22. Join algorithms: nested loop (good for few outer rows with an indexed inner), hash join (builds a hash table of the smaller side; needs `work_mem`, spills to disk if too large), merge join (both sides sorted). Chosen by estimated row counts.
23. `work_mem` is per sort/hash node per query; too small means disk spills (visible as `Disk` in EXPLAIN); too large times many connections means OOM.
24. `VACUUM` (reclaims dead tuples, updates the visibility map), `VACUUM FULL` (rewrites table, locks), autovacuum (thresholds, scale factors; per-table tuning for hot tables), `ANALYZE` (refreshes stats), table and index bloat, transaction ID wraparound (know the term and why autovacuum must run).
25. `pg_stat_statements`, `pg_stat_activity`, `pg_locks`, `pg_stat_user_tables`: where to look.
26. Extended statistics: `CREATE STATISTICS` for correlated columns.
27. Generated columns, triggers (and why business logic in triggers is a cost), `LISTEN/NOTIFY` as a lightweight pub/sub (payload limits, not durable).
28. Full-text search basics: `tsvector`, `tsquery`, `to_tsvector`, GIN, ranking; when it beats `LIKE`.

### RESOURCES (in order)
1. PostgreSQL docs, Window Functions tutorial: https://www.postgresql.org/docs/current/tutorial-window.html ; the function reference https://www.postgresql.org/docs/current/functions-window.html ; syntax (frames) https://www.postgresql.org/docs/current/sql-expressions.html#SYNTAX-WINDOW-FUNCTIONS .
2. PostgreSQL docs, "WITH Queries (Common Table Expressions)": https://www.postgresql.org/docs/current/queries-with.html . Read the recursive section carefully.
3. PostgreSQL docs, "Table Partitioning": https://www.postgresql.org/docs/current/ddl-partitioning.html . Read: Overview, Declarative Partitioning, Partition Maintenance, Partitioning and Constraint Exclusion, Best Practices.
4. PostgreSQL docs, Materialized Views: https://www.postgresql.org/docs/current/rules-materializedviews.html ; `REFRESH MATERIALIZED VIEW` reference.
5. Use The Index, Luke, "Joins" chapter (nested loops, hash join, sort-merge join): https://use-the-index-luke.com/sql/join .
6. PostgreSQL docs, "Routine Vacuuming": https://www.postgresql.org/docs/current/routine-vacuuming.html ; Resource Consumption (`work_mem`): https://www.postgresql.org/docs/current/runtime-config-resource.html .
7. PostgreSQL docs, Full Text Search intro: https://www.postgresql.org/docs/current/textsearch-intro.html ; `LISTEN`: https://www.postgresql.org/docs/current/sql-listen.html .
8. Kleppmann DDIA ch. 3 ("Storage and Retrieval": B-trees vs LSM, OLTP vs OLAP) and ch. 6 (Partitioning). Finish chapter 7 if not done.
9. Optional: "SQL Performance Explained" patterns on Pagination (seek) and "Modern SQL" by Markus Winand: https://modern-sql.com/ (read the window function and CTE pages).

### START HERE (first 30 minutes)
1. On your 100k bookings: `select resource_id, start_at, row_number() over (partition by resource_id order by start_at desc) rn from bookings limit 20;`
2. Wrap it in a subquery to keep only `rn = 1`. You now have "most recent booking per resource" in one query.

### DRILLS
D1. Top-N per group: "top 3 most-booked resources per workspace in the last 30 days" as a single query with `rank()` over an aggregate subquery. Compare against the application-loop version (N queries) with logs and timing. Also write it with `LATERAL`, and with `DISTINCT ON` for the top 1 case. Compare plans.
D2. Running totals and moving averages: bookings per day per workspace with a 7-day moving average and a cumulative total; `LAG` to compute day-over-day change; handle missing days via a `generate_series` left join.
D3. Gaps and islands: find, for one resource, contiguous ranges of consecutive booking days and the idle gaps between bookings (`lead(start_at) over (order by start_at)`).
D4. Recursive CTE: add `resource_groups(id, parent_id, name)`; seed a 4-level tree; write the query returning all descendants of a node with depth and path; add a cycle in the data and show infinite recursion, then guard with depth limit and `CYCLE` detection.
D5. Data-modifying CTE: archive bookings older than a year in one statement (`DELETE ... RETURNING` into `bookings_archive`); verify counts; wrap in a transaction.
D6. Partitioning: create `bookings_p` partitioned by month on `start_at`; migrate the 100k rows; run a one-month query and show pruning in `EXPLAIN` (only one partition scanned); detach and drop an old partition and time it vs an equivalent `DELETE`. Try creating a unique constraint not including the partition key and read the error.
D7. Materialized view: confirmed bookings per day per workspace; refresh normally and `CONCURRENTLY` (with a unique index) while a reader runs; compare query time vs the raw aggregate; decide a staleness policy and write it down.
D8. Join strategies: write three queries that make the planner choose a nested loop, a hash join and a merge join (vary row counts, indexes, `ORDER BY`); capture the plans; explain why each was chosen.
D9. `work_mem` spill: `SET work_mem='64kB'` and run a large sort or hash join; find `Disk` in the plan; raise `work_mem` and compare time. Compute a safe global value given `max_connections`.
D10. Stale stats trap: `ANALYZE`, bulk insert 500k skewed rows, run a query depending on the distribution (estimate vs actual mismatch, a bad join choice), then `ANALYZE` and fix. Create extended statistics for two correlated columns and show an improved estimate.
D11. Vacuum: update every row of a 100k table several times; see `n_dead_tup` and table size grow; run `VACUUM` and see size stay (space reusable) and `VACUUM FULL` shrink it (and take a lock). Look at autovacuum logs.
D12. Full-text: add a `tsvector` generated column and GIN index to a notes table; query with `plainto_tsquery`; rank; compare with `ILIKE`.
D13. `LISTEN/NOTIFY`: one process listens on `booking_confirmed`; a trigger or app code notifies after commit; build a tiny SSE endpoint from M2.6 backed by it (closing the deferred M2.6 D9 for two server processes). Note the limits (non-durable, 8000-byte payload, per-connection).
D14. Capstone query set: write `reports.sql` with five reports (occupancy per resource per week, top customers, cancellation rate trend, hourly heatmap, longest idle gaps) each with an EXPLAIN comment and an index justification.

### BREAK
- Filter on a window function in `WHERE` and read the error; fix with a subquery.
- Use `LAST_VALUE` with the default frame and get the wrong answer; fix with `ROWS BETWEEN UNBOUNDED PRECEDING AND UNBOUNDED FOLLOWING`.
- Refresh a materialized view non-concurrently while a long reader runs and observe blocking.
- Insert a row outside all partitions without a default partition and read the error.

### QUIZ (cold)
1. Window function vs `GROUP BY`?
2. `row_number` vs `rank` vs `dense_rank`.
3. How does `DISTINCT ON` differ from `row_number() = 1`?
4. How does a recursive CTE terminate and how do you stop cycles?
5. What is partition pruning and what constraints does partitioning impose?
6. When would you not partition?
7. Materialized view refresh options and trade-offs?
8. Nested loop vs hash vs merge join: when and why?
9. What does `work_mem` affect and how do you see a spill?
10. What do `VACUUM` and `ANALYZE` do and what happens without them?

### PHASE 4 EXIT TEST (cold, one sitting, 40 minutes)
1. Draw the ERD from memory.
2. Show an EXPLAIN before/after on 100k rows and read it aloud.
3. Reproduce a lost update and fix it three ways, naming each's failure mode.
4. Prove overlapping bookings are impossible via a constraint.
5. N+1 before/after trace.
6. Write the top-3-per-group query with a window function without notes.
7. Explain read committed vs repeatable read vs serializable with write skew.
Fail any and you repeat the module that owns it before Phase 5.

### GATE
Spoken (5 minutes): given "top 3 most-booked resources per workspace, last 30 days" write the query on paper; read a hash join vs nested loop plan and say why each was chosen; explain partition pruning.
Pass: quiz 9/10, D1 to D14, Phase 4 exit passed, tags `gate/M4.5` and `gate/phase4`.

### DELIVERABLE AND FAILURE MODES
Deliverable: `labs/pg-lab` complete: migrations, seed, queries, `EXPLAIN.md`, concurrency scripts and transcripts, reports, restore runbook.
Faking it: copying window-function solutions from StackOverflow. The test: write a variant on paper with a different grouping.


---

# PHASE 5. AUTHENTICATION AND AUTHORIZATION

Repo: `labs/auth-lab` (Hono + `postgres.js` + the `pg-lab` schema). Server first; a bare HTML page for browser-only drills. No auth libraries until M5.5. Security work always has an attacker: every module has an "attack" drill where you are the attacker first and the defender second.

Threat-model habit (use in every module): write four lines in NOTES before building. Who is the attacker? What can they control (request, cookie, link, script on our origin)? What do they want? Which control stops it?

---

## M5.1 Passwords and sessions

Hours: 14. Prerequisite: M4.5, M2.2. Phase: 5.

### WHY
You will implement session auth by hand once so you know exactly what a library does. Most auth breaches come from storage and lifecycle mistakes (weak hashing, no rotation, no revocation), not from exotic crypto.

### ATOMS
Concepts
1. Authentication (who are you) vs authorization (what may you do) vs accounting/audit (what did you do). Different bugs, different tests.
2. Credentials: something you know, have, are. MFA combines factors.
3. Session: server state mapping an opaque random ID to a user; the browser holds only the ID in a cookie.
4. Session ID requirements: at least 128 bits from a CSPRNG (`crypto.randomBytes(32)`), opaque (no user data), unguessable, never in a URL.
5. Store only a hash of the session token in the DB (`sha256(token)`), so a database leak does not yield live sessions.
6. Session row: `id (hash)`, `user_id`, `created_at`, `last_seen_at`, `expires_at` (absolute), `idle_expires_at`, `revoked_at`, `user_agent`, `ip` (informational), `mfa_verified_at`.
7. Absolute timeout vs idle timeout vs sliding renewal; "remember me" as a separate longer-lived token.
8. Cookie flags for the session: `__Host-` prefix, `Secure`, `HttpOnly`, `SameSite=Lax`, `Path=/`, no `Domain` (M2.2).
9. Session fixation: attacker plants a known session ID before login; defense: always issue a new session ID at login (and on privilege change).
10. Logout: delete the cookie and revoke the row server-side; "log out everywhere": revoke all rows for the user; password change revokes other sessions.
11. Session listing (devices) and revocation UI.

Passwords
12. Never store passwords or reversible encryption. Use a slow, memory-hard, salted password hash: Argon2id (preferred), scrypt, bcrypt (72-byte limit), PBKDF2 (if FIPS). Parameters (memory, iterations, parallelism) tuned to roughly 100 to 500ms on your hardware; store the parameters in the encoded hash string for later upgrades.
13. Salt: unique per password, stored with the hash; pepper: a secret key kept outside the DB (optional defense in depth).
14. Plain SHA-256 or MD5 for passwords is a vulnerability: GPUs compute billions per second.
15. Password policy per NIST 800-63B: minimum length (8+, prefer 12+), allow long passphrases and all characters, no composition rules, no forced periodic rotation, check against breached password lists (HIBP k-anonymity API), block common passwords.
16. Timing attacks: compare secrets with constant-time comparison (`crypto.timingSafeEqual`); do work (a dummy hash) even when the user doesn't exist so login time doesn't reveal account existence.
17. Account enumeration: login, signup and reset should not reveal whether an email exists (or document the product choice and add rate limits).
18. Login flow steps: normalize email (trim, lowercase, Unicode NFKC), look up, verify hash, check account status (locked/verified), rotate session, set cookie, audit log.
19. Rehash on login if parameters are outdated.
20. Brute force defense: per-account and per-IP rate limiting with backoff, global limits, optional CAPTCHA, lockout trade-offs (lockout enables denial of service against victims).
21. Credential stuffing: reused passwords from other breaches; defenses: breached-password check, rate limits, MFA, device signals.

Reset and verification
22. Reset tokens: random 256-bit, stored hashed, short TTL (15 to 60 min), single use, bound to the user, invalidated on password change; the reset link is a bearer credential: don't leak via `Referer` (set `Referrer-Policy: no-referrer` on that page).
23. Email verification tokens: same properties; an unverified email must not grant account takeover by "claiming" an address.
24. Email is not authentication of identity forever: email changes need re-auth; password reset email is a high-value target.
25. Sensitive actions need re-authentication (change email, delete account, add API key).
26. MFA overview: TOTP (RFC 6238), WebAuthn/passkeys (the future; phishing-resistant), SMS (weak), recovery codes. You implement TOTP in a stretch drill and study passkeys conceptually.
27. JWT as a session: signed blob (`header.payload.signature`); stateless verification; revocation is hard (denylist = state again); size; algorithm confusion (`alg: none`, RS/HS confusion); token theft; storage choices. "Stateless auth" is a tradeoff, not a win. You will not ship JWTs in this module.
28. API tokens/personal access tokens: random, hashed at rest, scoped, revocable, shown once, prefix for identification (`slk_live_...`).
29. Audit log: who logged in from where, failed attempts, password changes; useful to users and incident response.

### RESOURCES (in order)
1. OWASP Authentication Cheat Sheet: https://cheatsheetseries.owasp.org/cheatsheets/Authentication_Cheat_Sheet.html . Full.
2. OWASP Password Storage Cheat Sheet: https://cheatsheetseries.owasp.org/cheatsheets/Password_Storage_Cheat_Sheet.html . Full (parameters table).
3. OWASP Session Management Cheat Sheet: https://cheatsheetseries.owasp.org/cheatsheets/Session_Management_Cheat_Sheet.html . Full.
4. OWASP Forgot Password Cheat Sheet: https://cheatsheetseries.owasp.org/cheatsheets/Forgot_Password_Cheat_Sheet.html .
5. NIST SP 800-63B, Digital Identity Guidelines, Authentication and Lifecycle Management: https://pages.nist.gov/800-63-3/sp800-63b.html . Read section 5.1.1 (memorized secrets) and 7 (session management).
6. Lucia's session guide (conceptual, library-free; the repo is archived but the guide is the best minimal reference): https://lucia-auth.com/ . Read, then close it and implement yourself.
7. Have I Been Pwned "Pwned Passwords" API (k-anonymity): https://haveibeenpwned.com/API/v3#PwnedPasswords .
8. Argon2 RFC 9106: https://www.rfc-editor.org/rfc/rfc9106 (abstract, section 4 parameters). Node `crypto` docs: `scrypt`, `randomBytes`, `timingSafeEqual`: https://nodejs.org/api/crypto.html . Bun's `Bun.password` (Argon2id/bcrypt): https://bun.sh/docs/api/hashing#bun-password .
9. JWT introduction and pitfalls: https://jwt.io/introduction ; Tim McLean, "Critical vulnerabilities in JSON Web Token libraries": https://auth0.com/blog/critical-vulnerabilities-in-json-web-token-libraries/ ; "JWTs are not session tokens" arguments (Joepie91's "Stop using JWT for sessions": https://gist.github.com/samsch/0d1f3d3b4745d778f78b230cf6061452 ), read as one critique.
10. MDN, "Authentication" overview: https://developer.mozilla.org/en-US/docs/Web/HTTP/Guides/Authentication .

### START HERE (first 30 minutes)
1. Threat model in NOTES (4 lines): attacker with a stolen DB dump; attacker guessing passwords; attacker planting a session ID; attacker with network access.
2. `crypto.randomBytes(32).toString('base64url')` in a REPL. Hash it with `crypto.createHash('sha256')`. Say aloud which one goes in the cookie and which in the DB.
3. Time `Bun.password.hash('hunter2', 'argon2id')` (or `crypto.scrypt`) 10 times. Record milliseconds.

### DRILLS
D1. Schema: `users(id, email citext unique, email_verified_at, password_hash, created_at, disabled_at)`, `sessions(id text primary key, user_id, created_at, last_seen_at, expires_at, idle_expires_at, revoked_at, user_agent, ip)`, `password_resets(token_hash, user_id, expires_at, used_at)`, `login_attempts` (or use an in-memory limiter). Acceptance: migration runs from scratch.
D2. Register, login, logout, with Argon2id (or scrypt) and encoded params in the hash. Cookie `__Host-sid` with all flags (use local HTTPS via Caddy or `mkcert`, since `__Host-` requires `Secure`). Session middleware loads `c.var.user` or `null`. Acceptance: 12 integration tests including wrong password, unknown email (same response and similar timing), expired session, idle timeout, revoked session.
D3. Token hygiene: store only `sha256(token)`. Dump the `sessions` table; prove the dumped values can't be used as cookies. Constant-time compare for any direct token comparison.
D4. Rotation and fixation attack: from an attacker page/curl, plant a known `sid` cookie value (simulate subdomain cookie tossing) before login; log in; show the old ID is not valid after login because you issued a new one. Then temporarily disable rotation and show the fixation works.
D5. Logout and logout-all: `POST /logout` revokes the current row; `POST /logout-all` revokes all rows for the user; `POST /password` changes the password and revokes all other sessions. Tests prove each. Add `GET /sessions` listing devices and `DELETE /sessions/:id`.
D6. Rate limiting: sliding-window or token-bucket limiter keyed by IP and by normalized email; `429` with `Retry-After`; tests with fake clocks. Show that per-IP-only limiting is bypassed with many IPs and per-account-only enables lockout DoS; explain your combined policy.
D7. Timing: measure the login endpoint response times for existing vs nonexistent emails (1,000 samples each); show a statistical difference when you skip the dummy hash and none when you add it.
D8. Password policy: enforce length 12+, check HIBP via k-anonymity (send first 5 hex of SHA-1), reject common passwords; tests with a mocked HIBP.
D9. Password reset: `POST /reset` (always returns 202), emails (log to console) a link with a 256-bit token stored hashed with 30-minute TTL, single use; `POST /reset/confirm` sets the password and revokes sessions. Tests: expired token, reused token, token for another user, enumeration (response identical).
D10. Offline cracking demo: take a leaked dump of your own users table using (a) unsalted SHA-256, (b) salted SHA-256, (c) bcrypt, (d) Argon2id; crack a list of 10 weak passwords with a 100k-word list using a script; record time-to-crack per scheme.
D11. JWT hands-on (study only, not shipping): hand-build an HS256 JWT with `crypto`, verify it, then forge one with `alg: none` against a naive verifier, and one with a changed `kid`; fix the verifier by pinning the algorithm. Write the revocation problem and three mitigations (short TTL + refresh, denylist, versioning column) and why each reintroduces state.
D12. API tokens: `POST /tokens` returns `slk_<random>` once, stores `sha256`, supports scopes and `last_used_at`; `Authorization: Bearer ...` middleware; revoke endpoint.
D13. Stretch: TOTP MFA (RFC 6238) with `otpauth` or a hand-rolled HMAC-SHA1 implementation verified against test vectors; recovery codes stored hashed. Also read about WebAuthn: https://webauthn.guide/ .

### BREAK
- Store the raw session token and show a DB read leading to account takeover.
- Use `Math.random()` for a token; recover the next token from earlier ones (small PRNG state demo with `seedrandom`).
- Skip `HttpOnly`; steal the cookie via XSS (M2.2 drill).
- Return different errors for unknown email vs wrong password; enumerate emails with a script.
- Make reset tokens non-expiring and multi-use; demonstrate account takeover via an old email in a mailbox.

### QUIZ (cold)
1. Why store only a hash of the session ID?
2. What is session fixation and what stops it?
3. Why Argon2id and not SHA-256? What parameters matter?
4. How does a timing attack reveal account existence?
5. What are the properties of a good reset token?
6. NIST's password guidance in four points?
7. What does logout-all do and why must it be server-side?
8. Why is JWT revocation the hard part? Name `alg` confusion.
9. Per-IP vs per-account rate limits: pros and cons?
10. Why `__Host-` prefix for session cookies?

### GATE
Spoken (6 minutes): draw the login sequence (POST credentials, hash verify, new session row, `Set-Cookie`), then subsequent requests, then logout-all, with no JWT in the diagram; then explain what an attacker with a DB dump can and cannot do.
Pass: quiz 9/10, D1 to D12, all tests green, tag `gate/M5.1`.

### DELIVERABLE AND FAILURE MODES
Deliverable: `labs/auth-lab/` with sessions implemented, tests, cracking-time table, JWT forging notes.
Faking it: using a library or AI-generated auth. The module is passing only if you can delete the code and rewrite the session middleware cold.

---

## M5.2 XSS and CSP

Hours: 10. Prerequisite: M5.1. Phase: 5.

### WHY
If an attacker runs JavaScript on your origin, it is their site now. HttpOnly cookies reduce theft but not use. XSS is the reason "tokens in localStorage" is a junior default.

### ATOMS
1. XSS: attacker-controlled data interpreted as code in the victim's browser in your origin. Reflected (in the request/response), stored (persisted and shown to others), DOM-based (client-side sink fed by a source).
2. Sources: URL params, `location.hash`, `postMessage`, storage, API responses, user content. Sinks: `innerHTML`, `outerHTML`, `document.write`, `eval`, `new Function`, `setTimeout(string)`, `<script>`, `href="javascript:..."`, `srcdoc`, `dangerouslySetInnerHTML`, `v-html`.
3. Context-aware output encoding: HTML body, HTML attribute, JavaScript, URL, CSS contexts need different encoding. Use framework auto-escaping (React text nodes) and know where it stops (`dangerouslySetInnerHTML`, `href`, `style`, `src` URLs, `target=_blank`).
4. `textContent` vs `innerHTML`; `setAttribute` safe for most attributes but not event handler or URL attributes with untrusted schemes; validate URL schemes (`http`, `https`, `mailto`) with an allowlist.
5. Sanitization when you must allow HTML (rich text, markdown): DOMPurify with a strict allowlist; sanitize at render time; keep the raw source; never write your own sanitizer.
6. JSON in HTML script tags (`<script>window.__DATA__ = ${JSON.stringify(x)}</script>`): `</script>` and U+2028 hazards; escape `<` as `\u003c`.
7. Mutation XSS (mXSS) and why sanitizer + `innerHTML` can still fail; keep libraries updated.
8. What XSS can do: read non-HttpOnly cookies and storage, make same-origin authenticated requests (CSRF-free), read the DOM, keylog, phish with fake UIs, install service workers. HttpOnly stops cookie theft only.
9. CSP: `Content-Security-Policy` header: allowed sources per directive: `default-src`, `script-src`, `style-src`, `img-src`, `connect-src`, `frame-src`, `frame-ancestors` (replaces X-Frame-Options), `form-action`, `base-uri`, `object-src`, `upgrade-insecure-requests`, `report-to`/`report-uri`.
10. Strict CSP with nonces or hashes: `script-src 'nonce-<random>' 'strict-dynamic'`; a nonce must be unique per response and unguessable; no `'unsafe-inline'`, no host allowlists as the primary defense (JSONP/CDN bypasses).
11. CSP report-only mode for rollout; collect violation reports.
12. CSP is defense in depth, not a substitute for encoding.
13. Trusted Types: browser API that forces sinks to accept only typed, sanitized values (`require-trusted-types-for 'script'`).
14. Related headers: `X-Content-Type-Options: nosniff`, `Referrer-Policy`, `Permissions-Policy`, `Cross-Origin-Opener-Policy`, `Cross-Origin-Embedder-Policy`, `Strict-Transport-Security` (M2.5), `frame-ancestors` for clickjacking.
15. Clickjacking: framing your page and tricking clicks; `frame-ancestors 'none'` or `'self'`.
16. Third-party scripts are XSS you invited; Subresource Integrity (`integrity="sha384-..."`) for static third-party files; supply-chain risk in npm dependencies (lockfiles, `npm audit`, minimal deps).
17. Content sniffing and file uploads: user-uploaded HTML/SVG served from your origin is XSS; serve uploads from a separate domain, with `Content-Disposition: attachment` and `nosniff` (used again in M6.3).
18. Markdown rendering: raw HTML in markdown, `javascript:` links.
19. Cookie scope vs XSS: sibling subdomains can XSS-attack the parent site's cookies if `Domain` is wide.

### RESOURCES (in order)
1. OWASP XSS Prevention Cheat Sheet: https://cheatsheetseries.owasp.org/cheatsheets/Cross_Site_Scripting_Prevention_Cheat_Sheet.html . Full. DOM-based XSS Prevention CS: https://cheatsheetseries.owasp.org/cheatsheets/DOM_based_XSS_Prevention_Cheat_Sheet.html .
2. MDN, Cross-site scripting (XSS): https://developer.mozilla.org/en-US/docs/Web/Security/Attacks/XSS ; MDN CSP guide: https://developer.mozilla.org/en-US/docs/Web/HTTP/Guides/CSP .
3. web.dev, "Mitigate cross-site scripting (XSS) with a strict Content Security Policy": https://web.dev/articles/strict-csp .
4. OWASP Content Security Policy Cheat Sheet: https://cheatsheetseries.owasp.org/cheatsheets/Content_Security_Policy_Cheat_Sheet.html . CSP Evaluator: https://csp-evaluator.withgoogle.com/ .
5. DOMPurify README: https://github.com/cure53/DOMPurify . web.dev "Trusted Types": https://web.dev/articles/trusted-types .
6. PortSwigger Web Security Academy, XSS learning path (free labs): https://portswigger.net/web-security/cross-site-scripting . Do at least 8 labs (reflected, stored, DOM, in attributes, in JS strings, CSP bypass intro).
7. OWASP Cheat Sheets: Clickjacking Defense, HTTP Headers: https://cheatsheetseries.owasp.org/cheatsheets/Clickjacking_Defense_Cheat_Sheet.html ; https://cheatsheetseries.owasp.org/cheatsheets/HTTP_Headers_Cheat_Sheet.html .
8. MDN, Subresource Integrity: https://developer.mozilla.org/en-US/docs/Web/Security/Subresource_Integrity .

### START HERE (first 30 minutes)
1. Threat model: attacker can post a note that other users view.
2. In a page, render `note.body` with `innerHTML`. Post `<img src=x onerror="alert(document.domain)">`. See the alert.
3. Render the same note with `textContent`. Observe it's inert. Write the one-line rule.

### DRILLS
D1. Stored XSS lab: notes rendered via `innerHTML`. Build the attack payload that (a) reads `document.cookie` for non-HttpOnly cookies and `localStorage`, sends them to `evil.test`; (b) performs a same-origin `fetch('/api/notes', {method:'POST'})` with the victim's HttpOnly cookie. Prove (b) works even with HttpOnly.
D2. Fix in layers, one at a time, each proven by re-running the exploit: encode output (`textContent`/React text), HttpOnly cookie, sanitize if HTML is required (DOMPurify with a strict profile), then CSP. Document which layer stopped what.
D3. Reflected and DOM XSS: a search page reflecting `?q=` into HTML, and a page reading `location.hash` into `innerHTML`; exploit each, fix each (encoding; avoid sinks; `URL` validation).
D4. Context table: for 6 contexts (HTML text, quoted attribute, unquoted attribute, JS string inside a script, URL `href`, CSS value), write the payload that breaks naive escaping and the correct encoding; verify each in a browser.
D5. CSP rollout: add `Content-Security-Policy-Report-Only: script-src 'self'; report-to csp`; collect reports at `POST /csp-report`; fix inline scripts using nonces; switch to enforcing `script-src 'nonce-...' 'strict-dynamic'; object-src 'none'; base-uri 'none'; frame-ancestors 'none'`. Acceptance: the stored XSS payload from D1 is blocked by CSP alone (temporarily disable encoding to test), and the console shows the violation.
D6. JSON-in-HTML: server-render `window.__DATA__` with and without escaping; craft a payload `</script><script>alert(1)</script>` in a note title; show the break; fix with `\u003c` escaping.
D7. Security headers: add `nosniff`, `Referrer-Policy: strict-origin-when-cross-origin`, `Permissions-Policy`, `frame-ancestors`; test with https://securityheaders.com or Mozilla Observatory (https://observatory.mozilla.org/) against a deployed copy; reach grade A.
D8. Clickjacking: frame your login page from `evil.test` with an invisible overlay; block with `frame-ancestors`; verify via DevTools console message.
D9. Uploads as XSS: upload an `.html` and an `.svg` containing script; serve from the same origin and see execution; fix with `Content-Disposition: attachment`, `nosniff`, a separate cookieless domain (`usercontent.shop.test`), and a CSP sandbox header (`Content-Security-Policy: sandbox`).
D10. Supply chain: add SRI to a third-party script; tamper with the file and see the browser block it; run `bun audit` or `npm audit` and `osv-scanner`; write your dependency policy (pin, review, minimal).
D11. PortSwigger labs: complete 8 and record in NOTES the technique and lesson of each.

### BREAK
- Use React's `dangerouslySetInnerHTML` with unsanitized input and exploit it.
- Allow `unsafe-inline` in CSP and show the XSS returns.
- Put a user-controlled value in `href` and exploit with `javascript:`.
- Use a CSP host allowlist including a CDN with JSONP and bypass it (read PortSwigger or the CSP Evaluator warning).

### QUIZ (cold)
1. Reflected vs stored vs DOM XSS.
2. What can XSS do against an HttpOnly cookie? What can't it do?
3. Why is output encoding context-dependent?
4. Why not write your own HTML sanitizer?
5. Strict CSP in one sentence: what's a nonce, why `strict-dynamic`?
6. Why is CSP not a substitute for encoding?
7. Where does React's escaping stop?
8. How does a user upload become XSS and how do you stop it?
9. What is Trusted Types for?
10. What do `frame-ancestors` and `nosniff` do?

### GATE
Spoken (4 minutes): XSS vs CSRF in one minute each: who is the attacker, what is the browser doing, what header/cookie/token stops it; then the XSS defenses as layers and what each stops.
Pass: quiz 9/10, D1 to D11, CSP report-only to enforce rollout done, tag `gate/M5.2`.

### DELIVERABLE AND FAILURE MODES
Deliverable: `labs/auth-lab/xss/` exploit and fix pairs; CSP policy file; headers grade screenshot.
Faking it: "React escapes everything". Test: find the 4 places it doesn't.

---

## M5.3 CSRF and Fetch Metadata

Hours: 10. Prerequisite: M5.2. Phase: 5.

### WHY
Cookies are attached automatically. An attacker's site can cause your user's browser to send an authenticated state-changing request. You will build the attack, then the layered defense.

### ATOMS
1. CSRF: the attacker causes the victim's browser to send a request to a target site where the victim has ambient credentials (cookies, HTTP auth, client certs). The attacker cannot read the response, but the side effect happens.
2. Requirements for CSRF: a state-changing action, ambient authentication, predictable request parameters (no secret the attacker can't know).
3. Vectors: auto-submitting `<form method=POST>`, `<img src>` for GET side effects, `fetch` simple requests (no preflight) with `credentials:'include'`, `<script>` and `<link>` loads, cross-site WebSocket handshakes.
4. Why CORS and preflight do not stop it: simple requests are sent without preflight; a form POST needs no CORS; CORS only controls reading the response.
5. Defenses, strongest combination:
   - `SameSite=Lax` (or `Strict`) session cookie: blocks cookies on cross-site POST/subresource requests (not on top-level GET navigation, not same-site attackers).
   - Never mutate on GET/HEAD/OPTIONS.
   - Verify `Origin` (or fallback `Referer`) on unsafe methods against an allowlist.
   - Fetch Metadata: reject unsafe requests where `Sec-Fetch-Site` is `cross-site` (allow `same-origin`, `same-site` only if you want, and `none` for user-initiated navigations to safe methods).
   - Anti-CSRF tokens: synchronizer token (stored in the session, embedded in forms/headers) or signed double-submit cookie (cookie value HMAC'd with session ID); token compared in constant time.
   - Custom request header requirement (`X-Requested-With` or JSON-only `Content-Type`) works because it forces a preflight for cross-site requests, but only if CORS is strict; treat as a supporting layer.
6. Defense in depth: SameSite + Origin/Fetch-Metadata check for all unsafe requests; tokens for the highest-risk forms or if you can't rely on browser versions.
7. Login CSRF: attacker logs the victim into the attacker's account; apply CSRF protection to login too.
8. Fetch Metadata headers: `Sec-Fetch-Site` (`cross-site`, `same-site`, `same-origin`, `none`), `Sec-Fetch-Mode` (`navigate`, `cors`, `no-cors`, `same-origin`), `Sec-Fetch-Dest`, `Sec-Fetch-User`. Forbidden for JavaScript to set; sent by modern browsers (resource isolation policy).
9. Same-site attackers: another subdomain with XSS or a user-content subdomain can bypass SameSite; cookie tossing; this is why `__Host-` and not hosting user content on a sibling subdomain matters.
10. SameSite caveats: `Lax` still sends on top-level GET navigations (so GET must be safe); the Chrome "Lax+POST" 2-minute exception for cookies lacking explicit SameSite; older browsers ignoring it; `None` for cross-site embeds is CSRF-able.
11. Token delivery in SPAs: set a readable CSRF cookie (not HttpOnly) and echo it in a header (`X-CSRF-Token`); or return it from `GET /csrf` (same-origin only).
12. APIs using `Authorization: Bearer` headers are not CSRF-able (not ambient), but the token storage then faces XSS.
13. CSRF and CORS credentialed requests: if you enable CORS with credentials for specific origins, those origins can make requests, so treat them as trusted.
14. Frameworks' defaults: Hono `csrf()` middleware checks `Origin`/`Sec-Fetch-Site`; Next Server Actions compare `Origin` and `Host`; Rails/Django use tokens.
15. Testing CSRF: build the attacker page; automated test sending a request with `Origin: https://evil.test` and no token must return 403.

### RESOURCES (in order)
1. OWASP CSRF Prevention Cheat Sheet: https://cheatsheetseries.owasp.org/cheatsheets/Cross-Site_Request_Forgery_Prevention_Cheat_Sheet.html . Full.
2. MDN, CSRF: https://developer.mozilla.org/en-US/docs/Web/Security/Attacks/CSRF .
3. web.dev, "Protect your resources from web attacks with Fetch Metadata": https://web.dev/articles/fetch-metadata . Full, including the resource isolation policy code.
4. web.dev, "SameSite cookies explained" (re-read) and "Schemeful same-site": https://web.dev/articles/schemeful-samesite .
5. PortSwigger Web Security Academy, CSRF path (free labs): https://portswigger.net/web-security/csrf . Do at least 6 labs (no defenses; token validation depends on method; SameSite Lax bypass via method override; referer-based).
6. Hono CSRF middleware docs and source: https://hono.dev/docs/middleware/builtin/csrf .
7. Google "Fetch Metadata" resource isolation policy: https://web.dev/articles/fetch-metadata#step-5:-reject-all-other-cross-site-and-same-site-but-cross-origin-requests (the algorithm).

### START HERE (first 30 minutes)
1. Threat model: attacker page on `evil.test`, victim logged in at `shop.test` with `SameSite=None; Secure` cookie (the forced vulnerable setup).
2. Write `evil.test/attack.html` with an auto-submitting form to `POST http://shop.test:3000/transfer`. Open it as a logged-in victim. The transfer happens.
3. Write one sentence: why didn't CORS stop it?

### DRILLS
D1. Attack lab: `/transfer` endpoint mutating a balance. Attacks from `evil.test`: auto-submitting form, `fetch` with `credentials:'include'` and `text/plain` body containing JSON (simple request), and `<img>` hitting a (bad) GET endpoint `/transfer?to=...`. Show each succeeds when cookies are `SameSite=None`. Save the server logs.
D2. Defend layer by layer, re-run all attacks after each layer, and record which attacks each layer stops: (a) `SameSite=Lax`; (b) delete the GET mutation; (c) `Origin` allowlist check on unsafe methods; (d) `Sec-Fetch-Site` check (reject `cross-site`); (e) synchronizer CSRF token in forms and `X-CSRF-Token` for fetch; (f) JSON-only with `Content-Type` check.
D3. Same-site attacker: host the attacker page on a sibling subdomain (`evil.shop.test`) with `SameSite=Lax` cookies and an `Origin` check that only checks the registrable domain; show the bypass; fix by checking exact origin and using tokens.
D4. Lax navigation: a top-level GET navigation from `evil.test` to a GET endpoint with a side effect still carries the cookie under Lax; prove it and use it to justify "never mutate on GET".
D5. Token implementation: synchronizer token stored in the session row; embed in HTML forms via a helper; verify with `timingSafeEqual`; rotate on login; per-request tokens are optional; test: missing, wrong, replayed from another session.
D6. Signed double-submit: cookie `__Host-csrf` = `HMAC(secret, sessionId + '.' + random)`; header must match; verify HMAC; show why the unsigned version is vulnerable to cookie tossing from a sibling subdomain.
D7. Login CSRF: attacker auto-submits a login form with the attacker's credentials; victim ends up in the attacker's account; protect login with the Origin/Fetch-Metadata check or a pre-session token.
D8. WebSocket CSRF (from M2.6 D8): confirm your origin check; add auth-ticket pattern (short-lived one-time ticket from an authenticated POST used in the upgrade URL).
D9. Automated tests: a table-driven suite: for each unsafe route, a request with `Origin: https://evil.test`, a request with `Sec-Fetch-Site: cross-site`, a request with a missing/incorrect token must all fail with 403; same-origin must succeed.
D10. PortSwigger CSRF labs: complete 6 and write the lesson of each.

### BREAK
- Remove `SameSite` and rely on CORS to "block" the attack (it doesn't).
- Accept a missing `Origin` header and see a non-browser-looking request path that bypasses the check; decide the policy (browsers always send `Origin` on cross-origin POST; missing may be old clients or curl).
- Implement a token but accept any non-empty value.
- Rely on `Referer` while the attacker page sets `Referrer-Policy: no-referrer` and watch the check fail open.

### QUIZ (cold)
1. What are the three conditions for CSRF?
2. Why don't CORS or preflight stop CSRF?
3. What does `SameSite=Lax` block and not block?
4. What is Fetch Metadata and what request do you reject?
5. Synchronizer token vs signed double-submit?
6. Why must GET never mutate (relate to Lax)?
7. What is login CSRF?
8. How can a same-site attacker bypass SameSite?
9. Why aren't bearer-header APIs CSRF-able, and what's the trade-off?
10. How do you test for CSRF in CI?

### GATE
Spoken (4 minutes): CORS, cookies and CSRF as one story: the browser auto-attaches cookies, the attacker's page triggers a mutating simple request, SameSite reduces it, Fetch Metadata/Origin checks and tokens stop it, CORS is irrelevant because it gates reading not sending.
Pass: quiz 9/10, D1 to D10, tests green, tag `gate/M5.3`.

### DELIVERABLE AND FAILURE MODES
Deliverable: `labs/auth-lab/csrf/` attack pages, the layered defenses, the layer-by-layer table, test suite.
Faking it: adding the `csrf()` middleware and moving on. You must demonstrate each attack failing for the right reason.

---

## M5.4 Authorization and IDOR

Hours: 12. Prerequisite: M5.3. Phase: 5.

### WHY
Broken object-level authorization (BOLA/IDOR) is the number one API vulnerability. "Hidden buttons" are not security. If you have no test proving user A cannot read user B's row, you do not have authorization.

### ATOMS
1. Authorization decisions have three inputs: who (subject), what action, on which resource (and context). Every endpoint must answer: is this subject allowed this action on this specific resource?
2. IDOR/BOLA: changing an ID in a URL/body (`/api/invoices/8` to `/9`) returns or modifies another user's/tenant's object because only authentication was checked.
3. Function-level authorization (BFLA): a normal user calling an admin endpoint; deny by default; authorization checks on the server for every route.
4. Mass assignment: binding client JSON onto a model lets the attacker set `role`, `owner_id`, `workspace_id`, `is_admin`; allowlist writable fields per action (Zod schemas per route, never spread the body into the DB).
5. Tenant scoping: every query for tenant data includes `workspace_id = <membership's workspace>`; fetch by `(id, workspace_id)` together, not `id` alone.
6. Identity vs scope source: derive `workspace_id` from the verified membership (session + URL `:wid` checked against membership), never from a client-supplied body field alone.
7. Response codes: 401 (unauthenticated), 403 (authenticated, not permitted), 404 (hide existence across tenants). Choose per resource and document it.
8. Indirect references: sequential IDs make enumeration easy; random UUIDs reduce guessing but are not authorization (an exposed UUID is still IDOR).
9. Authorization at the data layer: parameterized queries with tenant predicates; repository functions that require a tenant context argument (cannot be called without it); composite FKs (M4.1); later RLS (M5.6).
10. Role model: owner/member/viewer mapped to actions (read, create, update, confirm, cancel, manage members, billing). Write the permission matrix as data and test it exhaustively.
11. Privilege escalation: changing own role, adding oneself to a workspace, removing the last owner (invariant), accepting invitations meant for others.
12. Horizontal (user to user) vs vertical (user to admin) escalation.
13. Rate limiting and enumeration: 404 vs 403 timing and response differences leak existence; uniform responses.
14. Sensitive field-level authorization: not every viewer may see every field (e.g., emails of members); response shaping per role.
15. Bulk and list endpoints: filters must still apply tenant scope; `?workspace_id=` params must be verified; export endpoints; search endpoints; `include` expansions.
16. Authorization on side channels: file downloads, signed URLs, websocket messages, background jobs (jobs must carry and re-verify the actor and tenant), webhooks, cached responses (shared cache keyed by URL only leaks, M2.4).
17. Time-of-check/time-of-use: authz check then a state change in separate transactions can race (role revoked mid-request); do the check in the same transaction or re-check in the write predicate.
18. Auditing: log denied attempts (with request id) for detection.
19. Testing strategy: table-driven matrix tests (actor x action x resource ownership) plus negative tests for every route; a "route inventory" test that fails if a route is registered without an authorization declaration.
20. Authorization is not the UI. Frontend checks are UX only.

### RESOURCES (in order)
1. OWASP API Security Top 10 (2023): https://owasp.org/API-Security/editions/2023/en/0x11-t10/ . Read API1 (BOLA), API3 (Broken Object Property Level Authorization, includes mass assignment), API5 (BFLA) in full; skim the rest now, return in M9.9.
2. OWASP Authorization Cheat Sheet: https://cheatsheetseries.owasp.org/cheatsheets/Authorization_Cheat_Sheet.html . IDOR Prevention CS: https://cheatsheetseries.owasp.org/cheatsheets/Insecure_Direct_Object_Reference_Prevention_Cheat_Sheet.html . Mass Assignment CS: https://cheatsheetseries.owasp.org/cheatsheets/Mass_Assignment_Cheat_Sheet.html .
3. OWASP Top 10 (web): A01 Broken Access Control: https://owasp.org/Top10/A01_2021-Broken_Access_Control/ .
4. PortSwigger Web Security Academy, Access control vulnerabilities (free labs): https://portswigger.net/web-security/access-control . Do at least 8 labs including IDOR, horizontal to vertical escalation, multi-step process bypass, method-based.
5. Hono middleware docs (for composing auth middleware): https://hono.dev/docs/guides/middleware .
6. Zod `strict()`/`pick()`/`omit()` docs for allowlisting fields: https://zod.dev/api .

### START HERE (first 30 minutes)
1. Threat model: attacker is a logged-in user of workspace A who wants workspace B's bookings.
2. Make `GET /bookings/:id` fetch by id only. Log in as a user in workspace A and request a booking id from workspace B. It returns. That is the bug.
3. Fix by adding `AND workspace_id = $membership_workspace`. Request again: 404.

### DRILLS
D1. Route layer: `requireSession` middleware; `loadMembership` middleware that resolves `:wid` from the URL, verifies the session user is a member, and sets `c.var.membership` (role) or returns 404 (not 403, to hide existence; document the policy). Acceptance: no handler reads `workspace_id` from the body.
D2. Data layer: repository functions `getBooking(ctx, id)` where `ctx = {workspaceId, userId, role}` is required, and the SQL always includes `workspace_id = ctx.workspaceId`. A lint-style test greps the repository for queries on tenant tables lacking `workspace_id` and fails.
D3. Permission matrix: `docs/permissions.md` table of actions x roles (owner, member, viewer, anonymous), and `permissions.ts` implementing it as data with a single `can(role, action)`; table-driven tests asserting every cell.
D4. IDOR attack suite (as the attacker): scripts that, logged in as user A, try every ID-bearing endpoint (GET, PUT, PATCH, DELETE) with B's IDs, in bodies, URLs and query params. Acceptance: all fail; script prints a matrix.
D5. Mass assignment: `PATCH /memberships/:id` with a body `{ "role": "owner" }` by a member; `POST /bookings` with `{ "workspace_id": <other>, "created_by": <other user> }`. Show the vulnerable version succeed, then fix with per-route Zod schemas (`strict()`) and derived fields.
D6. Escalation edge cases: a test for each: member can't promote self; can't add self to another workspace; removing the last owner fails (enforce in a transaction with a `SELECT ... FOR UPDATE` on the owners set or a deferred constraint); invites can only be accepted by the invited email.
D7. Uniform responses: for existing-but-forbidden vs nonexistent resources return identical 404 bodies and compare response times over 1,000 samples (should be statistically indistinguishable).
D8. List endpoints: `GET /workspaces/:wid/bookings?workspace_id=other` ignored; `?include=members` respects permissions; CSV export endpoint requires `export` permission; search endpoint is tenant scoped.
D9. TOCTOU: simulate revoking a member's role during a long request; show a stale check allowing the write; fix by performing the authorization predicate inside the write statement (`... WHERE EXISTS (select 1 from memberships where user_id=$1 and workspace_id=$2 and role in ('owner','member'))`) or the same transaction.
D10. Route inventory test: a test that enumerates registered Hono routes and fails if any route lacks an `authz` annotation (public, session, or role-based); add a deliberate unprotected route to see it catch it.
D11. PortSwigger access control labs: 8 labs.
D12. The proof test file `authz.test.ts`: "A cannot read B's booking; viewer cannot confirm; owner can; member can't change roles" in a real-DB integration test. Show the test failing against the vulnerable version.

### BREAK
- Remove the tenant predicate from one query and show the leak, then the test catching it.
- Turn sequential ids into UUIDs and show IDOR still works when an id leaks via a different endpoint.
- Make a handler trust a hidden form field `role=member`.
- Have a background job accept a `workspace_id` from its payload without checking that the enqueuing actor was authorized.

### QUIZ (cold)
1. Define IDOR/BOLA and BFLA.
2. Why isn't hiding the button security?
3. What is mass assignment and its fix?
4. 401 vs 403 vs 404-to-hide?
5. Where should `workspace_id` come from?
6. Why aren't UUIDs authorization?
7. How can authz checks race (TOCTOU) and how do you fix it?
8. What must a background job carry and verify?
9. How do you test that a route can't be added without authz?
10. Name three non-obvious places authorization is forgotten.

### GATE
Spoken (4 minutes): walk the path of `GET /workspaces/:wid/bookings/:id` and name every authorization check; explain the IDOR test matrix.
Pass: quiz 9/10, D1 to D12, `authz.test.ts` green and shown failing on the vulnerable variant, tag `gate/M5.4`. If there is no test proving IDOR is closed, you do not have authorization and the gate fails.

### DELIVERABLE AND FAILURE MODES
Deliverable: `labs/auth-lab/authz/` with middleware, repository layer, permission matrix doc, test suite.
Faking it: relying on "we use `where user_id = ...` somewhere". Test: grep proof plus the route inventory test.

---

## M5.5 OAuth 2.0 and OIDC

Hours: 12. Prerequisite: M5.4. Phase: 5.

### WHY
"Sign in with Google" is everywhere and confusingly explained. You will trace the redirects by hand, then use a maintained library, and end with your own session cookie.

### ATOMS
1. OAuth 2.0 is delegated authorization: a user lets a client app access resources on a resource server, without sharing their password. Roles: resource owner (user), client (your app), authorization server (Google), resource server (Google API).
2. OpenID Connect (OIDC) layers authentication on top: adds the ID token (a JWT with claims about the user and the authentication event), the `openid` scope, the UserInfo endpoint, discovery document, and standard claims (`sub`, `email`, `email_verified`, `name`, `picture`).
3. Authorization Code flow with PKCE is the standard for web apps, SPAs and mobile. Implicit flow and Resource Owner Password Credentials are deprecated.
4. Flow steps: (a) your app redirects the browser to the authorization endpoint with `client_id`, `redirect_uri`, `response_type=code`, `scope`, `state`, `code_challenge`, `code_challenge_method=S256`, `nonce` (OIDC); (b) the user authenticates and consents; (c) the AS redirects back to `redirect_uri?code=...&state=...`; (d) your server verifies `state`, then POSTs the code plus `code_verifier` (and client secret for confidential clients) to the token endpoint; (e) receives `access_token`, `id_token`, optional `refresh_token`; (f) verifies the ID token; (g) upserts a local user and creates your own session.
5. `state`: random value tied to the browser session, verified on callback; prevents CSRF on the callback (login CSRF/code injection).
6. PKCE: `code_verifier` random; `code_challenge = BASE64URL(SHA256(verifier))`; protects against authorization code interception, required for public clients, recommended for all.
7. `nonce`: ties the ID token to the session; prevents token replay.
8. Redirect URI: exact string match registered at the provider; open redirect via loose matching is a classic vulnerability.
9. Tokens: access token (to call APIs; short-lived; opaque or JWT), refresh token (get new access tokens; long-lived; store server-side encrypted), ID token (for the client, proves who the user is; verify signature via JWKS, `iss`, `aud`, `exp`, `nonce`). Do not use the ID token as an API access token and do not use an access token as proof of login.
10. Scopes: `openid email profile`; request the minimum.
11. Where tokens live: server-side only for web apps (BFF pattern); never in localStorage; your app issues its own HttpOnly session cookie after login (M5.1) rather than using Google's token as your session.
12. Account linking policy: the same email via password and via Google. Only link if `email_verified` is true and the existing account is verified; ask the user to authenticate the existing account to link; never auto-merge on an unverified email (account pre-hijacking). Use the `(provider, sub)` pair as the stable identity, not the email.
13. Identity table: `identities(provider, provider_user_id, user_id, email, created_at, unique(provider, provider_user_id))`.
14. Client types: confidential (server can keep a secret) vs public (SPA/mobile; cannot).
15. Other grants: client credentials (service-to-service), device code (TVs/CLIs), refresh token rotation; know them.
16. OAuth security best current practice (RFC 9700): exact redirect matching, PKCE everywhere, no implicit, no tokens in URLs, refresh token rotation, sender-constrained tokens (DPoP) as concept.
17. Mix-up attacks, authorization code injection, open redirects, CSRF on callback; how each is mitigated.
18. Logout: local session revocation always; provider logout (RP-initiated logout) optional; "sign out of Google" is not "sign out of your app".
19. Using libraries: Arctic (provider-specific OAuth clients), `openid-client` (certified), Hono OAuth providers; know what each does for you (discovery, PKCE, state, token exchange, JWKS verification).
20. Provider setup: create a project, OAuth consent screen, credentials (client ID and secret), authorized redirect URIs for local and prod.
21. SAML and enterprise SSO exist (know the name and when they appear: B2B customers); not drilled.

### RESOURCES (in order)
1. oauth.net/2 overview pages: https://oauth.net/2/ ; "OAuth 2.0 Simplified" by Aaron Parecki: https://www.oauth.com/ . Read: Introduction, Roles, Authorization Code flow, PKCE, Redirect URIs, Security.
2. RFC 6749 (OAuth 2.0): https://www.rfc-editor.org/rfc/rfc6749 sections 1 and 4.1 only; RFC 7636 (PKCE): https://www.rfc-editor.org/rfc/rfc7636 sections 1 and 4.
3. OpenID Connect Core 1.0, sections 1 to 3 (intro, authentication, code flow) and 3.1.3.7 (ID token validation): https://openid.net/specs/openid-connect-core-1_0.html .
4. RFC 9700, OAuth 2.0 Security Best Current Practice: https://www.rfc-editor.org/rfc/rfc9700 . Read the "Summary of recommendations" and attacks sections.
5. Okta Developer, "An Illustrated Guide to OAuth and OpenID Connect" (video, 15 min): https://www.youtube.com/watch?v=t18YB3xDfXI .
6. Google Identity docs, "OpenID Connect": https://developers.google.com/identity/openid-connect/openid-connect . Arctic docs: https://arcticjs.dev/ (or `arctic.js.org`; search the current URL) and its Google provider page.
7. JWT validation checklist: RFC 8725 (JWT Best Current Practices): https://www.rfc-editor.org/rfc/rfc8725 .
8. PortSwigger OAuth labs: https://portswigger.net/web-security/oauth . Do 4 labs.

### START HERE (first 30 minutes)
1. Create a Google Cloud project and an OAuth client (Web application). Add redirect URI `http://localhost:3000/auth/google/callback`.
2. Fetch the discovery document: `curl https://accounts.google.com/.well-known/openid-configuration | jq`. Write down `authorization_endpoint`, `token_endpoint`, `jwks_uri`.
3. Open the authorization URL (hand-built with a fake `state` and PKCE challenge) in the browser; observe the redirect with `?code=`.

### DRILLS
D1. By hand, no library: implement `/auth/google/start` (generate `state`, `code_verifier`, `nonce`; store in a short-lived signed cookie or server session; redirect) and `/auth/google/callback` (verify `state`, exchange code with `fetch`, validate the ID token's signature with Google's JWKS via `jose`, check `iss`, `aud`, `exp`, `nonce`, `email_verified`). Acceptance: you can log in and see your Google `sub` in the DB.
D2. Draw the sequence diagram with real redirect URLs and parameters (redact secrets) in NOTES (a mermaid diagram). Identify each browser redirect vs each server-to-server call.
D3. Replace the hand-rolled code with Arctic or `openid-client` and diff what the library did for you (list at least 6 things).
D4. Identities and linking: schema `identities`; policy implemented: (1) new Google user with unverified email: reject; (2) Google email matches an existing verified password account: require the user to log in with the password first, then link; (3) never merge by email alone. Tests for each case.
D5. After callback: upsert the user, create **your** session row and cookie exactly like password login; no Google tokens in the browser; store the refresh token (if requested) encrypted at rest using AES-256-GCM with a key from env; logout revokes your session.
D6. Attack lab: (a) skip `state` verification and perform a login CSRF using an attacker-initiated authorization code; (b) accept a loose `redirect_uri` (your own bad registration with a wildcard on a test provider such as a local Keycloak/`mock-oauth2-server`) and steal the code; (c) skip PKCE and demonstrate code interception with a malicious local listener (simulated). Then fix each.
D7. Local identity provider: run Keycloak or `navikt/mock-oauth2-server` in Docker; point your app at it by configuration only (discovery URL), proving your implementation is provider-agnostic.
D8. Token handling: write a table of each token type: purpose, audience, lifetime, where stored, who may read it, how revoked. Build a refresh-token rotation demo against the mock server.
D9. Add GitHub as a second provider; handle its quirks (email may be private; use the `/user/emails` endpoint; no OIDC ID token, use the userinfo API).
D10. PortSwigger OAuth labs: complete 4 and note the lesson.

### BREAK
- Remove the `nonce` check and replay an ID token.
- Use the access token's `email` claim without `email_verified`.
- Auto-link by email and demonstrate pre-hijacking: attacker registers `victim@x.com` by password (unverified) before the victim signs in with Google, retaining access.
- Store the ID token in `localStorage` and read it through your XSS lab.

### QUIZ (cold)
1. OAuth vs OIDC: what does each provide?
2. Walk through Authorization Code + PKCE step by step.
3. What do `state`, PKCE and `nonce` each prevent?
4. ID token vs access token vs refresh token?
5. What must you validate in an ID token?
6. Why exact-match redirect URIs?
7. Why create your own session after OAuth login?
8. What's the safe account-linking policy and what's pre-hijacking?
9. Which grants are deprecated and why?
10. What does a library save you from?

### GATE
Spoken (6 minutes): draw the full Authorization Code + PKCE sequence with real parameter names, and the moment your own session cookie is issued; explain the three attacks (CSRF on callback, code interception, redirect URI abuse) and each defense.
Pass: quiz 9/10, D1 to D10, tag `gate/M5.5`.

### DELIVERABLE AND FAILURE MODES
Deliverable: `labs/auth-lab/oauth/` with hand-rolled and library versions, sequence diagram, linking tests.
Faking it: using a hosted auth product. Not until you can reproduce the flow yourself.

---

## M5.6 RBAC, ABAC, ReBAC, RLS, secrets (Phase 5 exit)

Hours: 14. Prerequisite: M5.5. Phase: 5.

### WHY
You built authorization for one app. This module is the vocabulary and the patterns for when the org, tenants, or number of services grows: the gap between "built an auth system" and "can design authz for a company".

### ATOMS
Models
1. RBAC: permissions attached to roles, roles to users. Simple, auditable; breaks when permissions depend on resource attributes ("only their own drafts") or deep sharing.
2. ABAC: decisions from attributes of subject, resource, action and environment (time, IP, MFA state). Expressive; harder to audit; rules live in code/policy.
3. ReBAC: access derived from relationships in a graph (Google Zanzibar: tuples `object#relation@user`, relation rewriting, groups-of-groups, folder inheritance). Fits Drive/GitHub-style sharing.
4. ACLs per object; capability tokens (signed URLs); ownership checks.
5. Policy as code and centralization: one `can(subject, action, resource)` function or policy engine (OpenFGA, Cerbos, Oso, Cedar, OPA) with tests; decision logging; "explain why denied".
6. Default deny; explicit allow; precedence of deny rules; avoid role explosion (50 roles) by moving to attributes.
7. Authorization caching: cache decisions only with correct invalidation; short TTL; revocation latency is a business decision.
8. Permissions in tokens vs checked live: embedding roles in a session/JWT makes revocation lag.

Multi-tenancy
9. Isolation patterns: shared tables with `tenant_id` (cheapest, one missing `WHERE` leaks); schema per tenant (stronger isolation, migration fan-out); database per tenant (strongest, ops-heavy; regulated data); cell-based architecture.
10. Noisy neighbor, per-tenant limits, quotas; per-tenant encryption keys for enterprise.
11. Tenant resolution: subdomain, path, header, session; where each can be spoofed.
12. Cross-tenant admin ("support") access and break-glass auditing.

Postgres RLS and roles
13. Row-Level Security: `ALTER TABLE ... ENABLE ROW LEVEL SECURITY`; `CREATE POLICY name ON t FOR SELECT/INSERT/UPDATE/DELETE USING (...) WITH CHECK (...)`; policies apply to non-owner roles (table owners and superusers bypass unless `FORCE ROW LEVEL SECURITY`); the application sets context per transaction via `SET LOCAL app.current_workspace = '...'` (or `set_config(..., true)`), read in the policy with `current_setting('app.current_workspace')::bigint`.
14. RLS is defense in depth: even a query missing `WHERE workspace_id = ...` returns only permitted rows. It is not a replacement for application authz (and "Supabase RLS and hope" as the only layer fails the gate).
15. RLS pitfalls: performance (policy predicates need indexes), `SECURITY DEFINER` functions bypassing it, views run as owner, pooling with transaction mode requires `SET LOCAL` inside the transaction, forgetting `FORCE`, policies combining with OR (permissive) vs AND (restrictive).
16. Least-privilege database roles: separate roles for migrations (owner), app (DML on specific tables), read-only reporting, and admin; no `SUPERUSER`/`CREATEDB`/`CREATEROLE` for the app; `GRANT ... ON TABLE ... TO app`; `REVOKE ALL ... FROM PUBLIC`; default privileges; schema `public` locked down.
17. SQL injection blast radius shrinks with least privilege.

Secrets and service auth
18. Secrets: API keys, DB passwords, signing keys, OAuth client secrets, encryption keys. Never in git, images or logs; `.env` is fine for local only; production uses the platform's secret store or a vault (HashiCorp Vault, AWS Secrets Manager, GCP Secret Manager, Doppler, SOPS).
19. Rotation: design every secret to be rotatable without downtime (two valid keys during overlap; `kid` in tokens); secret scanning (gitleaks, GitHub secret scanning); what to do when one leaks (rotate first).
20. Envelope encryption and KMS: data keys encrypted by a master key; application-level encryption of sensitive columns; key separation; what encryption at rest does and doesn't protect.
21. Service-to-service authentication: mTLS (mutual certs), static API keys (weak rotation), short-lived signed tokens (JWT from an internal issuer, workload identity), network policy; do not reuse user session cookies for services.
22. Least privilege for deploy credentials: CI tokens scoped; separate who can deploy from who can read prod secrets.
23. Audit logging: append-only, includes actor, action, resource, result, request id; tamper evidence; retention.
24. Data protection basics you will be asked about: PII minimization, GDPR-style deletion/export, encryption in transit and at rest, backups containing deleted data.

### RESOURCES (in order)
1. Google, "Zanzibar: Google's Consistent, Global Authorization System" (USENIX ATC 2019): https://research.google/pubs/zanzibar-googles-consistent-global-authorization-system/ . Read the abstract, sections 1 to 2 (motivation, model, namespace configuration) and skip the distributed systems internals unless curious.
2. NIST SP 800-162, Guide to ABAC: https://csrc.nist.gov/pubs/sp/800/162/final . Read the executive summary and section 2 definitions.
3. OWASP Authorization Cheat Sheet (re-read) and OWASP Multi-Tenant Application Security (search "OWASP multi-tenant cheat sheet"): https://cheatsheetseries.owasp.org/ .
4. PostgreSQL docs, Row Security Policies: https://www.postgresql.org/docs/current/ddl-rowsecurity.html . Full. And `CREATE POLICY`: https://www.postgresql.org/docs/current/sql-createpolicy.html . Database Roles: https://www.postgresql.org/docs/current/user-manag.html ; Privileges: https://www.postgresql.org/docs/current/ddl-priv.html .
5. OpenFGA docs, concepts and "Modeling": https://openfga.dev/docs/concepts ; Cerbos or Oso intro (read one): https://docs.cerbos.dev/ .
6. OWASP Secrets Management Cheat Sheet: https://cheatsheetseries.owasp.org/cheatsheets/Secrets_Management_Cheat_Sheet.html ; The Twelve-Factor App, III. Config: https://12factor.net/config .
7. HashiCorp Vault "What is Vault?" concepts and AWS KMS envelope encryption docs (concepts only): https://developer.hashicorp.com/vault/docs/what-is-vault ; https://docs.aws.amazon.com/kms/latest/developerguide/concepts.html#enveloping .
8. Gitleaks README: https://github.com/gitleaks/gitleaks .

### START HERE (first 30 minutes)
1. Write three real requirements in NOTES from your domain: "owner can do everything", "a member can edit a booking only if they created it and it's still draft", "a viewer of a shared resource group can see all resources nested under it".
2. For each, label RBAC, ABAC or ReBAC, and say why.

### DRILLS
D1. Centralize: refactor M5.4's checks into one `can(user, action, resource)` module with exhaustive tests; every handler calls it; a lint/test enforces no inline role checks elsewhere.
D2. ABAC rule: "a member may edit a booking only if `created_by = user.id AND status = 'draft'`; owners may edit any draft in the workspace". Tests: owner edits another's draft (allowed), member edits another's draft (denied), member edits own confirmed booking (denied).
D3. ReBAC mini-implementation: tables `resource_groups(id, parent_id)`, `group_members(group_id, user_id, relation)`; implement "can view resource" as a recursive CTE (M4.5) over group ancestry; test deep nesting and a cycle guard. Then model the same in OpenFGA locally (Docker) and compare.
D4. RLS: enable RLS on `bookings` with a policy `USING (workspace_id = current_setting('app.current_workspace')::bigint)` and `WITH CHECK` as well; the app sets `SET LOCAL app.current_workspace` per request transaction; connect as a non-owner role. Write a deliberately "forgetful" query without a tenant `WHERE` and prove RLS still prevents the leak. Test `INSERT` of a row for another tenant is rejected by `WITH CHECK`.
D5. Remove the policy (or connect as the owner role) and show the leak returns, proving RLS was doing the work; add `FORCE ROW LEVEL SECURITY` and discuss when the owner should bypass.
D6. RLS performance: with 1M bookings and 100 tenants, compare plans with and without the policy; ensure an index on `workspace_id` makes policy checks cheap; record numbers.
D7. Least-privilege roles: create `slotlock_migrator` (owner of the schema objects), `slotlock_app` (SELECT/INSERT/UPDATE/DELETE on listed tables only; no DDL), `slotlock_readonly`; revoke `PUBLIC` defaults; connect as app and attempt `DROP TABLE bookings`, `CREATE TABLE`, `SELECT` on a table not granted: all fail. Migration runs as `migrator`, app as `app`.
D8. `docs/authz-model.md`: one page: which model for which decision and why; the tenant isolation pattern chosen and the cost of the alternatives; the RLS policy list; the role list; revocation latency policy.
D9. Secrets: add gitleaks as a pre-commit hook; deliberately commit a fake AWS key and see it blocked; write the rotation runbook for the session-signing/CSRF HMAC secret with a two-key overlap (accept `kid` A and B, sign with B) and implement it; run the rotation with traffic flowing.
D10. Envelope encryption: encrypt the OAuth refresh token column with a per-row data key wrapped by a master key from env (AES-256-GCM via `crypto`); implement key rotation (rewrap) without decrypting data.
D11. Audit log: an append-only `audit_log` table (revoke UPDATE/DELETE from the app role); log logins, role changes, permission denials, exports with `request_id`; write a query that shows "what did user X do last week".
D12. Service auth thought lab: two Hono services where service B accepts requests only from A: implement (a) a static key with rotation, (b) a short-lived HMAC-signed token with `iat`/`exp`/`aud`; write when you'd choose mTLS.

### BREAK
- Use `SECURITY DEFINER` on a function that reads bookings and see it bypass RLS.
- Set `app.current_workspace` with a session-level `SET` through a transaction pooler and watch it leak across requests; fix with `SET LOCAL`.
- Give the app role `SUPERUSER` and show a SQL injection becomes full database compromise.
- Leave a private key in a git history; rotate and purge (and note rotation is mandatory regardless).

### QUIZ (cold)
1. RBAC vs ABAC vs ReBAC with one example each matched correctly.
2. What does RLS add on top of correct app authz, and why is "RLS alone" still the hope answer?
3. Name three RLS pitfalls.
4. What does the least-privilege app role look like?
5. Three tenant isolation patterns and their costs.
6. How do you design a rotatable signing secret?
7. What does envelope encryption do?
8. How do roles in a JWT make revocation lag?
9. Why centralize the policy?
10. What belongs in an audit log?

### PHASE 5 EXIT TEST (paper, 40 minutes, cold)
Design, on paper, with diagrams where needed:
1. `POST /orders` that cannot double-charge on retry (idempotency key; you implement it in M6.1).
2. A listing endpoint that stays correct when rows are inserted during pagination (cursor; M6.2).
3. Session login plus "log out all devices", and the cookie attributes.
4. An IDOR test plan for a booking API with three roles.
5. A CSRF defense stack for a cookie-session app and why CORS is irrelevant.
6. The OAuth login flow ending in your own session cookie.
7. The authz model for owner/member/viewer plus "edit own draft" plus nested group sharing.
If any answer is "use Supabase/Clerk/RLS and hope", you fail.

### GATE
Spoken (5 minutes): explain RBAC vs ABAC vs ReBAC with concrete examples, what RLS adds as defense in depth, and the tenant isolation trade-offs.
Pass: quiz 9/10, D1 to D12, Phase 5 exit passed, tags `gate/M5.6` and `gate/phase5`.

### DELIVERABLE AND FAILURE MODES
Deliverable: sessions, OAuth traced, IDOR tests, centralized `can`, one ABAC rule, one RLS policy, least-privilege roles, `docs/authz-model.md`, rotation runbook.
Faking it: reading about Zanzibar without implementing a toy ReBAC. The recursive-CTE ReBAC is the proof.

---

# PHASE 6. API AS A PRODUCT

The `slotlock` repo is created at the start of M6.1: new public repo, copy the lab schema and auth, `NOTES.md` at the root, `docs/` folder. From here, labs stay closed unless a gate fails. Before M6.1 you may not touch `slotlock`.

---

## M6.1 Resources, errors, validation, idempotency, rate limits

Hours: 16. Prerequisite: M5.6. Phase: 6.

### WHY
API design is product design plus HTTP semantics. Retries, double-clicks and flaky networks are normal; idempotency turns "it ran twice" from a double charge into a non-event.

### ATOMS
Resource design
1. Resources are nouns with stable URLs; collections `/workspaces/:wid/bookings`, items `/workspaces/:wid/bookings/:id`; plural nouns; nesting only as deep as ownership (max two levels).
2. Actions that are not CRUD: sub-resource or action endpoints (`POST /bookings/:id/confirm`, `POST /bookings/:id/cancel`) that change lifecycle state; the lifecycle (`draft` to `confirmed` to `cancelled`, later `no_show`) is a state machine enforced in one place with legal-transition table tests.
3. REST when it fits, RPC (tRPC, server functions) when your verbs aren't resources; honesty beats fashion; no GraphQL.
4. Representations: JSON field naming (camelCase or snake_case, consistent), ISO 8601 UTC timestamps with `Z`, money as integer minor units plus currency code (or strings for decimals), IDs as strings, enums as strings, no leaking internal ids or DB column names.
5. `PUT` replace vs `PATCH` partial (JSON Merge Patch RFC 7396 vs JSON Patch RFC 6902); which to support and why.
6. Create returns `201` + `Location` + the representation; delete returns `204`.
7. Conditional requests/optimistic concurrency at the API: `ETag`/`If-Match` or a `version` field and `409`/`412` (M2.4, M4.3).
8. Compatibility over versioning: add fields, don't remove or change meaning; tolerate unknown fields; version only on breaking changes (path `/v2` or header); deprecation with `Sunset`/`Deprecation` headers.

Validation and errors
9. Validation at the boundary with Zod: parse body, query, params, headers; reject unknown fields (`strict`); coerce query numbers explicitly; validate with the schema as source of truth and the TypeScript type derived.
10. Validation is a security boundary, not a type convenience: size limits (body, array length, string length), numeric ranges, enum allowlists, date ordering, UUID formats.
11. Error contract (RFC 9457 Problem Details or your own stable shape): `{ "error": { "code": "BOOKING_OVERLAP", "message": "...", "details": {...}, "requestId": "..." } }`; stable machine-readable `code`; human `message` safe to display; field errors as `details.fields`.
12. Status mapping: 400 malformed, 401, 403, 404, 409 conflicts (overlap, version, duplicates), 415, 422 validation, 429, 500 (log; generic body), 503.
13. Never leak stack traces, SQL, or internal paths; log them with the request id.
14. A global error handler maps thrown `AppError`s and Zod errors to the contract; unknown errors become 500 and alert.
15. Idempotent deletes and "already exists" semantics: decide per endpoint and document.

Idempotency
16. Idempotency key: client generates a unique key per logical operation (UUID) and sends `Idempotency-Key`; server stores key, request fingerprint, and the response; a retry with the same key and same payload replays the stored response; same key with a different payload is a 422/409 error.
17. Implementation in Postgres: table `idempotency_keys(user_id, key, request_hash, status, response_code, response_body, created_at, locked_at)` with `PRIMARY KEY (user_id, key)`; insert-or-lock at the start in the same transaction as the work (or a staged approach); concurrent duplicates serialize on the unique key (one proceeds, others wait or get 409).
18. Scope the key per user/tenant and per endpoint; TTL (24h or so); recovery if the process dies mid-request (lock timeout and resume points: Stripe-style atomic phases).
19. Where to apply: `POST` creates, payments, confirm, anything with external side effects; not needed for naturally idempotent `PUT`/`DELETE`.
20. Client side: disabling the button is UX, not idempotency; generate the key when the form mounts (or on user intent) and reuse it for retries.
21. Natural idempotency: constraints (`UNIQUE`), upserts, state machine transitions that are no-ops when repeated (confirming an already confirmed booking returns the same result).
22. Exactly-once is a myth over networks; target at-least-once delivery with idempotent handling.

Rate limiting and abuse
23. Why: protect availability, cost, and accounts. Per-IP, per-user, per-token, per-tenant, per-route limits; stricter on auth endpoints.
24. Algorithms: fixed window (burst at boundaries), sliding window log/counter, token bucket (burst plus refill), leaky bucket, concurrency limits.
25. Storage: in-memory per instance (inexact when scaled), Postgres (simple), Redis (standard; not yet allowed here, so Postgres with `UPDATE ... RETURNING` or an in-process limiter for the lab).
26. Responses: `429 Too Many Requests` with `Retry-After`; `RateLimit-*` headers (IETF draft `RateLimit` and `RateLimit-Policy`); client backoff with jitter.
27. Identify the client behind proxies correctly (`X-Forwarded-For` trust list), otherwise attackers spoof their IP.
28. Payload and complexity limits: body size, JSON depth, list sizes, query timeouts, pagination caps (M6.2).
29. Request ID and correlation: `X-Request-Id` middleware (M2.1) included in every error body and log line.
30. CORS policy for the API (explicit allowlist) and security headers on API responses (`nosniff`, `Cache-Control: no-store` on authenticated JSON).

### RESOURCES (in order)
1. Zod docs: https://zod.dev/ . Read: objects (`strict`, `pick`, `omit`, `partial`), unions, `refine`/`superRefine`, `coerce`, `transform`, error formatting.
2. RFC 9457, Problem Details for HTTP APIs: https://www.rfc-editor.org/rfc/rfc9457 . Full (short).
3. Stripe API docs, "Idempotent requests" and "Errors": https://docs.stripe.com/api/idempotent_requests ; https://docs.stripe.com/api/errors . Brandur Leach, "Implementing Stripe-like Idempotency Keys in Postgres": https://brandur.org/idempotency-keys . Full; this is the implementation reference for D4.
4. IETF HTTPAPI draft, "The Idempotency-Key HTTP Header Field": https://datatracker.ietf.org/doc/draft-ietf-httpapi-idempotency-key-header/ .
5. Google API Design Guide (resource-oriented design, standard methods, errors): https://cloud.google.com/apis/design . Microsoft REST API Guidelines: https://github.com/microsoft/api-guidelines/blob/vNext/azure/Guidelines.md (skim) .
6. Arnaud Lauret, *The Design of Web APIs* (2nd ed.), chapters 1 to 7 (goals, resources and actions, data, errors, documentation). https://www.manning.com/books/the-design-of-web-apis-second-edition . If you don't have the book, read the Google guide in full and the OWASP API Top 10 sections on resource consumption.
7. RFC 6585 (429) https://www.rfc-editor.org/rfc/rfc6585 ; RFC 9110 `Retry-After` section 10.2.3; IETF draft "RateLimit header fields for HTTP": https://datatracker.ietf.org/doc/draft-ietf-httpapi-ratelimit-headers/ .
8. OWASP API Security Top 10 2023: API4 (Unrestricted Resource Consumption), API6 (Unrestricted Access to Sensitive Business Flows): https://owasp.org/API-Security/editions/2023/en/0x11-t10/ .
9. RFC 7396 (JSON Merge Patch) https://www.rfc-editor.org/rfc/rfc7396 ; RFC 6902 (JSON Patch) https://www.rfc-editor.org/rfc/rfc6902 (skim).
10. Token bucket explanation: Stripe's "Scaling your API with rate limiters" blog: https://stripe.com/blog/rate-limiters .

### START HERE (first 30 minutes)
1. Create the `slotlock` repo (public, with README and an MIT or similar license). `git init`, push. Add `NOTES.md` and `docs/api.md` stubs.
2. Copy `auth-lab` session code and `pg-lab` migrations in as the first commit (history is clean; note the origin in NOTES).
3. Write the first two sentences of `docs/api.md`: the base URL, the error shape.

### DRILLS
D1. `docs/api.md` (the contract): every route with method, path, auth (public/session/token), required role, request schema, success codes, error codes, idempotency requirement, rate limit; the error shape; the pagination shape (M6.2 fills in). This file is written before the code for each route.
D2. Validation layer: a `validate({body, query, params})` Hono middleware using Zod that produces 422 with `details.fields`; `strict()` schemas on every mutating route; size limits (body 100KB, string lengths, array lengths). Tests: 15 invalid payloads including unknown fields, negative numbers, `end_at <= start_at`, huge arrays, wrong content types, malformed JSON (400).
D3. Error handler: `AppError` classes with `code`, `status`, `details`; global handler maps Zod/DB errors (unique `23505`, exclusion `23P01`, FK `23503`, check `23514`, serialization `40001`) to contract errors; 500s return a generic body with the request id; a test proves no stack/SQL leaks.
D4. Idempotency: implement `Idempotency-Key` on `POST /workspaces/:wid/bookings` and `POST /bookings/:id/confirm` using the Postgres table and the staged approach from Brandur's article. Tests: 20 concurrent identical requests produce exactly one row and 20 identical responses; same key with a different body returns 422; a crash simulated between phases resumes correctly; key scoped per user; keys expire.
D5. Lifecycle state machine: `draft -> confirmed -> cancelled` (and `confirmed -> no_show`), transitions in one function with a table of legal moves; illegal transitions return 409 `INVALID_TRANSITION`; confirming twice is idempotent (200 and the same body); overlap prevented by the exclusion constraint (M4.3) and mapped to `409 BOOKING_OVERLAP`. Tests include 50 concurrent confirms of conflicting bookings.
D6. Optimistic concurrency at the API: `ETag` on `GET /bookings/:id`; `PATCH` requires `If-Match` (428 if missing, 412 on mismatch). Test with two clients.
D7. Rate limiter: implement token bucket keyed by `(route class, user or IP)`; auth routes stricter; Postgres-backed or in-process behind an interface; `429` with `Retry-After` and `RateLimit-*` headers; tests with a fake clock; a load script proves the limit under concurrency. Correct client IP extraction using a configured trusted-proxy count.
D8. API hygiene middleware stack in order, documented: request id, logging (M9.1 later), security headers, CORS allowlist, body limit, rate limit, auth, authz, validation, handler, error handler. Tests for ordering invariants (e.g., rate limiting before expensive auth).
D9. JSON conventions doc: field naming, timestamps, money, ids, null vs absent, enum policy, unknown field policy; implement shared serializers and tests (a `serializeBooking` function; no raw DB rows leave the API).
D10. Compatibility exercise: add a new optional field to a response and a new optional request field; run an "old client" test fixture proving nothing breaks; write a deprecation of a field with `Deprecation` and `Sunset` headers.
D11. Client retry simulation: a script acting as a flaky client that retries POSTs with and without keys under injected failures (timeouts after the server committed); count duplicates in each mode.

### BREAK
- Remove the idempotency key table's unique constraint and show duplicates under concurrency.
- Replay a key with a different body and see the confusion if unchecked.
- Return 500 with the raw error message and find the SQL in the body; fix.
- Make the rate limiter keyed by `X-Forwarded-For` blindly and bypass it with a spoofed header.
- Kill the server between inserting the booking and storing the idempotent response; observe the retry's behavior in each design.

### QUIZ (cold)
1. When is REST enough and when is RPC honest?
2. How do you model `confirm` and why not `PUT status`?
3. What goes in an error contract and what must never?
4. 409 vs 422 vs 400 for three concrete cases.
5. How does an idempotency key table work end-to-end, including concurrent duplicates?
6. Why is a disabled button not idempotency?
7. What does a token bucket do compared to a fixed window?
8. Why is `X-Forwarded-For` dangerous to trust blindly?
9. Compatibility vs versioning: what changes are non-breaking?
10. Why must validation limit sizes?

### GATE
Spoken (6 minutes): design `POST /orders` that can't double-charge on retry, covering client key generation, server table, concurrent duplicates, crash mid-request and response replay.
Pass: quiz 9/10, D1 to D11, `docs/api.md` committed, 20-concurrent-duplicate test green, tag `gate/M6.1`.

### DELIVERABLE AND FAILURE MODES
Deliverable: `slotlock` with contract doc, validation, error handling, idempotency, lifecycle, rate limiting.
Faking it: copying an idempotency middleware package. The Postgres implementation with crash recovery is the module.

---

## M6.2 Pagination, filtering, ORM peek

Hours: 10. Prerequisite: M6.1. Phase: 6.

### WHY
Offset pagination breaks under concurrent writes and gets slower as pages grow. Cursor (keyset) pagination is correct and fast. This is also where you may finally look at an ORM, only to read the SQL it emits.

### ATOMS
1. Offset pagination: `LIMIT n OFFSET m`: cost grows with offset (the database walks and discards `m` rows); rows inserted or deleted between page requests cause duplicates or skips; total counts (`count(*)`) are expensive on large tables.
2. Cursor/keyset pagination: "give me the next page after this row" using the sort key: `WHERE (start_at, id) > ($c1, $c2) ORDER BY start_at, id LIMIT n`. Uses the index; stable under inserts; no offset cost.
3. The sort key must be total and deterministic: include a unique tiebreaker (`id`).
4. Row-value comparison `(a, b) > (x, y)` in Postgres uses the composite index efficiently; mixed ascending/descending needs care (reverse keys or `OR` expansion).
5. Opaque cursors: base64url-encoded JSON `{k: [..], v: 1}`, optionally signed (HMAC) so clients can't tamper or probe; include sort/filter hash so a cursor can't be reused with different filters.
6. Fetch `limit + 1` rows to know if there's a next page without a count; return `nextCursor` (and `hasMore`); enforce a max `limit`.
7. Backward pagination (`prevCursor`) by reversing the comparison and order, then reversing the results.
8. Counts: avoid exact totals by default; use estimates (`reltuples`), capped counts (`count up to 1000+`), or separate endpoint.
9. Filtering: allowlist of filterable fields and operators; typed parsing (enums, dates); never concatenate query-string values into SQL; build SQL with parameters and whitelisted identifiers; each supported filter has an index plan.
10. Sorting: allowlist of sortable fields; each sort order needs a matching composite index (M4.2); changing sort changes the cursor format.
11. Sparse fieldsets and expansions only when a real client needs them; avoid inventing many query parameters.
12. Search: prefix, trigram or full-text (M4.2/M4.5), not `ILIKE '%x%'` on large tables.
13. Pagination response shape: `{ "items": [...], "page": { "nextCursor": "...", "limit": 50 } }`; also `Link` headers (RFC 8288) optionally.
14. Pagination under authorization: the tenant predicate is part of the keyset query and the index `(workspace_id, start_at, id)`.
15. Infinite lists and "load more": client uses the cursor; refetch and merge semantics (M7.2).
16. ORMs: Drizzle (SQL-like, type-safe, thin) vs Prisma (heavier abstraction, engine); what an ORM gives (types, migrations, relations) and costs (hidden queries, N+1, limited SQL features, bad for window functions/CTEs/ranges). Query builders vs ORMs.
17. Reading the emitted SQL: Drizzle's `.toSQL()`, query logging; compare with your hand-written query; check indexes still apply.
18. Policy: ORM only where you can read the SQL; raw SQL for the hard parts; never let an ORM generate DDL you didn't review.

### RESOURCES (in order)
1. Use The Index, Luke, "Paging Through Results" (the seek method): https://use-the-index-luke.com/no-offset . Read the whole page and the "Fetch next page" subpage.
2. Markus Winand, "We need tool support for keyset pagination" and `pg` row value comparison docs: https://www.postgresql.org/docs/current/functions-comparisons.html#ROW-WISE-COMPARISON .
3. Stripe API docs, "Pagination" (cursor design with `starting_after`/`ending_before`), and Slack's "Evolving API pagination at Slack": https://slack.engineering/evolving-api-pagination-at-slack/ .
4. Brandur, "Paginating Postgres" or the Shopify engineering post on pagination: https://shopify.engineering/pagination-relative-cursors .
5. Drizzle ORM docs: schema, select, joins, `.toSQL()`, migrations (Drizzle Kit): https://orm.drizzle.team/docs/overview . Read the "SQL-like" select docs and migrations overview only.
6. RFC 8288 Web Linking (the `Link` header): https://www.rfc-editor.org/rfc/rfc8288 (skim).
7. OWASP API4 (Unrestricted Resource Consumption) for `limit` caps.

### START HERE (first 30 minutes)
1. Seed 100k bookings in slotlock (copy the seeder). Run `select ... order by start_at limit 20 offset 80000` and `explain analyze` it. Note rows scanned.
2. Run the keyset equivalent with `(start_at, id) > (...)`. Compare plans.

### DRILLS
D1. `GET /workspaces/:wid/bookings?limit=&cursor=&status=&resourceId=&from=&to=&sort=` returning `{items, page:{nextCursor, hasMore}}`. Implementation: signed opaque cursor containing sort key and filter hash; `limit` default 50, max 100; whitelist filters and sorts; every combination backed by an index (document them in `docs/indexes.md` with EXPLAIN snapshots).
D2. The correctness demo: a script that pages through 10,000 rows while another process inserts and deletes rows. Offset version: count duplicates/skips. Cursor version: zero duplicates, zero skips for rows existing throughout the traversal. Chart both.
D3. The performance demo: time page 1 vs page 5,000 with offset and with keyset on 1M rows; produce a table.
D4. Backward pagination: `prevCursor` and tests for first and last page boundaries, empty pages, exactly `limit` items, and equal sort keys requiring the tiebreaker.
D5. Mixed sort directions: `ORDER BY start_at DESC, id DESC` and an index to match; test cursor correctness with duplicated `start_at` values.
D6. Cursor tampering: modify a cursor and replay with different filters; the HMAC check rejects with 400 `INVALID_CURSOR`; test that a cursor can't escape tenant scope.
D7. Search: `?q=` using prefix match on a normalized column with a B-tree, then trigram GIN for infix; document when you'd step to full-text.
D8. Totals: `GET /bookings/count?status=confirmed` returns an exact count with a statement timeout and a fallback estimate; document the trade-off.
D9. ORM peek (read-only): in a scratch folder, define the same schema in Drizzle, write the listing query, print `.toSQL()`, paste next to your hand-written SQL in NOTES. Run both through `EXPLAIN`. Note three things Drizzle did that you wouldn't, and one thing you couldn't express (e.g., `tstzrange` exclusion or the window function). Decide: slotlock stays on `postgres.js`.
D10. Optional Prisma contrast: a 30-minute read of the emitted queries for a nested include and how many round trips occur (via query logging).
D11. Add `Link` headers (`rel=next`) to the response and document client usage.

### BREAK
- Page with a non-unique sort key and observe duplicates/skips.
- Allow a client-provided `ORDER BY` string and exploit SQL injection through it; fix with a whitelist.
- Remove the supporting index and watch p95 explode at page 5,000.
- Let the client set `limit=1000000` and observe memory/latency; cap it.

### QUIZ (cold)
1. Why is `page=47` slow and inconsistent?
2. Write the keyset predicate for `(start_at, id)` ascending in SQL.
3. Why does the sort need a unique tiebreaker?
4. Why opaque and signed cursors?
5. How do you know there's a next page without a count?
6. How do you do backward pagination?
7. What indexes support tenant-scoped keyset pagination?
8. Why whitelist filters and sorts?
9. What did the ORM hide and what couldn't it express?
10. When would you offer exact counts and when estimates?

### GATE
Spoken (4 minutes): explain why offset breaks, show the cursor comparison in SQL, and explain how a row inserted during pagination behaves in each approach.
Pass: quiz 9/10, D1 to D11, concurrency demo evidence, tag `gate/M6.2`.

### DELIVERABLE AND FAILURE MODES
Deliverable: cursor pagination endpoint, `docs/indexes.md`, correctness and performance evidence.
Faking it: copying a pagination library. The SQL and the concurrency test are the proof.

---

## M6.3 Uploads, webhooks, background jobs

Hours: 16. Prerequisite: M6.2. Phase: 6.

### WHY
A request/response cycle is the wrong place for slow work, and external systems retry and duplicate. This module is where "what if the job runs twice?" becomes a code answer.

### ATOMS
Uploads
1. `multipart/form-data` structure: boundary, parts, per-part headers (`Content-Disposition`, `Content-Type`); `FormData` and `File` APIs; streaming parsers vs buffering in memory.
2. Limits: total size, per-file size, number of files, field sizes; reject early using `Content-Length` and enforce while streaming (a client can lie); 413 responses.
3. Do not trust the client `Content-Type` or filename; sniff magic bytes (file signature) to determine the type; allowlist types (png, jpeg, pdf); normalize and discard the original filename (generate a random key); defend path traversal (`../`).
4. Storage: not in the DB (blobs bloat it) and not on the app's local disk in production (ephemeral); object storage (S3-compatible: AWS S3, Cloudflare R2, MinIO locally); store metadata in Postgres (`attachments(id, booking_id, storage_key, size, mime, sha256, created_by, created_at)`).
5. Direct-to-storage uploads with presigned URLs: the API authorizes and signs a short-lived URL with constraints (size, content type, key); the browser uploads to storage; the API verifies on completion; reduces your server load. Upload completion callbacks and verification.
6. Serving files: authorization on download (presigned GET URLs with short TTL or proxy through the API), `Content-Disposition: attachment`, `X-Content-Type-Options: nosniff`, a separate domain for user content (M5.2), range requests, cache headers (private).
7. Image processing: resize/convert on upload or on demand; processing is CPU heavy (offload to a worker/queue); decompression bombs (pixel limits); strip EXIF (privacy).
8. Antivirus scanning exists (ClamAV or a service); out of scope, but size/type checks are not.
9. Resumable/multipart uploads for big files (tus, S3 multipart) as a concept.
10. Orphan cleanup: files uploaded but never attached; lifecycle rules and a janitor job.

Webhooks out (you send)
11. You POST an event to a customer's URL when something happens (`booking.confirmed`). Event envelope: `id`, `type`, `created`, `data`, `apiVersion`.
12. Signing: HMAC-SHA256 over `timestamp + '.' + rawBody` with a per-endpoint secret; headers `X-Signature`, `X-Timestamp`; constant-time verification; timestamp tolerance (5 minutes) to prevent replay; secret rotation with two active secrets (Standard Webhooks spec).
13. Delivery semantics: at-least-once; retries with exponential backoff and jitter (minutes to a day or more); consider 2xx as success, others retry; per-endpoint circuit breaker/disable after repeated failure; delivery log with attempts, status, latency; manual replay.
14. Receivers must be idempotent on event `id`; ordering is not guaranteed; include a sequence or version.
15. SSRF risk when you call user-provided URLs: block private/loopback/link-local IPs (`127.0.0.0/8`, `10/8`, `172.16/12`, `192.168/16`, `169.254.169.254`, IPv6 equivalents), re-resolve DNS at connect time (DNS rebinding), no redirects (or re-validate), short timeouts, response size caps, allowed ports and `https` only, run egress through a restricted network.
16. Timeouts and payload limits on outbound calls; don't follow redirects; don't store response bodies verbatim in logs.

Webhooks in (you receive)
17. Verify the signature using the raw body bytes (not a re-serialized JSON), check the timestamp window, reject on mismatch before parsing.
18. Idempotent handling: store `event_id` in a table with a unique constraint; process once; return 2xx quickly and do the work asynchronously (enqueue); handle out-of-order events by checking state/versions.
19. Replay protection and IP allowlisting (supplement, not replacement); secret storage and rotation; testing with provider CLIs (Stripe CLI `stripe listen`).
20. Respond fast: providers time out in seconds and retry; acknowledge, then process.

Background jobs
21. Why jobs: slow work (email, PDF, image processing), unreliable dependencies (webhooks, third-party APIs), scheduled work (reminders), fan-out; keep request latency low.
22. Queue options: Postgres table + workers (`SELECT ... FOR UPDATE SKIP LOCKED`), pg-boss/Graphile Worker (Postgres-backed libraries), Redis-based (BullMQ), cloud queues (SQS). For this curriculum: Postgres table (you write it) and read pg-boss.
23. Job table: `jobs(id, type, payload jsonb, run_at, attempts, max_attempts, locked_at, locked_by, last_error, state, dedupe_key)`; claim with `SKIP LOCKED` and a lease; heartbeat/lease expiry for crashed workers; exponential backoff on failure; dead-letter state after max attempts.
24. At-least-once delivery means a job can run twice (worker crash after the side effect but before the ack; lease expiry while still running; duplicate enqueue). Handlers must be idempotent: idempotency keys on external calls, unique constraints, state checks ("if email already sent for this booking, skip"), outbox pattern (M4.3).
25. Transactional enqueue: insert the job in the same transaction as the state change (outbox), so a rollback removes the job and a commit guarantees it.
26. Scheduling and delays: `run_at`; recurring jobs (cron) with leader election or a unique dedupe key per scheduled slot.
27. Concurrency control: per-type concurrency, per-tenant fairness, rate-limited external calls, priorities.
28. Observability: queue depth, age of oldest job, failure rate, retries, processing time; alerts (M9.3).
29. Graceful shutdown: stop claiming, finish current job within a deadline, release the lease.
30. Poison messages: a job that always fails shouldn't block others or retry forever.
31. Email sending: provider API (Resend, Postmark, SES) vs SMTP; local fake SMTP (MailHog/Mailpit); deliverability basics (SPF, DKIM, DMARC) as vocabulary; never send in the request path.
32. Idempotent external calls: pass your idempotency key to the provider (Stripe supports it).

### RESOURCES (in order)
1. MDN, `FormData`, "Sending forms through JavaScript", and multipart/form-data format: https://developer.mozilla.org/en-US/docs/Web/API/FormData ; RFC 7578 https://www.rfc-editor.org/rfc/rfc7578 (skim).
2. OWASP File Upload Cheat Sheet: https://cheatsheetseries.owasp.org/cheatsheets/File_Upload_Cheat_Sheet.html . Full.
3. AWS S3 presigned URL docs (concepts) and MinIO quickstart for local S3: https://docs.aws.amazon.com/AmazonS3/latest/userguide/using-presigned-url.html ; https://min.io/docs/minio/container/index.html . Cloudflare R2 docs as an alternative.
4. Stripe docs, "Webhooks" (signing, retries, best practices) and Standard Webhooks spec: https://docs.stripe.com/webhooks ; https://www.standardwebhooks.com/ . Read the signature verification sections carefully.
5. OWASP SSRF Prevention Cheat Sheet: https://cheatsheetseries.owasp.org/cheatsheets/Server_Side_Request_Forgery_Prevention_Cheat_Sheet.html . Full.
6. Brandur Leach, "Transactionally Staged Job Drains in Postgres": https://brandur.org/job-drain ; "Postgres Queues": https://brandur.org/postgres-queues . pg-boss README and docs (read the design sections): https://github.com/timgit/pg-boss ; Graphile Worker docs: https://worker.graphile.org/ .
7. PostgreSQL docs, `SELECT ... FOR UPDATE SKIP LOCKED` (M4.3 reread).
8. AWS Builders' Library, "Timeouts, retries and backoff with jitter": https://aws.amazon.com/builders-library/timeouts-retries-and-backoff-with-jitter/ . Full.
9. Mailpit docs for local email testing: https://mailpit.axllent.org/ .
10. The "Two Generals" and exactly-once myths: Tyler Treat, "You Cannot Have Exactly-Once Delivery": https://bravenewgeek.com/you-cannot-have-exactly-once-delivery/ .

### START HERE (first 30 minutes)
1. Run MinIO in Docker. Create a bucket `slotlock-attachments`.
2. Upload a file with the AWS CLI or `mc`. List it. Download it. This is the storage you will talk to.
3. Write on paper: "What happens if this job runs twice?" for sending a confirmation email.

### DRILLS
D1. Upload endpoint: `POST /workspaces/:wid/bookings/:id/attachments` (multipart), size limit 2MB enforced while streaming, allowed types by magic-byte sniffing (png, jpeg, pdf), random storage key, metadata row, authz (workspace member with write permission), 413/415/422 mapped to the error contract, storage in MinIO via the S3 SDK. Tests: oversize, wrong type with a spoofed `Content-Type`, double extension, path traversal filename, zero-byte file, unauthorized tenant.
D2. Presigned upload: `POST /attachments/presign` returns a short-lived presigned PUT with size and content-type conditions; `POST /attachments/:id/complete` verifies the object (size, HEAD, optional sniff of the first bytes) before marking it live; unverified uploads are cleaned by a janitor job.
D3. Download: authorized `GET /attachments/:id` returns a 302 to a 60-second presigned GET or streams through the API with `Content-Disposition: attachment`, `nosniff`, `Cache-Control: private, no-store`; SVG/HTML uploads are never rendered inline.
D4. Job queue from scratch: `jobs` table; `enqueue(type, payload, {runAt, dedupeKey})` inside the same transaction as the business write; worker loop using `FOR UPDATE SKIP LOCKED`, lease/heartbeat, exponential backoff with jitter, dead-letter after N attempts, graceful shutdown. Tests: 3 workers, 1,000 jobs, each processed exactly once under normal operation; kill -9 a worker mid-job and see the lease expire and the job retried.
D5. Job idempotency: handlers for `email.booking_confirmation` and `webhook.deliver`; prove "job runs twice" safe with: `email_log(booking_id, kind)` unique, provider idempotency key, a state check. Force double execution (duplicate enqueue, lease expiry while still working) and prove one email. The gate answer is this test.
D6. Outbound webhooks: `webhook_endpoints(url, secret, active)`, `webhook_deliveries(event_id, endpoint_id, attempt, status, response_code, next_attempt_at)`; on `booking.confirmed` enqueue a delivery per endpoint; sign with HMAC-SHA256 over `timestamp.body`; retry schedule (1m, 5m, 30m, 2h, 12h); auto-disable after repeated failures; replay endpoint. A fake receiver (a separate Hono app) verifies the signature, timestamp, and dedupes on event id.
D7. Kill test: kill the worker after the HTTP call succeeded but before marking the delivery done; restart; the delivery repeats; the receiver stays idempotent. Write the diagram of this failure.
D8. Inbound webhooks: implement `POST /webhooks/payments` for a fake provider (or Stripe test mode with `stripe listen`): verify the signature on the raw body, reject old timestamps, store `event_id` unique, enqueue processing, return 200 fast; out-of-order handling for `payment.succeeded` before `payment.created`.
D9. SSRF defense for customer webhook URLs: validation at registration and at delivery (resolve DNS, reject private ranges, re-check after resolving, pin the IP for the connection, disable redirects, 5s timeout, 1MB response cap). Attack tests against `http://169.254.169.254`, `http://localhost:5432`, a DNS name resolving to 127.0.0.1, an HTTP redirect to an internal address, and decimal/hex IP encodings.
D10. Email via Mailpit: SMTP or API-compatible client; confirmation email on booking confirmation, rendered from a template, sent by a job; test with Mailpit's API.
D11. Scheduled job: reminder 24 hours before a booking using `run_at`, deduped by `(booking_id, 'reminder')`; cancelling the booking cancels the pending job; reschedule updates it.
D12. Metrics: expose job queue depth, oldest job age, failures by type (as a SQL view and a `/admin/jobs` page or JSON), used again in M9.3.

### BREAK
- Upload an HTML file with `Content-Type: image/png` and serve it inline from your origin to show XSS; fix.
- Upload a 5GB stream and verify the server aborts early without buffering.
- Remove the webhook timestamp check and replay an old request.
- Make the job handler non-idempotent and run it twice to double-send.
- Point a webhook at `localhost` services.
- Crash the worker between claim and process repeatedly and watch the job reach the dead-letter state.

### QUIZ (cold)
1. Why not trust `Content-Type` and filename, and what do you do instead?
2. Why not store uploads in the DB or app disk?
3. How does a presigned URL flow work and what does it constrain?
4. What does the HMAC cover, and why the timestamp?
5. Why verify against the raw body?
6. Why do jobs run twice and how do you make handlers safe?
7. How does `SKIP LOCKED` plus a lease give you a queue?
8. What is the transactional outbox solving?
9. List five SSRF defenses.
10. Why acknowledge webhooks quickly and process asynchronously?

### GATE
Spoken (5 minutes): "What happens if the job runs twice?" answered with code: the failure scenarios (crash after side effect, lease expiry, duplicate enqueue), the guards, and the test that proves a single email and a tolerated duplicate webhook.
Pass: quiz 9/10, D1 to D12, the kill tests green, tag `gate/M6.3`.

### DELIVERABLE AND FAILURE MODES
Deliverable: attachments, job queue, outbound/inbound webhooks, SSRF defenses, email jobs in `slotlock`.
Faking it: using a queue library before writing the Postgres one. You must have written the claim query and the lease logic.

---

## M6.4 Contract, health, first deploy (Phase 6 exit)

Hours: 10. Prerequisite: M6.3. Phase: 6.

### WHY
A contract that disagrees with the server is worse than none. And "deployed" is a different state from "works on my machine"; the first deploy surfaces config, networking and migration problems early.

### ATOMS
1. A written contract: `docs/api.md` (mandatory) and OpenAPI 3.1 (optional): paths, schemas, auth schemes, error shapes; generate from Zod (`@hono/zod-openapi`) or write by hand; contract tests verify the server matches it (schemathesis/dredd/your own).
2. Documentation as a product: examples, curl snippets, error catalog (every `code`), auth guide, rate limits, pagination guide, idempotency guide, webhook guide, changelog.
3. Health endpoints: liveness `GET /health` (process is up; no dependencies) vs readiness `GET /ready` (can serve: DB reachable, migrations at expected version); orchestrators and load balancers use them differently; startup probes.
4. Configuration: environment variables, validated at boot with Zod (fail fast), no secrets in git, `.env.example`, config per environment; 12-factor (config, backing services, processes, port binding, disposability, logs to stdout).
5. Process lifecycle: bind `0.0.0.0`/`PORT`, graceful shutdown on SIGTERM (M1.3), connection draining, startup migrations vs a separate migrate step.
6. Containerization: a minimal Dockerfile (multi-stage, non-root user, `.dockerignore`, pinned base image digest), image size, layer caching, `HEALTHCHECK`.
7. Platform deploys: Fly.io or Railway: app + managed Postgres, secrets via platform, release command for migrations, scaling, regions; volume vs object storage; logs.
8. Environments: dev, staging (prod-like data shape, separate DB), prod; same image, different config; promotion; seed and anonymized data policy.
9. Release safety: migrations run before the new code (expand), rollback story (previous image), feature flags (concept).
10. TLS and domains: platform-provided cert; custom domain in M9.4.
11. CI basics: install, type-check, lint, test (with a Postgres service), build image, deploy on `main`; caching dependencies; secrets in CI.
12. Dependency hygiene: lockfile, `bun audit`/`npm audit`, Dependabot or Renovate.
13. README for operators: how to run locally, env vars table, how to migrate, how to deploy, runbooks.

### RESOURCES (in order)
1. OpenAPI "Learn": https://learn.openapis.org/ . Read "Introduction" and "Specification" (first four chapters). `@hono/zod-openapi`: https://github.com/honojs/middleware/tree/main/packages/zod-openapi .
2. The Twelve-Factor App (all 12 short pages): https://12factor.net/ .
3. Fly.io docs: "Launch a Hono/Node app", "Postgres", "Secrets", "Release commands": https://fly.io/docs/ . Or Railway docs: https://docs.railway.com/ . Pick one platform and read its quickstart plus deploy and Postgres docs.
4. Docker docs: "Best practices for writing Dockerfiles" and multi-stage builds: https://docs.docker.com/build/building/best-practices/ .
5. Kubernetes docs on liveness/readiness/startup probes (concepts apply to any platform): https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-startup-probes/ .
6. GitHub Actions docs: workflow syntax, service containers, caching: https://docs.github.com/en/actions .
7. Schemathesis docs (property-based API testing against OpenAPI): https://schemathesis.readthedocs.io/ .

### START HERE (first 30 minutes)
1. Add `GET /health` (always 200) and `GET /ready` (runs `select 1` with a 500ms timeout and checks `schema_migrations` version).
2. Stop Postgres; `curl -i /ready` shows 503 while `/health` is 200. Start it again.

### DRILLS
D1. Finish `docs/api.md`: every route, every error code, auth, rate limits, pagination, idempotency, webhooks, with copy-pasteable curl examples that you actually run in CI (a script executes each example against a seeded DB and fails on mismatch).
D2. OpenAPI: generate from Zod schemas (or hand-write) and serve at `/openapi.json`; run Schemathesis against a local instance and fix every failure it finds (expect 500s on edge inputs); record the findings in NOTES.
D3. Config: Zod-validated env at boot (`DATABASE_URL`, `SESSION_SECRET`, `S3_*`, `APP_ORIGIN`, ...); the app refuses to start with a clear error listing missing/invalid vars; `.env.example`; no defaults for secrets.
D4. Dockerfile: multi-stage, non-root, small final image; `docker build` and run locally against the compose Postgres; measure the image size and startup time.
D5. CI: GitHub Actions workflow with a Postgres service: install, `tsc --noEmit`, lint, tests, build image. Fail on a deliberate type error.
D6. Deploy: to Fly.io or Railway with managed Postgres; run migrations via the release command; secrets set via the platform; `GET /ready` wired as the health check; verify with curl over HTTPS; put the URL in README.
D7. Staging: a second app and database from the same image; promote the same image to prod; document differences in config only.
D8. Rollback drill: deploy a broken version (a failing `/ready`), watch the platform refuse or roll back; then roll back by redeploying the previous image; write the runbook.
D9. Zero-downtime check: run a load loop (`while true; curl ...`) during a deploy and count failed requests; tune the health check and graceful shutdown to reach zero failures.
D10. Backup: platform backups enabled; perform a restore into a new DB and run the verification script from M4.4.
D11. Implement the Phase 5 exit paper designs for bookings: idempotent confirm and pay, cursor list, logout-all, IDOR tests (verify each is in the repo, with tests).

### BREAK
- Stop the DB in staging and watch `/ready` drop traffic while `/health` stays green.
- Deploy with a missing env var and verify the boot error is clear.
- Change a response field without updating the contract and watch the contract test fail.
- Kill the instance during a long request and check graceful shutdown behavior in the platform.

### QUIZ (cold)
1. `/health` vs `/ready`: what does each check and who uses it?
2. What are the 12-factor rules on config and processes?
3. Why validate env at boot?
4. What makes a Dockerfile good for production?
5. How do you roll back an image and what about migrations?
6. Staging vs production: what must be identical and what must differ?
7. What does a contract test protect you from?
8. How do you achieve a zero-downtime deploy?
9. What is expand/contract in this context?
10. What should a README for operators contain?

### PHASE 6 EXIT TEST
- `docs/api.md` complete and its examples run in CI.
- An idempotent double `POST` creates one booking (shown live).
- Cursor pagination correct under concurrent inserts (shown live).
- "What happens if the job runs twice?" answered with the test.
- `/ready` fails when the DB is down (shown live on the deployed app).
- Deployed to a real URL with staging and prod.

### GATE
Spoken (4 minutes): deploy flow from git push to traffic, rollback plan, and the health/readiness distinction.
Pass: quiz 9/10, D1 to D11, Phase 6 exit passed, tags `gate/M6.4` and `gate/phase6`.

### DELIVERABLE AND FAILURE MODES
Deliverable: `slotlock` deployed (ugly and correct), contract tests, CI green, runbooks. Start internship applications now (Part E).
Faking it: a deploy with no staging, no rollback drill, no readiness check. Those drills are the module.


---

# PHASE 7. CLIENT DATA: CACHE AND MUTATIONS

Repo: `slotlock/apps/web` (Vite + React 19 + TypeScript strict + one router). The API is the deployed `slotlock` from M6.4. No UI kit, no CSS framework required (plain CSS or CSS modules; accessibility matters more than looks). Next.js is not allowed until Phase 8.

Router choice (do in M7.2, then freeze): TanStack Router or React Router 7 (data router mode). Pick one in 20 minutes by reading the first docs page of each; the choice is not a gate.

---

## M7.1 Server state vs client state, the baby query cache

Hours: 12. Prerequisite: M6.4. Phase: 7.

### WHY
Most "state management" pain is server state (data owned by someone else, async, shared, out of date) handled with tools for client state (a toggle, a form draft). You will build a 150-line cache first so TanStack Query is no longer magic.

### ATOMS
1. Client state: owned by the UI, synchronous, ephemeral (open modal, input draft, selected tab). Server state: owned by the server, asynchronous, shared across users, can become stale at any moment, needs caching, deduping, invalidation and error handling.
2. URL state is a third kind: shareable, back-button aware (filters, page cursor, selected id); prefer URL over component state for anything a user may want to link to or reload.
3. Derived state should be computed during render, not copied into state; avoid duplicating server data in `useState` (it goes stale).
4. React render model: components are functions of props and state; render must be pure; effects synchronize with outside systems (not event handlers, not data derivation); cleanup functions; dependency arrays tell React what the effect reads; Strict Mode double-invokes effects in dev to find missing cleanup.
5. The `useEffect` + `fetch` + `useState` pattern and its failure modes: no cache, duplicate requests, no dedupe, race conditions (response from request 1 arrives after request 2), no abort, loading flicker, waterfalls (child fetches after parent renders), unmounted updates, error handling gaps, stale data on focus/reconnect.
6. External stores: `useSyncExternalStore(subscribe, getSnapshot)` is the correct primitive for subscribing React to non-React state; snapshots must be referentially stable between changes.
7. Cache anatomy: key (serializable, deterministic), entry `{data, error, status, updatedAt, promise}`, observers (subscribers), statuses (`pending`, `success`, `error`), fetch status (`fetching`, `idle`), staleness (`staleTime`), garbage collection (`gcTime` after the last observer leaves).
8. Request deduplication: concurrent requests for the same key share one in-flight promise.
9. Stale-while-revalidate: show cached data immediately, refetch in the background, replace if changed; the user sees speed, the app converges to truth.
10. Invalidation: marking keys stale and refetching active ones after a mutation; key hierarchies (`['bookings']` invalidates `['bookings', 'list', {...}]` and `['bookings', 'detail', 88]`).
11. Structural sharing: keep references of unchanged parts so React skips re-renders.
12. Cancellation: `AbortSignal` passed to `fetch`; abort when no observers remain or key changes.
13. Retries: exponential backoff for network and 5xx failures; no retry on 4xx.
14. Refetch triggers: window focus, reconnect, interval, manual, mount if stale.
15. Error and loading states are part of the data model; model them as discriminated unions (`{status:'pending'} | {status:'success', data} | {status:'error', error}`) so impossible states are unrepresentable.
16. Typed API client: one `api.ts` that builds URLs, attaches credentials (`credentials: 'include'`), parses JSON, validates responses with Zod (at least in dev), converts error bodies into typed `ApiError` with `code` and `requestId`.
17. What a cache can't solve: authorization changes, multi-tab consistency, offline writes, huge lists (virtualization) — know the edges.
18. Alternatives and when each fits: TanStack Query/SWR (server state), router loaders (route-level data), Zustand/Jotai/Redux (client state; Redux only when you need the pattern), Context (low-frequency values only; it re-renders all consumers).

### RESOURCES (in order)
1. React docs: "Synchronizing with Effects", "You Might Not Need an Effect", "Lifecycle of Reactive Effects": https://react.dev/learn/synchronizing-with-effects ; https://react.dev/learn/you-might-not-need-an-effect ; https://react.dev/learn/lifecycle-of-reactive-effects . Full.
2. Dan Abramov, "A Complete Guide to useEffect": https://overreacted.io/a-complete-guide-to-useeffect/ .
3. React docs, `useSyncExternalStore`: https://react.dev/reference/react/useSyncExternalStore . Full.
4. TanStack Query docs, Overview, Important Defaults, Query Keys, Query Functions, Caching: https://tanstack.com/query/latest/docs/framework/react/overview ; read "Important Defaults" and "Caching Examples" twice.
5. TkDodo's blog, "Practical React Query" series (the first 6 posts: Practical React Query, React Query Data Transformations, Status Checks, Testing, Effective Query Keys, Why You Want React Query): https://tkdodo.eu/blog/practical-react-query .
6. Kent C. Dodds, "Application State Management with React" and "Stop using client-side state for server data" concept: https://kentcdodds.com/blog/application-state-management-with-react .
7. MDN, `AbortController`: https://developer.mozilla.org/en-US/docs/Web/API/AbortController . MDN, Fetch API using `credentials`.
8. HTTP `stale-while-revalidate` RFC 5861 (Phase 2 callback): https://www.rfc-editor.org/rfc/rfc5861 .

### START HERE (first 30 minutes)
1. Scaffold `apps/web` with Vite React-TS (`bun create vite`). Strict TypeScript.
2. Write `BookingsList` with `useEffect` + `fetch` + `useState`. Navigate away and back: watch the refetch and the flash.
3. Open Network tab and count requests with React Strict Mode on.

### DRILLS
D1. Bad version first: list + detail components with `useEffect`/`fetch`/`useState`. Reproduce on purpose and write down evidence for each of: duplicate requests, stale response overwriting a newer one (throttle the network, click rapidly between ids), no cancellation, unmounted update warnings (React 18+ no longer warns; check the Network tab for wasted work), waterfalls (parent list then child fetch), no shared cache between list and detail.
D2. Typed API client `api.ts`: `request<T>(path, {method, body, signal, schema})`, JSON encode/decode, `credentials:'include'`, `ApiError` with `status`, `code`, `details`, `requestId`; Zod validation for responses; `Idempotency-Key` helper (M6.1) for mutations; tests with `msw` or a mock `fetch`.
D3. Build the baby cache `miniQuery.ts` (about 150 lines, no libraries): `QueryClient` with a `Map<string, Entry>`; `fetchQuery(key, fn, {staleTime})` with in-flight dedupe; `subscribe(key, cb)`; `getSnapshot(key)`; `invalidate(prefix)`; `gc` after the last observer plus `gcTime`; `useQuery(key, fn, opts)` built on `useSyncExternalStore`; `AbortSignal` support; retries with backoff; focus refetch. Tests with a fake `fetch` and fake timers: dedupe (10 components, one request), stale-while-revalidate, invalidation of a prefix, abort on unsubscribe, error then retry.
D4. Race proof: a test that fires request A (slow) then request B (fast) for different keys in one observer and asserts the UI shows B's data; implement the check that fixes it (key identity comparison or abort).
D5. Structural sharing: implement a small `replaceEqualDeep` and a test where unchanged items keep object identity; show a memoized child skipping re-render.
D6. Rewrite list and detail with `miniQuery`. Compare the Network tab before/after on: list to detail to back (instant), two components using the same query (one request), a refetch on focus.
D7. Query key design doc in NOTES: key factory `bookingKeys = { all, lists(), list(filters), detail(id) }`, why filters belong in keys, what gets invalidated by a confirm or a cancel.
D8. Discriminated-union loading model: write a `QueryState<T>` type and a component that exhaustively switches over it; try to render `data` while `pending` and watch TypeScript refuse.
D9. Server-state vs client-state sort: take 15 pieces of state from the slotlock UI (current user, bookings list, selected resource, booking form draft, filter, modal open, cursor, toast queue, theme, unsaved changes flag, workspace, permissions, search text, selected date, notifications), classify each (server/client/URL/derived), choose where it lives, justify. Commit as `docs/client-state.md`.
D10. Context pitfall: put a frequently changing value in Context, count re-renders with React DevTools Profiler, then fix with `useSyncExternalStore` selectors or colocated state.

### BREAK
- Remove dedupe and render 10 components; count requests.
- Return a new snapshot object on every `getSnapshot` call and trigger React's infinite loop warning; understand why.
- Forget to abort on unmount and demonstrate the wasted request and a stale write.
- Store fetched data in `useState` copied from props and show it going out of sync.
- Remove the cleanup in an effect and read Strict Mode's double-subscription duplicates.

### QUIZ (cold)
1. Server state vs client state vs URL state: examples of each.
2. List five bugs of `useEffect + fetch + useState`.
3. What is `useSyncExternalStore` for and why must snapshots be stable?
4. `staleTime` vs `gcTime`.
5. What is request deduplication and where does it live?
6. What is stale-while-revalidate and what does the user see?
7. How does invalidation by key prefix work?
8. How does a race condition between two responses happen and what fixes it?
9. Why model query state as a discriminated union?
10. When is Context the wrong tool?

### GATE
Spoken (5 minutes): explain what happens from "component mounts" to "data on screen" in your baby cache, including dedupe, stale, abort and GC; then say what TanStack Query adds that yours doesn't.
Pass: quiz 9/10, D1 to D10, baby cache tests green, tag `gate/M7.1`.

### DELIVERABLE AND FAILURE MODES
Deliverable: `apps/web/src/lib/miniQuery.ts`, `api.ts`, tests, `docs/client-state.md`.
Faking it: installing TanStack Query "just to try". Not allowed until this gate is passed.

---

## M7.2 Query lifecycle, SWR, waterfalls, races, routing

Hours: 12. Prerequisite: M7.1. Phase: 7.

### WHY
Now you adopt TanStack Query knowingly: you can predict when it fetches, serves stale data, refetches, and when it causes waterfalls. You also wire the router so URL state drives queries.

### ATOMS
1. TanStack Query objects: `QueryClient`, `QueryClientProvider`, `useQuery`, `queryOptions`, `useSuspenseQuery`, `useInfiniteQuery`, `useMutation`, `useQueryClient`, `QueryCache`, `MutationCache`.
2. Defaults: `staleTime: 0` (immediately stale), `gcTime: 5 minutes`, `retry: 3` with backoff, refetch on mount/focus/reconnect for stale queries; you set `staleTime` per query type deliberately (resources list 5 min, availability 10 s, user profile 60 s).
3. Statuses: `status` (`pending|error|success`) vs `fetchStatus` (`fetching|paused|idle`); `isLoading` (pending and fetching) vs `isFetching`; `isPlaceholderData`; `isStale`.
4. `queryKey` is the dependency array of the query; objects are hashed deterministically; `queryFn` receives `{signal, queryKey}`; pass `signal` to `fetch` so cancellation works.
5. `select` to derive/transform without extra state; stable references; structural sharing.
6. Dependent queries with `enabled`; parallel queries (`useQueries`); avoid waterfalls by prefetching and by moving fetches up to route loaders.
7. Waterfalls: request chains where each depends on the previous render; detect in the Network waterfall; remove by hoisting queries, prefetching on route transition (`queryClient.prefetchQuery`), or one API call returning the combined data (designing the API for the screen, not the table).
8. Prefetching: on hover/focus, on route loaders, on `useEffect` for the next page; `initialData` and `placeholderData: keepPreviousData` for smooth pagination.
9. Infinite and cursor pagination: `useInfiniteQuery` with `getNextPageParam` from `page.nextCursor` (M6.2); flattening pages; refetch semantics (all pages refetch sequentially); `maxPages`.
10. Error handling: error boundaries and `throwOnError`, per-query `retry`, `QueryCache` `onError` global handler, mapping `ApiError.code` to UI; 401 flow (redirect to login, clear cache), 403 and 404 UX.
11. Cache reset on logout and on workspace switch (`queryClient.clear()`/`removeQueries`); never show user A's cache to user B on a shared browser.
12. Persistence (optional): `persistQueryClient` to localStorage/IndexedDB; the security consequences of persisting authenticated data; not default.
13. Devtools: `@tanstack/react-query-devtools` to watch states live.
14. Router responsibilities: map URL to component tree, parse params and search params (typed, validated), nested layouts, links with prefetch, scroll restoration, navigation blocking for dirty forms, 404 handling, redirects on auth; route loaders vs component-level queries.
15. URL as state: filters and cursor in search params (typed, validated with Zod); back/forward work; reload preserves state; shareable links.
16. Code splitting by route with `lazy` and `Suspense`; preload on intent.
17. Suspense for data: `useSuspenseQuery` + error boundaries vs manual `isPending` checks; trade-offs and skeletons that do not shift layout (CLS from M3.2).
18. Race conditions at the UI layer: debounced search inputs with `AbortSignal`, ignoring stale responses, `useDeferredValue`/`startTransition` for expensive renders (concept).
19. Optimistic concurrency from the UI: displaying `412` conflicts and offering reload (M6.1 ETag).
20. Query cache and HTTP cache interact: the browser may serve a cached response to `fetch` according to your headers (M2.4); `cache: 'no-store'` or sensible `Cache-Control: private, no-cache` for API responses; know which layer answered (DevTools "(disk cache)").
21. Accessibility of async UI: loading announcements (`aria-busy`, live regions), focus management after route change, skeleton vs spinner semantics.

### RESOURCES (in order)
1. TanStack Query docs: Queries, Query Keys, Query Functions, Network Mode, Parallel, Dependent, Background Fetching Indicators, Window Focus Refetching, Disabling/Pausing, Query Retries, Paginated, Infinite, Placeholder Data, Initial Data, Prefetching, Query Cancellation, Suspense: https://tanstack.com/query/latest/docs/framework/react/guides/queries . Read in that order.
2. TkDodo: "React Query and React Context", "Placeholder and Initial Data", "React Query Error Handling", "Seeding the Query Cache", "Automatic Query Invalidation after Mutations", "Using WebSockets with React Query": https://tkdodo.eu/blog/ .
3. Router docs: TanStack Router "Quick Start", "Search Params", "Data Loading", "Preloading": https://tanstack.com/router/latest/docs/framework/react/overview ; or React Router 7 "Data Loading", "Search Params", "Pending UI": https://reactrouter.com/start/framework/installation . Read only the one you chose.
4. React docs: `Suspense`, `lazy`, `useTransition`, `useDeferredValue`: https://react.dev/reference/react/Suspense .
5. web.dev, "Optimize Cumulative Layout Shift" (skeleton stability): https://web.dev/articles/optimize-cls .
6. W3C WAI ARIA Authoring Practices: live regions and feedback: https://www.w3.org/WAI/ARIA/apg/ ; web.dev Learn Accessibility: https://web.dev/learn/accessibility .
7. `msw` docs (Mock Service Worker) for API mocking in tests: https://mswjs.io/docs/ .

### START HERE (first 30 minutes)
1. Install TanStack Query and Devtools. Replace `miniQuery` in the list screen only.
2. Open the devtools and watch status transitions as you navigate. Predict the fetch before each navigation; write predictions and results.

### DRILLS
D1. Parity audit: for each behavior in D3 of M7.1 (dedupe, SWR, invalidate, abort, retry, focus), show the TanStack equivalent and the config knob; list three features it has that your cache lacks.
D2. Resource and booking screens: `resourcesQuery`, `bookingsListQuery(filters)`, `bookingDetailQuery(id)` via `queryOptions` helpers; deliberate `staleTime` per query with a one-line justification each; typed `ApiError` handling.
D3. Router: routes `/`, `/w/:wid/bookings`, `/w/:wid/bookings/:id`, `/w/:wid/bookings/:id/edit`, `/login`, `*`; filters and cursor in typed search params; protected route redirect to `/login?next=`; 404 and 403 pages; scroll restoration.
D4. Waterfall hunt: build the screen naively (workspace to resources to bookings to booking details, each in a child), record the waterfall in DevTools, then remove it with route-level prefetch and a combined endpoint (`GET /workspaces/:wid/dashboard`) where justified. Show before/after total time with 150ms simulated latency.
D5. Infinite list with `useInfiniteQuery` and the cursor API; "Load more" button; keep scroll position when returning from detail; test with 1,000 rows.
D6. Search-as-you-type: debounce 250ms, cancel previous requests with `signal`, keep previous data to avoid flicker; a test fires out-of-order responses and asserts correctness.
D7. Prefetch on intent: `onMouseEnter`/`onFocus` on a booking row prefetches detail; show the instant navigation and verify no duplicate fetch.
D8. Error UX table for each of 401, 403, 404, 409, 412, 422, 429, 5xx, offline: what the user sees and what the code does (retry button, redirect, banner). Implement via a central error handler and a boundary.
D9. Logout and workspace switch clear the cache; a test logs in as A, logs out, logs in as B, and asserts no A data renders even momentarily.
D10. Loading states: skeletons with fixed dimensions (CLS ≈ 0 using Lighthouse), `aria-busy`, route-change focus management (focus the page `<h1>`), announcements via a polite live region.
D11. Suspense variant: rebuild the detail screen with `useSuspenseQuery` and an error boundary; compare code, loading behavior and pitfalls (waterfalls in Suspense).
D12. Code splitting: lazy-load the edit route and the heavy chart/calendar component; verify bundle chunks in the build analyzer (`vite-bundle-visualizer`) and the LCP/INP impact.

### BREAK
- Put `Date.now()` in a query key and watch infinite refetching.
- Forget the `signal` and see requests continue after navigating away.
- Set `staleTime: Infinity` on bookings and watch stale data persist after a mutation elsewhere.
- Use `enabled: !!id` incorrectly and render forever-pending UI.
- Persist the cache across logout and leak data.

### QUIZ (cold)
1. `isLoading` vs `isFetching` vs `isPending`.
2. How do `staleTime` and `gcTime` interact with navigation back?
3. What makes a good query key?
4. Name three ways to remove a waterfall.
5. How does `useInfiniteQuery` use the API's `nextCursor`?
6. What must happen to the cache on logout, and why?
7. Why URL search params for filters?
8. How do you cancel a request properly?
9. HTTP cache vs query cache: how can both bite you?
10. How do you make loading states accessible?

### GATE
Spoken (5 minutes): trace a navigation from `/bookings` to `/bookings/88` and back: URL change, route match, prefetch, query states, cache hit, network (or none), render, focus, scroll position.
Pass: quiz 9/10, D1 to D12, waterfall before/after evidence, tag `gate/M7.2`.

### DELIVERABLE AND FAILURE MODES
Deliverable: list/detail screens with TanStack Query and router, error UX table, waterfall evidence.
Faking it: copying `useQuery` snippets without predictions. The prediction log in NOTES is the proof.

---

## M7.3 Mutations, optimistic UI, double submit, forms

Hours: 14. Prerequisite: M7.2. Phase: 7.

### WHY
Writes are where UIs lie: double clicks, flaky networks, rollbacks, conflicts. You will combine the API's idempotency (M6.1) with client-side optimistic updates and well-built forms.

### ATOMS
Mutations
1. `useMutation`: `mutationFn`, `onMutate`, `onSuccess`, `onError`, `onSettled`, `mutate` vs `mutateAsync`, `isPending`, `variables`, `reset`; mutation scope/serialization for ordering.
2. After a mutation: invalidate affected keys, or update the cache directly with the server response (`setQueryData`) when the response contains the new state (cheaper and consistent).
3. Optimistic updates: `onMutate` cancels in-flight queries for the key (`cancelQueries`), snapshots the previous value, writes the optimistic value; `onError` rolls back from the snapshot; `onSettled` invalidates to converge to truth. React 19 `useOptimistic` is a UI-level alternative (state reverts when the action finishes).
4. When not to be optimistic: payments, irreversible actions, high failure probability, server-side computed results; use pending UI instead.
5. Double submit: user clicks twice, network retries, two tabs. Layers: disable the button during `isPending` (UX), per-intent idempotency key (correctness; generated at form mount or per attempt and reused on retry), server dedupe (M6.1), DB constraints (M4.3).
6. Idempotency key lifecycle on the client: generate when the form instance is created; reuse for retries of the same payload; regenerate after success or if the payload changes materially.
7. Conflict handling: `409 BOOKING_OVERLAP` shows the conflicting slot and refreshes availability; `412` shows "someone else changed this" with a reload/merge choice; `422` maps field errors back to inputs.
8. Rollback correctness with concurrent mutations: snapshot per mutation; or avoid manual rollback by invalidating on settle and letting the server state win.
9. Offline and flaky networks: `networkMode`, paused mutations, retry; "queue offline writes" is a product decision with conflict implications; don't promise it.
10. Multi-tab: BroadcastChannel/`storage` events or refetch on focus; stale tabs and logout propagation.
11. Mutation side effects in UI: toasts, navigation after success (`navigate` in `onSuccess`), focus management, preventing leaving with unsaved changes.
12. Cache write consistency with lists: after creating a booking, insert into the first page of matching lists or invalidate lists; know the sort/filter implications (the new row may not belong in a filtered list).

Forms
13. Native form semantics first: `<form>`, `<label for>`, `name`, `type`, `required`, `autocomplete`, `inputmode`, `Enter` to submit, `FormData` extraction.
14. Controlled vs uncontrolled inputs; uncontrolled with `FormData` for simple forms; React 19 form actions: `<form action={fn}>`, `useActionState`, `useFormStatus`, `useOptimistic`.
15. Validation: same Zod schema as the API (shared package or copied with a contract test); client validation is UX only; the server is the authority; show server errors per field.
16. Error messaging: `aria-invalid`, `aria-describedby` linking errors, error summary with focus on the first invalid field, inline vs summary, no placeholder-as-label, text not color alone.
17. Date/time forms: timezone handling (store UTC, display in the resource's timezone), `datetime-local` pitfalls, DST edge cases, `Intl.DateTimeFormat`, `Temporal` (concept), "end must be after start".
18. Dirty state and navigation blocking; draft persistence (sessionStorage) and privacy implications.
19. Accessibility of dialogs: `<dialog>` element, focus trap and return, `Escape`, `aria-modal`, scroll lock; focus management on mutation result.
20. Keyboard navigation: logical tab order, visible focus, roving tabindex for composite widgets, skip link; test with keyboard only and with a screen reader (VoiceOver/NVDA/Orca) once.
21. Component testing: React Testing Library (`getByRole`, `user-event`), MSW for network, test behavior not implementation; Playwright for real browser flows (M9.6).

### RESOURCES (in order)
1. TanStack Query docs: Mutations, Invalidations from Mutations, Updates from Mutation Responses, Optimistic Updates: https://tanstack.com/query/latest/docs/framework/react/guides/mutations ; https://tanstack.com/query/latest/docs/framework/react/guides/optimistic-updates .
2. React docs: `useOptimistic`, `useActionState`, `useFormStatus`, `<form>`: https://react.dev/reference/react/useOptimistic ; https://react.dev/reference/react/useActionState ; https://react.dev/reference/react-dom/components/form .
3. TkDodo, "Mastering Mutations in React Query" and "Concurrent Optimistic Updates in React Query": https://tkdodo.eu/blog/mastering-mutations-in-react-query ; https://tkdodo.eu/blog/concurrent-optimistic-updates-in-react-query .
4. Stripe, Idempotent requests (re-read) and Brandur's post (M6.1) for the key semantics.
5. MDN: HTML forms guide (`<form>`, constraint validation, `FormData`): https://developer.mozilla.org/en-US/docs/Learn_web_development/Extensions/Forms ; `<dialog>`: https://developer.mozilla.org/en-US/docs/Web/HTML/Reference/Elements/dialog .
6. W3C WAI Forms tutorial: https://www.w3.org/WAI/tutorials/forms/ ; ARIA Authoring Practices: Dialog (Modal) pattern: https://www.w3.org/WAI/ARIA/apg/patterns/dialog-modal/ ; WCAG 2.2 Quick Reference (skim success criteria for forms and focus): https://www.w3.org/WAI/WCAG22/quickref/ .
7. Testing Library: Guiding principles and queries priority: https://testing-library.com/docs/guiding-principles ; https://testing-library.com/docs/queries/about#priority .
8. MDN `Intl.DateTimeFormat` and the Temporal proposal docs: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Intl/DateTimeFormat ; https://tc39.es/proposal-temporal/docs/ . Falsehoods programmers believe about time: https://gist.github.com/timvisee/fcda9bbdff88d45cc9061606b4b923ca .

### START HERE (first 30 minutes)
1. Build a plain `<form>` for "create booking" with labels and no JS. Submit it with Enter. Look at the `FormData` entries in the console.
2. Write on paper the chain for a double click: which layer stops the second POST in each of the four layers.

### DRILLS
D1. Create booking form: native semantics, Zod validation (client and server share the schema), field errors mapped from 422 `details.fields`, `aria-describedby`, error summary focus.
D2. `useMutation` for create/confirm/cancel: `Idempotency-Key` generated at form mount and reused on retry; button disabled while pending; on success `setQueryData` for detail and invalidate lists.
D3. Double-submit lab: 10 rapid clicks, two tabs, and network failure with retry. Show the Network panel: with only a disabled button (UX) duplicates still happen in the two-tab case; with the key, exactly one booking exists. Evidence screenshot and DB count in NOTES.
D4. Optimistic cancel: instantly mark the booking cancelled in list and detail, cancel in-flight queries, snapshot, rollback on error (force a 500 with a dev toggle), invalidate on settle. Test the rollback.
D5. `useOptimistic` variant of the same interaction using React 19 form actions; compare the code and behavior (rollback semantics) with the TanStack version.
D6. Concurrent optimistic edits: two quick edits on the same booking; show the flicker bug with naive invalidation and fix with mutation scope or invalidate-only-last (TkDodo's article).
D7. Conflicts: simulate two users booking the same slot; the loser gets `409 BOOKING_OVERLAP`, the UI shows a conflict message, refetches availability and keeps the user's form input. A Playwright test covers it with two browser contexts.
D8. ETag/`412` flow: edit page loads with `ETag`; another client changes it; save returns 412; UI offers "Reload latest" and shows a field-level diff.
D9. Timezones: bookings across DST changes in a resource's timezone; `datetime-local` input converted correctly to UTC; tests for the spring-forward gap and fall-back ambiguity; display with `Intl.DateTimeFormat` and `timeZone`.
D10. Dialogs: a native `<dialog>` for "Cancel booking" confirm; focus moves in, returns to the trigger on close; `Escape` works; keyboard-only test.
D11. Unsaved-changes guard on the edit route (router blocker plus `beforeunload`), draft restored from `sessionStorage` after reload (and cleared on logout).
D12. Accessibility pass: keyboard-only walk-through of create/edit/cancel; Lighthouse accessibility and axe (`@axe-core/playwright`) at zero serious violations; one screen reader session noted in NOTES.
D13. Tests: RTL + MSW for create (success, 422, 409, network failure); no assertions on implementation details.
D14. Offline behavior: toggle offline in DevTools while submitting; observe paused mutation; decide and document the product behavior (disable the form with a message vs queue).

### BREAK
- Remove `cancelQueries` in `onMutate` and let an in-flight refetch overwrite the optimistic value.
- Skip the rollback and leave a lie on screen after a failure.
- Generate the idempotency key at click time for each retry (new key per attempt) and see duplicates.
- Use placeholder text as the only label and test with a screen reader.
- Validate only on the client and bypass it with curl.

### QUIZ (cold)
1. Walk through an optimistic update step by step, including rollback and settle.
2. When is optimistic UI wrong?
3. Which layers prevent double submit and which one gives correctness?
4. When do you `setQueryData` vs invalidate?
5. How do you present a 409 vs a 412 vs a 422?
6. How should an idempotency key be generated on the client?
7. Why must the server validate even if the client does?
8. How do you link errors to inputs accessibly?
9. What breaks with `datetime-local` and DST?
10. What must a modal dialog do for keyboard users?

### GATE
Spoken (5 minutes): "user double-clicks Save, loses connection, retries, and another user already took the slot" from DOM event to database row: every layer, every outcome.
Pass: quiz 9/10, D1 to D14, double-submit evidence, tag `gate/M7.3`.

### DELIVERABLE AND FAILURE MODES
Deliverable: create/edit/cancel flows with idempotency, optimistic cancel, conflict UX, accessibility pass.
Faking it: only disabling the button. Test: two tabs.

---

## M7.4 Same UI twice (Phase 7 exit)

Hours: 12. Prerequisite: M7.3. Phase: 7.

### WHY
You consolidate by building the same feature set with two data layers and writing what differs. The comparison, not the code, is the learning.

### ATOMS
1. A fair comparison needs a fixed spec: identical routes, behaviors, and acceptance tests run against both implementations.
2. Metrics for comparison: lines of code, requests per scenario, time to interactive content on navigation, re-renders, bug count found by the shared tests, bundle size, a11y results, complexity of mutation handling.
3. Version A: `useEffect` + your `miniQuery` + hand-written mutation helpers. Version B: TanStack Query + router prefetch.
4. Behaviors in the spec: list with filters and cursor; detail; create with idempotency; edit with ETag; optimistic cancel; error UX table; logout clears cache; accessible loading states.
5. Shared Playwright suite as the arbiter: the same tests run against both builds (`VITE_DATA_LAYER=mini|tanstack`).
6. Writing a decision doc: context, options, evaluation, decision, consequences (ADR format).
7. Narrating a full-stack flow is the skill interviewers test: say the sequence precisely without naming frameworks (URL, history, route match, data request, cache, HTTP, server, DB, response, render, paint).

### RESOURCES (in order)
1. Playwright docs: writing tests, locators, fixtures, trace viewer: https://playwright.dev/docs/intro ; https://playwright.dev/docs/locators ; https://playwright.dev/docs/trace-viewer .
2. ADR guidance: Michael Nygard, "Documenting Architecture Decisions": https://cognitect.com/blog/2011/11/15/documenting-architecture-decisions ; adr.github.io: https://adr.github.io/ .
3. Re-read: M7.1 to M7.3 resources, only the sections where your comparison finds differences.
4. Kent C. Dodds, "Testing Implementation Details" for the approach to shared behavioral tests: https://kentcdodds.com/blog/testing-implementation-details .

### START HERE (first 30 minutes)
1. Write the acceptance spec as 20 bullet sentences (given/when/then) in `docs/ui-spec.md`.
2. Convert the first 5 into Playwright tests that currently pass on version B.

### DRILLS
D1. Spec file with 20 behaviors, each with a test id.
D2. Playwright suite (at least 20 tests) parameterized by the data layer; run against both builds; record failures.
D3. Implement version A (mini) fully against the spec; fix until all tests pass.
D4. Measure: per scenario (cold list load, list to detail, detail to back, create, optimistic cancel, 3 rapid filter changes) record request count, time to content, long tasks (Performance panel), React Profiler commits. Table in `docs/data-layer-comparison.md`.
D5. Defect log: every bug found in either version, root cause, and which invariant it violated.
D6. ADR: "Server-state management in slotlock" with the decision to use TanStack Query (or not) and consequences, including what you would revisit (e.g., router loaders).
D7. Narrate aloud, recorded, 5 minutes: the full flow `/bookings` to `/bookings/88`, edit, save twice, back, with no framework names. Transcribe and cut vague words.
D8. Teach-back: explain the baby cache to a friend or record it (3 minutes). Publish as a short post draft (reused in M9.11).

### BREAK
- Run the shared suite with network latency 800ms and packet loss; list the tests that become flaky and fix the root causes, not the timeouts.
- Remove the idempotency key from version B and let the double-submit test fail.

### QUIZ (cold)
1. Name three things TanStack Query does that your cache doesn't and one your cache does explicitly.
2. What would make you choose router loaders over component queries?
3. What would make you choose Zustand/Redux?
4. What is the failure mode when server data is copied into client state?
5. Which tests protect against double-submit?
6. What goes in an ADR?
7. What does the browser do between a link click and the first paint of the new route (client-side routing)?
8. How does `history.pushState` interact with the back button?
9. What would you measure to prove that prefetching helped?
10. Name two accessibility bugs your tests would catch.

### PHASE 7 EXIT TEST (spoken, 8 minutes, cold, no framework names)
Narrate the full flow: click `/bookings` list item, URL change, data load, render, edit, save twice (double click), network failure, retry, success, back button and what the user sees at every step.

### GATE
Pass: quiz 9/10, D1 to D8, shared tests green on both versions, Phase 7 exit passed, tags `gate/M7.4` and `gate/phase7`.

### DELIVERABLE AND FAILURE MODES
Deliverable: two implementations, shared Playwright suite, comparison doc, ADR, recorded narration.
Faking it: comparing from reading. Numbers or it didn't happen.

---

# PHASE 8. WHERE CODE RUNS: CSR, SSR, RSC

Repo: `labs/render-lab` (same booking list page implemented three ways) and a Next.js App Router app used strictly as a rendering lab. The question for every module is always the same four: where is HTML generated, what JS ships, when does data load, what is cached where.

---

## M8.1 Rendering models and the four questions

Hours: 8. Prerequisite: M7.4, M3.4. Phase: 8.

### WHY
Frameworks are bundles of answers to these four questions. Know the questions and you can evaluate any framework next year.

### ATOMS
1. The four questions: (1) where is HTML generated (browser, server per request, build time)? (2) what JavaScript ships and executes (and when)? (3) when does data load (before HTML, after hydration, in parallel)? (4) what is cached where (browser, CDN, server, DB)?
2. CSR (client-side rendering): the server sends an empty shell + JS bundle; the browser executes JS, fetches data, renders. Pros: simple hosting, rich interactivity, cheap server. Cons: slow first content (JS download, parse, execute, then data waterfall), SEO and social previews need workarounds, blank page without JS.
3. SSR (server-side rendering): the server renders HTML per request; the browser paints before JS; then JS hydrates. Pros: fast first content, SEO, works without JS initially. Cons: server cost and latency (TTFB depends on data), hydration cost, uncanny valley (visible but not interactive), complexity.
4. SSG (static site generation): HTML generated at build, served by CDN. Pros: fastest, cheapest, resilient. Cons: stale until rebuild, not for personalized or fast-changing data.
5. ISR / stale-while-revalidate at the page level: static pages regenerated in the background after a revalidate window or on-demand.
6. Streaming SSR: the server flushes the shell immediately and streams slower parts as they resolve (HTTP chunked, M2.6); Suspense boundaries define the chunks.
7. Islands architecture (Astro): static HTML by default, interactive components hydrate individually (`client:load`, `client:visible`); minimal JS.
8. Partial prerendering (PPR): static shell with dynamic holes streamed in; combines SSG and SSR.
9. React Server Components (RSC): components that run only on the server and send a serialized UI description, not JS; client components for interactivity; zero bundle for server components; data access in components.
10. Resumability (Qwik) and fine-grained reactivity without hydration of all components (Solid; concept).
11. Hydration: React attaches event handlers and state to server HTML by re-running components on the client and matching the DOM; costs CPU, can mismatch; INP and TBT effects.
12. Uncanny valley: HTML visible, buttons dead until hydration completes; measure with throttled CPU and click tests.
13. TTFB, FCP, LCP, TBT/INP implications of each model (M3.2 and M3.3): SSR can improve FCP/LCP but worsen TTFB; CSR delays all of them.
14. Personalization vs cacheability: personalized HTML cannot be shared-cached (`Cache-Control: private` or `Vary: Cookie`); strategies: static shell + client fetch for personal bits, edge-side includes, streaming holes.
15. SEO: crawlers execute JS but inconsistently and later; metadata (title, `meta`, canonical, OpenGraph) must be in the initial HTML for previews; `robots`, sitemaps.
16. Routing and data coupling: route-level data loading (loaders) enables parallel fetching and early errors/redirects.
17. Costs: server compute, cold starts, bandwidth, build times, developer complexity, debugging difficulty, vendor lock-in.
18. Decision table per page type: marketing (SSG), blog (SSG/ISR), logged-in dashboard (CSR or SSR streaming), booking page (SSR streaming + client interactivity), admin tool (CSR), docs (SSG/islands).

### RESOURCES (in order)
1. web.dev, "Rendering on the Web": https://web.dev/articles/rendering-on-the-web . Full.
2. Patterns.dev, "Rendering Patterns": https://www.patterns.dev/vanilla/rendering-patterns/ (also the React section: https://www.patterns.dev/react/ ; use the Rendering Patterns section: CSR, SSR, Static, Islands, Streaming SSR, Server Components, Progressive Hydration). Read the whole section.
3. Astro docs, "Islands architecture": https://docs.astro.build/en/concepts/islands/ .
4. React docs, "`hydrateRoot`": https://react.dev/reference/react-dom/client/hydrateRoot ; "`renderToPipeableStream`": https://react.dev/reference/react-dom/server/renderToPipeableStream .
5. Jason Miller, "Islands Architecture": https://jasonformat.com/islands-architecture/ .
6. Re-read: your own M3.1 to M3.4 notes (waterfalls, LCP) and M2.6 (streaming).

### START HERE (first 30 minutes)
1. Make a table with columns: CSR, SSR, SSG, ISR, streaming SSR, islands, RSC; rows: the four questions. Fill from memory, then correct with the resources.
2. For `slotlock`'s five pages (marketing, login, bookings list, booking detail, admin settings), guess the best model for each and write why.

### DRILLS
D1. The four questions table, completed, in NOTES, with one concrete sentence per cell.
D2. Decision table for slotlock pages, with "what could make me change this" per row.
D3. Draw timelines (mermaid or paper) for one page under CSR, SSR, SSG, streaming SSR: browser and server swimlanes; mark TTFB, FCP, LCP, hydration, interactivity.
D4. 10 flashcard-style Q/A pairs created for the spaced retrieval schedule on hydration and the uncanny valley.
D5. Personalization puzzle: design caching for a page with a static list (same for everyone) and a personal header ("Hi, Maya; 2 bookings"); give three architectures and their cache keys.
D6. SEO check: serve the CSR shell and view-source; check what a social preview (OpenGraph debugger, or `curl` and read the `<head>`) shows; then the SSR version.
D7. Write the four-question one-paragraph answer for Next.js default, Astro default, Vite SPA, Remix/React Router framework mode (use docs; verify with a tiny app if unsure).

### BREAK
- Disable JavaScript in DevTools and load each of your pages; list which still show content.
- Throttle CPU 6x and click a button on an SSR page before hydration completes; document the dead-click time.

### QUIZ (cold)
1. The four questions.
2. CSR timeline from request to usable page.
3. SSR timeline; what is the uncanny valley?
4. SSG vs ISR vs SSR?
5. What does streaming SSR change about TTFB and FCP?
6. What are islands?
7. What do RSCs send over the wire?
8. Why can SSR worsen TTFB and still help LCP?
9. How do you cache a personalized page?
10. Which model for a marketing site, a dashboard and a booking page, and why?

### GATE
Spoken (5 minutes): the four questions applied to your three slotlock pages, with timeline drawings from memory.
Pass: quiz 9/10, D1 to D7, tag `gate/M8.1`.

### DELIVERABLE AND FAILURE MODES
Deliverable: tables and timelines in NOTES; decision doc.
Faking it: memorizing buzzwords. You must draw the timeline.

---

## M8.2 Measure CSR vs SSR on the same page

Hours: 12. Prerequisite: M8.1. Phase: 8.

### WHY
Opinions about rendering are cheap; measurements are not. You will build one page three ways and let the numbers decide.

### ATOMS
1. Experimental control: same data, same markup/styling, same API latency, same machine; vary only the rendering strategy; run each at least 10 times and report medians and p75.
2. Variants: (A) CSR: Vite SPA fetching `/api/bookings` after load. (B) SSR: Hono (JSX or `renderToString`) renders HTML with data, then hydrates a small client bundle. (C) Streaming SSR: shell flush then the list streamed (Suspense with `renderToPipeableStream`/`renderToReadableStream`).
3. Server-side data fetch next to the DB: the API call becomes a function call or a local query; no CORS, no extra round trip.
4. Serialization for hydration: embedding the initial data (`<script type="application/json">` with proper escaping, M5.2) so the client doesn't refetch.
5. Hydration cost: JS bytes, parse, execute, hydrate; measured by Performance panel "Hydrate" tasks, TBT, INP under CPU throttling.
6. What to measure: TTFB, FCP, LCP, CLS, TBT, INP (via interaction tests), JS transferred, HTML size, total requests, time-to-first-interactive-click, server CPU per request, p50/p95 server response time.
7. Conditions: fast desktop, throttled "Slow 4G + 4x CPU" (Lighthouse mobile), cold vs warm cache, high API latency (add 300ms), and slow DB.
8. Tooling: Lighthouse CLI with `--throttling-method=devtools`, WebPageTest (https://www.webpagetest.org/), Chrome DevTools Performance panel, `web-vitals` library, `autocannon`/`k6` for server throughput.
9. Failure mode comparison: API slow, API down, JS fails to load, JS slow (CPU), no JS.
10. SSR server cost: render time per request (CPU bound), memory, concurrency; why SSR throughput is lower than serving static files; mitigations: caching HTML (public pages), CDN, streaming.
11. Hydration mismatches appear here first: dates/locales on server vs client (M8.5 deepens).
12. Reporting: one table, plus a one-page narrative: when each strategy wins and loses on your evidence.

### RESOURCES (in order)
1. Lighthouse CLI docs and throttling: https://github.com/GoogleChrome/lighthouse#using-the-node-cli ; https://developer.chrome.com/docs/lighthouse/overview .
2. WebPageTest docs: https://docs.webpagetest.org/ .
3. `web-vitals` library README: https://github.com/GoogleChrome/web-vitals .
4. React docs, `renderToPipeableStream` / `renderToReadableStream` / `hydrateRoot`: https://react.dev/reference/react-dom/server . Hono JSX and streaming docs: https://hono.dev/docs/guides/jsx ; https://hono.dev/docs/helpers/streaming .
5. web.dev, "Optimize Largest Contentful Paint", "Optimize Interaction to Next Paint", "TTFB": https://web.dev/articles/optimize-lcp ; https://web.dev/articles/optimize-inp ; https://web.dev/articles/ttfb .
6. Re-read your M3.4 measurement protocol and use it unchanged.

### START HERE (first 30 minutes)
1. Make a spec for the page: header, filters, list of 50 bookings with 6 fields each, a "Confirm" button on each row. One screenshot as the reference.
2. Write the measurement protocol checklist (machine state, throttling, run counts, cache state) and commit it before any code.

### DRILLS
D1. Build variant A (CSR) in `render-lab/csr`. Record baseline metrics (10 runs).
D2. Build variant B (SSR + hydration) in `render-lab/ssr` using Hono + React (`renderToString` + `hydrateRoot`), embedding initial data. Record metrics.
D3. Build variant C (streaming) with a deliberately slow bookings section behind Suspense and an immediate shell. Record metrics.
D4. Same measurements under: Slow 4G + 4x CPU; +300ms API latency; cold and warm cache. One table: 3 variants x 4 conditions x 8 metrics, medians and p75, in `docs/render-metrics.md`.
D5. Uncanny valley test: scripted Playwright click on "Confirm" at the moment FCP fires; measure time until the handler runs under 6x CPU in SSR vs CSR. Chart it.
D6. Failure tests: block the JS file; kill the API; slow the API to 5s; disable JS. Screenshot each variant and describe the user experience.
D7. Server cost: use `autocannon -c 50 -d 20` against the SSR endpoint and the static CSR HTML; record RPS, p95 and CPU. Explain the ratio.
D8. Cacheability: add `Cache-Control: s-maxage=30, stale-while-revalidate=60` to a public variant of SSR; show CDN-like cache hits with a local Caddy cache or a simple proxy cache; show why personalization breaks it.
D9. Narrative: 1-page conclusion with three claims, each supported by a number from your table, and one claim you expected but the data refuted.

### BREAK
- Omit embedded data in SSR so the client refetches; measure the extra request and layout shift.
- Render with a server timezone different from the client's and watch a hydration warning.
- Put a 2s sleep before the first byte in SSR (non-streaming) and compare to streaming.

### QUIZ (cold)
1. Why did SSR improve or worsen LCP in your data?
2. What is TBT and why is hydration to blame?
3. When is CSR faster than SSR in your results?
4. How does streaming change what the user sees when data is slow?
5. Why does SSR reduce server throughput relative to static files?
6. How do you embed initial data safely?
7. What is the uncanny valley and how did you measure it?
8. What conditions favor each variant?
9. Which metrics move with API latency and which with CPU throttling?
10. What did the data refute?

### GATE
Spoken (5 minutes): present the metrics table and defend three claims with numbers.
Pass: quiz 9/10, D1 to D9, `docs/render-metrics.md` committed, tag `gate/M8.2`.

### DELIVERABLE AND FAILURE MODES
Deliverable: three variants, measurement table, narrative.
Faking it: a table from a single Lighthouse run. 10 runs, medians, protocol written first.

---

## M8.3 React Server Components

Hours: 16. Prerequisite: M8.2. Phase: 8.

### WHY
RSC is a different execution model, not a feature. If you can explain the server/client boundary, the serialized payload and why a function can't cross it, you understand modern React frameworks.

### ATOMS
1. Component environments: Server Components (default in the App Router): run on the server (at request time or build time), can be `async`, can access the DB/filesystem/secrets directly, can't use state, effects, event handlers, browser APIs. Client Components (`'use client'`): ship JS, hydrate, support interactivity; they also render on the server for the initial HTML.
2. `'use client'` marks a module boundary: the module and everything it imports (unless already server-only) join the client bundle.
3. What crosses the boundary: server to client props must be serializable (strings, numbers, booleans, null, arrays, plain objects, Date, Map/Set, typed arrays, BigInt, Promises, and React elements/JSX); not serializable: functions (except Server Functions), class instances, symbols, DB connections.
4. The RSC payload (Flight format): a streamed serialization of the rendered server tree, with references to client component modules and their props; the client React reconstructs the tree; navigation fetches a new payload (`?_rsc=` request or `RSC: 1` header) rather than a full page.
5. Composition rules: client components can render server components passed as `children` or props (the "donut pattern"), because the server renders them and passes the result; client components can't import server components.
6. `server-only` and `client-only` packages guard modules (e.g., importing the DB in a client module fails the build).
7. Environment variable exposure: only `NEXT_PUBLIC_*` reaches the client; accidental secret inclusion via a shared module; taint APIs (`experimental_taintObjectReference`) as defense in depth.
8. Data fetching in server components: `await db.query(...)` or `fetch` directly; parallel data loading with `Promise.all` or separate components under Suspense; avoid sequential awaits (waterfalls moved to the server are cheaper but still waterfalls); request memoization via `React.cache`.
9. Bundle effects: libraries used only in server components (markdown, date formatting, syntax highlighters) cost zero client JS.
10. Interactivity islands: push `'use client'` down to the smallest leaf components (a button, a form), keep layout and data on the server.
11. Context doesn't work across the boundary for server components; Providers must be client components placed high but take `children`.
12. Streaming and Suspense: `<Suspense fallback>` boundaries around async server components stream results progressively; errors via `error.tsx`; `loading.tsx` creates an automatic boundary.
13. The mental model: server components are a templating step that happens on the server whose output includes holes for client components.
14. Pitfalls: serialization errors, accidentally making everything a client component (a `'use client'` in a layout), importing a heavy library into a client component, passing large objects as props (they're in the payload, doubling data with HTML), non-serializable props.
15. Security: server component code is not exposed; props passed to client components are public (visible in the payload); never pass secrets or full DB rows (filter fields); authorization still required (a server component rendering data is only as safe as its checks; M5.4).
16. Routing in Next App Router: file-system routing, layouts, nested routes, route groups, dynamic segments, `searchParams`/`params` as promises (Next 15+), `generateStaticParams`, metadata API, `notFound()`, `redirect()`.
17. Next as a lab only: don't adopt Next patterns in production slotlock; you are testing models.
18. Alternatives: other RSC implementations and non-RSC server rendering (Remix/React Router framework mode loaders) differ; RSC is React's answer to data+component colocation.

### RESOURCES (in order)
1. React docs, "Server Components": https://react.dev/reference/rsc/server-components ; "`'use client'`": https://react.dev/reference/rsc/use-client ; "`'use server'`": https://react.dev/reference/rsc/use-server . Full.
2. Dan Abramov et al., "RSC From Scratch. Part 1: Server Components" (reactwg discussion): https://github.com/reactwg/server-components/discussions/5 . Work through the code.
3. Josh W. Comeau, "Making Sense of React Server Components": https://www.joshwcomeau.com/react/server-components/ .
4. Next.js docs (App Router): "Server and Client Components", "Composition Patterns", "Fetching Data", "Streaming/Loading UI": https://nextjs.org/docs/app/getting-started/server-and-client-components . Read fully; verify against the current version in the docs sidebar.
5. Vercel blog, "Understanding React Server Components": https://vercel.com/blog/understanding-react-server-components .
6. Delba de Oliveira's "Next.js App Router course": https://nextjs.org/learn . Do chapters 1 to 9 only (data fetching, streaming, partial prerendering) while keeping your four questions open.
7. React `cache` and `taint` docs: https://react.dev/reference/react/cache ; https://react.dev/reference/react/experimental_taintObjectReference .

### START HERE (first 30 minutes)
1. `bun create next-app` into `render-lab/next` (App Router, TypeScript, no Tailwind needed).
2. Make an async server component that queries Postgres directly and renders a list. Open the Network tab and view-source. Look for `_rsc` requests on navigation.
3. Add `'use client'` to a counter button. In the build output (`next build`), see the client chunk list.

### DRILLS
D1. Rebuild the bookings page in the Next lab: server component fetching from Postgres with `postgres.js`, `loading.tsx`, `error.tsx`, one client component for a "Confirm" button, one for a filter. Acceptance: measure client JS bytes versus the CSR and SSR variants.
D2. Serialization lab: pass each of these as props from a server component to a client component and record what happens: string, Date, Map, function, class instance, `Symbol`, a DB row object, a Promise (use with `use()`), JSX element. Table of results and the error messages.
D3. Payload reading: fetch a navigation payload with `curl -H 'RSC: 1' ...` (or from DevTools), find the client references (`I[...]`) and props; explain each line of the payload to a rubber duck. Show where data appears in both the HTML and the payload (duplication).
D4. Boundary placement: start with `'use client'` in the root layout; measure the bundle; push it down to leaves; measure again. Chart JS bytes.
D5. Donut pattern: a client `Modal` that accepts server-rendered `children` fetched on the server; prove the children's server-only library (e.g., `marked`) doesn't appear in the client bundle (bundle analyzer: `@next/bundle-analyzer`).
D6. Secret leak lab: import `db.ts` (with `process.env.DATABASE_URL`) into a client component and observe the build error from `server-only`; remove the guard and see what ships; add `server-only` back.
D7. Data waterfall: sequential awaits in a server component vs `Promise.all` vs separate Suspense boundaries; measure TTFB and time to complete with 300ms simulated query latency each.
D8. Props over-exposure: pass a full `user` row (with `password_hash`) to a client component; find it in the page source; fix by projecting a DTO. Write a rule: DTOs at the boundary.
D9. Authorization in server components: write the layout-level auth check, then show that a nested page rendered through a different path (a parallel route or direct navigation) can skip a layout check (layouts don't re-render on navigation); fix by checking in the data access layer (per query), not just in the layout.
D10. `React.cache` dedupe: fetch the same booking in a layout and a page; count DB queries with and without `cache`.
D11. Streaming: two slow Suspense boundaries (1s and 3s); observe chunked HTML in `curl -N`; map each chunk to a boundary.
D12. Write a 1-page "RSC in my own words": what runs where, what's serialized, what ships, what is cached, and three mistakes.

### BREAK
- Pass a function prop to a client component (error) and understand why.
- Read `cookies()` in a server component and see the whole route become dynamic.
- Put `'use client'` too high; measure the damage.
- Rely on a layout for auth and bypass it via a client navigation to a deeper segment.

### QUIZ (cold)
1. Server vs client components: what each can do and where each runs.
2. What does `'use client'` mean precisely?
3. What can be passed as props across the boundary and what can't, and why?
4. What is in the RSC payload?
5. How can a client component render server components?
6. How can a secret leak to the client in RSC, and how do you prevent it?
7. Why can server components reduce bundle size?
8. What changes on a client-side navigation?
9. Why must authorization live at the data access layer?
10. What does `React.cache` do?

### GATE
Spoken (6 minutes): "Why can't a function cross the server/client boundary, and what does the server actually send?" with the payload on screen.
Pass: quiz 9/10, D1 to D12, tag `gate/M8.3`.

### DELIVERABLE AND FAILURE MODES
Deliverable: Next lab page, serialization table, payload annotations, boundary charts.
Faking it: following the Next tutorial without answering the four questions at each step.

---

## M8.4 Server functions and actions

Hours: 12. Prerequisite: M8.3. Phase: 8.

### WHY
A server function looks like a local function call but is an HTTP endpoint you didn't name. If you can't say what request it makes, you can't secure it.

### ATOMS
1. Server Functions (`'use server'`; called Server Actions when used for mutations/forms): async functions that run on the server, callable from client code by reference; the framework generates an endpoint and an opaque ID; the client call serializes arguments, POSTs, and deserializes the result.
2. Transport: `POST` to the page URL (Next) with a `Next-Action: <id>` header and a body encoding of the arguments (React's reply serialization); one action invocation at a time per form by default; the response includes the new RSC payload for updated UI.
3. Every server function is a public HTTP endpoint: anyone can call it with `curl` if they can discover the action ID; IDs are not secrets. Authenticate and authorize inside each action; validate all inputs with Zod (arguments are untrusted, `FormData`/JSON); never trust hidden fields.
4. CSRF in actions: Next compares `Origin` to `Host`/allowed origins (`serverActions.allowedOrigins`) and uses `POST` only; SameSite cookies help; verify the behavior with attacker pages (M5.3 skills).
5. Closure capture and encryption: variables closed over by inline actions are encrypted into the payload by Next; still treat as sensitive; prefer explicit arguments and re-fetch authorization.
6. Progressive enhancement: `<form action={fn}>` works without JS (browser does a normal POST) when the action is passed as a server function; with JS, no full reload.
7. `useActionState(action, initialState)`: returns `[state, formAction, isPending]` to show validation errors and results; `useFormStatus` in child components; `useOptimistic` for instant UI.
8. Return values: return structured results (`{ok:false, fieldErrors}`) rather than throwing for expected failures; thrown errors reach error boundaries; redirect via `redirect()` (implemented by throwing).
9. Cache invalidation after mutation: `revalidatePath`, `revalidateTag`, `updateTag`/`refresh` (verify current names in the docs) to refresh server-rendered data (M8.6).
10. Idempotency still matters: server actions are subject to double submits and retries; use idempotency keys and DB constraints (M6.1).
11. Rate limiting and abuse: actions are POST endpoints; apply per-user limits; payload size limits (`bodySizeLimit`).
12. Observability: actions don't have nice URLs; add logging with an action name and request id; Sentry tags.
13. Where actions shouldn't be used: public APIs for third parties, webhooks, mobile clients (use route handlers/REST), file uploads at scale, long-running work (enqueue a job; M6.3).
14. Server Functions vs API route handlers vs tRPC: coupling, typing, portability, observability, and security boundaries; choose with reasons.
15. Known security incident lessons: React/Next had framework-level vulnerabilities in the server function protocol (for example, the late-2025 React Server Components "React2Shell" remote code execution vulnerability CVE-2025-55182); lesson: framework magic enlarges attack surface; keep patched, minimize exposed surface, and monitor advisories.

### RESOURCES (in order)
1. React docs, "Server Functions" and `useActionState`: https://react.dev/reference/rsc/server-functions ; https://react.dev/reference/react/useActionState .
2. Next.js docs, "Forms and Mutations" / "Updating Data" and "Data Security" guide: https://nextjs.org/docs/app/getting-started/mutating-data ; https://nextjs.org/docs/app/guides/data-security . Read the security guide in full.
3. Next.js docs, `serverActions` config: https://nextjs.org/docs/app/api-reference/config/next-config-js/serverActions .
4. Next.js blog, "How to Think About Security in Next.js": https://nextjs.org/blog/security-nextjs-server-components-actions .
5. OWASP CSRF CS (M5.3 reread), OWASP API Security Top 10 BFLA.
6. React blog security advisories: https://react.dev/blog (search "security advisory"); GitHub advisories for `facebook/react` and `vercel/next.js`: https://github.com/vercel/next.js/security/advisories .
7. Zod docs for `FormData` parsing (`zod-form-data` or manual `Object.fromEntries`).

### START HERE (first 30 minutes)
1. Add a server action `confirmBooking(id)` to the Next lab. Click the button and capture the POST in DevTools: URL, `Next-Action` header, body, response.
2. Reproduce the same call with `curl`, using your session cookie, from your terminal. It works. That is the lesson.

### DRILLS
D1. Request anatomy: document headers, body encoding and response of an action call; replay with `curl`; modify arguments (e.g., another booking id from another workspace) and show an IDOR if you hadn't checked authorization.
D2. Secure the action: authentication check from the session, authorization with `can()` (M5.6), Zod validation, idempotency key, rate limit, error contract (structured result). Tests call the action function directly and via HTTP.
D3. CSRF attack on actions: attacker page posts a form to the Next lab; show the `Origin` check blocks it; then show a misconfiguration (`allowedOrigins: ['*']` or a reverse proxy rewriting `Host`) allowing it; fix.
D4. Progressive enhancement: disable JS and submit the create-booking `<form action>`; it works via POST/redirect; enable JS and see no reload; handle validation errors in both modes with `useActionState`.
D5. `useOptimistic` plus `revalidatePath`: optimistic confirm with server revalidation; force failure and observe rollback.
D6. Closure capture: write an inline action closing over a secret; inspect the request payload; refactor to explicit arguments with server-side lookup.
D7. Compare three implementations of "cancel booking": server action, route handler called by `fetch`, and the existing Hono API; table: transport, typing, CSRF, caching, observability, who else can call it, testing ease.
D8. Reading advisory: read one real framework advisory (Next middleware auth bypass CVE-2025-29927 or the React Server Components RCE advisory) and write a 1-page analysis: attack, root cause, which of your design rules would have limited the impact.
D9. Logging: wrap actions in a helper that logs name, user, duration, result, request id; verify logs appear in Pino (M9.1 later; use console now).
D10. Decide and document in an ADR: "Mutations in slotlock go through the Hono API; server actions only in the lab" with reasons, or the reverse, with reasons.

### BREAK
- Remove the auth check in the action and call it with curl as an anonymous user.
- Pass a full object (with hidden fields) from a form and let the action trust it (mass assignment).
- Use a long-running task inside an action and watch timeouts; move it to a job.

### QUIZ (cold)
1. What HTTP request does a server action make?
2. Why is an action ID not a secret?
3. What must every action do before touching data?
4. How does Next defend actions against CSRF and how can that break?
5. What is progressive enhancement here?
6. What does `useActionState` return?
7. How do actions refresh server-rendered UI?
8. When should you not use actions?
9. What lesson do the framework advisories teach?
10. Why do idempotency keys still matter?

### GATE
Spoken (4 minutes): "What actually happens when I click this button that calls a server function?" including request, auth, validation, mutation, cache invalidation, response and UI update.
Pass: quiz 9/10, D1 to D10, tag `gate/M8.4`.

### DELIVERABLE AND FAILURE MODES
Deliverable: secured action, attack demos, ADR, advisory analysis.
Faking it: "it's just a function". If you can't show the curl, you fail.

---

## M8.5 Hydration bugs, streaming, Suspense

Hours: 10. Prerequisite: M8.4. Phase: 8.

### WHY
Hydration mismatches and slow streaming are the production bugs of SSR apps. Diagnosing them requires the model you built.

### ATOMS
1. Hydration algorithm: React renders the same component tree on the client and expects the same output as the server HTML; it attaches listeners and state; a mismatch triggers a warning and may discard server HTML (client re-render) or produce wrong UI.
2. Common mismatch causes: `Date.now()`/`new Date()` and timezone/locale formatting, `Math.random()`/`crypto.randomUUID()` in render, `typeof window` branches, reading `localStorage` during render, browser extensions mutating HTML, invalid HTML nesting (`<p>` inside `<p>`, `<div>` in `<p>`, `<table>` without `<tbody>`) causing the parser to restructure, whitespace/text differences, `Intl` differences between Node and browser ICU data, unstable IDs (use `useId`), responsive branches in JS (use CSS).
3. Fixes: render deterministic output on the first pass and update in an effect (`useEffect`/`useSyncExternalStore` with `getServerSnapshot`); `suppressHydrationWarning` only for unavoidable leaf text (timestamps); pass the server's time/locale to the client as props; use CSS for responsive layout; `useId` for IDs.
4. Selective hydration: with Suspense boundaries, React can hydrate parts independently and prioritize the part the user interacts with; HTML for unfinished boundaries can stream later; event replay for early clicks.
5. Streaming SSR mechanics: the server writes the shell, then for each suspended boundary a placeholder `<template>` with an ID and later an inline script that swaps the resolved HTML in (`$RC` functions); response uses `Transfer-Encoding: chunked` (HTTP/1.1) or H2 frames (M2.6).
6. Status codes and headers with streaming: once bytes are flushed, status and headers are committed; a late error can't change the 200 into a 500; redirects and `notFound()` must happen before the shell flushes; strategies: decide status before streaming, or error boundary UI after.
7. Suspense boundary design: granularity (too coarse blocks the shell; too fine causes popcorn), fallback dimensions to avoid layout shift, nested boundaries and reveal order (`SuspenseList` concept), which parts are critical for LCP (keep the LCP element in the shell).
8. Error boundaries: client error boundaries catch rendering errors in subtrees; `error.tsx` in Next; recoverable vs fatal; reporting to Sentry (M9.2).
9. Transitions: `startTransition`/`useTransition` mark updates as non-urgent, keeping the old UI while new content loads (avoid hiding content with a Suspense fallback); `useDeferredValue` for expensive derived renders.
10. `use()` hook: read a Promise passed from a server component inside a client component with Suspense; promise identity must be stable.
11. Debugging tools: React DevTools Profiler and Components (highlight updates), Chrome Performance panel (hydration tasks, long tasks), React's hydration error diffs (React 19 shows a diff), `curl -N` to see streamed chunks, Network tab "Waiting for server response" timings.
12. Hydration cost reduction: ship less JS (server components, islands), lazy-load below-the-fold client components (`dynamic()`/`lazy`), avoid giant client props, reduce component count, `content-visibility`.
13. Third-party scripts: load with `defer`/`async`/`next/script` strategies; they often cause the worst INP; measure before and after removal.
14. The `<head>` in streaming: metadata must be resolved before the shell flushes for crawlers; framework metadata APIs handle this.
15. Resilience: what happens when the client JS never arrives (no hydration): which interactions still work (links, forms with real actions), and which don't.

### RESOURCES (in order)
1. React docs, `hydrateRoot` (common errors section), "Hydration mismatch errors" in React 19 diff docs: https://react.dev/reference/react-dom/client/hydrateRoot#my-server-rendered-html-gets-recreated-from-scratch ; React docs `<Suspense>`: https://react.dev/reference/react/Suspense .
2. Next.js docs, "Hydration error" explanation: https://nextjs.org/docs/messages/react-hydration-error .
3. Dan Abramov / reactwg, "New Suspense SSR Architecture in React 18": https://github.com/reactwg/react-18/discussions/37 . Read fully.
4. React docs, `useTransition`, `useDeferredValue`, `use`: https://react.dev/reference/react/useTransition ; https://react.dev/reference/react/use .
5. web.dev, "Optimize long tasks" and "Third-party JavaScript performance": https://web.dev/articles/optimize-long-tasks ; https://web.dev/articles/optimizing-content-efficiency-loading-third-party-javascript .
6. Next.js docs, Loading UI and Streaming: https://nextjs.org/docs/app/getting-started/linking-and-navigating#streaming (or the Streaming guide in the current docs).
7. Re-read M2.6 notes on chunked transfer.

### START HERE (first 30 minutes)
1. In the Next lab, render `new Date().toLocaleString()` in a server component and a client component. Reload and read the hydration warning in the console.
2. Fix it two ways (deterministic initial render; `suppressHydrationWarning`) and say which is right in which case.

### DRILLS
D1. Mismatch gallery: reproduce 8 causes (date, random, window check, localStorage, bad nesting, locale, extension simulation via DevTools DOM edit before hydration, unstable id); record the warning text and the fix for each in `docs/hydration-bugs.md`.
D2. Streaming observation: two Suspense boundaries with 1s and 3s delays; `curl -N` the page and save the chunks; map placeholders to the swaps; read the injected `$RC` script.
D3. Status code trap: call `notFound()` after the shell has flushed (inside a Suspense boundary) and see the 200 status; restructure so the status is correct (check before the boundary) and test with `curl -i`.
D4. Suspense granularity experiment: coarse (one boundary), fine (every row), and balanced; measure FCP, LCP, CLS and perceived "popcorn" with a screen recording; choose.
D5. Selective hydration demo: a slow-hydrating heavy component beside a button; click the button at load under CPU throttling; show that React prioritizes hydration of the clicked boundary (event replay); compare with a non-Suspense version.
D6. Transition fix: a filter input that suspends and replaces the list with a fallback on every keystroke; fix with `useTransition`/`useDeferredValue` and keep the old list visible with a pending indicator.
D7. Hydration cost reduction: take a heavy client component page (a calendar), profile hydration time, then (a) lazy-load below the fold, (b) move static parts to server components, (c) drop a heavy library; chart the long tasks before/after.
D8. Third party: add a fake analytics script that blocks the main thread for 200ms; measure INP; load it with `defer`/after idle; remeasure.
D9. No-JS test: with JS disabled, list which slotlock lab pages work (navigation via links, forms with actions) and which don't; make the booking form work with no JS.
D10. Write the "hydration debugging checklist" (10 steps) that you will use in a production incident; test it on a bug a friend (or you, secretly) injects.

### BREAK
- Put a `<div>` inside a `<p>` and read the mismatch.
- Throw in a Suspense boundary after flush and see how the error surfaces to the client.
- Use `Math.random()` in an `id` attribute.

### QUIZ (cold)
1. What does hydration require to succeed?
2. List six causes of mismatches and their fixes.
3. When is `suppressHydrationWarning` acceptable?
4. How does streaming SSR deliver late content?
5. Why can't a streamed response change its status code, and what do you do?
6. What is selective hydration?
7. `useTransition` vs `useDeferredValue`?
8. How do you decide Suspense granularity?
9. What does a third-party script do to INP?
10. How would you debug a hydration error that only happens for some users?

### GATE
Spoken (4 minutes): from a streamed chunk arriving to a boundary becoming interactive: placeholder, inline swap script, hydration, event replay.
Pass: quiz 9/10, D1 to D10, tag `gate/M8.5`.

### DELIVERABLE AND FAILURE MODES
Deliverable: hydration bug gallery, streaming captures, debugging checklist.
Faking it: `suppressHydrationWarning` everywhere. It's a smell; justify each use.

---

## M8.6 Caching models in Next

Hours: 12. Prerequisite: M8.5. Phase: 8.

### WHY
Framework caching layers stack on top of HTTP caching (M2.4) and your query cache (M7). Most "stale data" bugs are an unknown layer answering. Note: Next's caching model has changed across versions (13/14 implicit caching, 15 mostly opt-in, 16 Cache Components with `use cache`); always read the docs for the installed version, then use the layer model.

### ATOMS
1. Layer map (draw it): browser HTTP cache; browser router/client cache; CDN; server full-route output cache; data/function cache; request memoization; Postgres; plus your TanStack Query cache for client fetches.
2. Request memoization (React `cache`/`fetch` dedupe within one render pass): same call, same render, one execution; ends after the request.
3. Data cache / `use cache` (Next 16 Cache Components): caches function/component output across requests with a lifetime (`cacheLife`: seconds, minutes, hours, days, weeks, max, or custom) and tags (`cacheTag`) for invalidation; keys derived from arguments and closed-over values; only for deterministic, non-personalized data unless keyed by user.
4. Full route cache/static rendering: routes that don't use dynamic APIs can be prerendered at build and served from cache; dynamic APIs (`cookies()`, `headers()`, `searchParams` in some modes, uncached `fetch` with dynamic behavior) opt a route (or a Suspense-bounded part with PPR) into per-request rendering.
5. Partial prerendering / Cache Components: static shell + cached components + dynamic holes streamed; the mental model: "everything is dynamic by default; you opt specific parts into caching with `use cache`" (verify against the installed version's docs).
6. Router (client) cache: the browser stores RSC payloads of visited/prefetched routes for instant back/forward and `<Link>` prefetch; staleness windows (`staleTimes`); invalidated by `router.refresh()`, server action revalidation, or cookie changes.
7. Invalidation APIs: `revalidatePath(path)`, `revalidateTag(tag)`, `updateTag(tag)` (read-your-own-writes in actions), `refresh()`, time-based `cacheLife`; on-demand invalidation from webhooks (M6.3) and jobs.
8. Personalization and cache keys: never `use cache` on user-specific data without user in the key; cached components must not read cookies/headers inside (pass values as arguments); data leakage across users is the worst caching bug (M2.4 shared cache leakage).
9. CDN interaction: `Cache-Control` from Next for static assets (`immutable` for hashed `_next/static`), `s-maxage`/`stale-while-revalidate` for ISR/static pages, `Vary`, platform behaviors (Vercel Data Cache and CDN, self-hosted `next start` with a shared cache handler for multi-instance).
10. Multi-instance deployments: local filesystem caches diverge between instances; need a shared cache handler (Redis or object storage) or a platform that provides one; `revalidate` calls hitting a different instance.
11. Debugging: cache logs (`logging.fetches.fullUrl`), response headers (`x-nextjs-cache: HIT|MISS|STALE`, `x-vercel-cache`), `next build` route table (static/dynamic markers), DevTools "disable cache", bypass with unique query strings, version-specific debug flags.
12. Stale data decision matrix: which data can be stale and for how long (resource list: minutes; availability: seconds or live; user account: never cached shared; pricing: tag-invalidated on change).
13. Correctness under mutation: after a booking mutation, which layers must be invalidated (query cache, router cache, route/data cache, CDN), in which order, and how to test it.
14. Cache stampede/thundering herd on expiry: coalescing, stale-while-revalidate, jittered TTLs, locks (concept; M10.1).

### RESOURCES (in order)
1. Next.js docs, Caching and Revalidating (current version): https://nextjs.org/docs/app/getting-started/caching and "Caching" guide under App Router; `use cache`, `cacheLife`, `cacheTag`, `revalidateTag`, `updateTag`, `refresh`: https://nextjs.org/docs/app/api-reference/directives/use-cache .
2. Next.js docs, "Partial Prerendering" / "Cache Components": https://nextjs.org/docs/app/getting-started/cache-components .
3. Next.js blog posts for the version you installed (14 to 16) on caching changes: https://nextjs.org/blog . Read "Next.js 15" caching changes and "Next.js 16" Cache Components.
4. MDN, HTTP caching (re-read) and RFC 9111: https://developer.mozilla.org/en-US/docs/Web/HTTP/Guides/Caching ; https://www.rfc-editor.org/rfc/rfc9111 .
5. Vercel docs, "Data Cache" and "CDN Cache": https://vercel.com/docs/data-cache ; https://vercel.com/docs/edge-network/caching (verify current paths).
6. Next.js docs, self-hosting: "Caching and ISR" for multi-instance: https://nextjs.org/docs/app/guides/self-hosting .
7. Re-read M7.2 (query cache) and M2.4 notes.

### START HERE (first 30 minutes)
1. Draw the layer map: browser, router cache, CDN, route cache, data cache, memoization, DB, TanStack cache. Put one arrow for a `GET /bookings/88` request showing the layers it may stop at.
2. In the Next lab, run `next build` and read the route table: which routes are static and which dynamic, and guess why.

### DRILLS
D1. Layer map document: for each layer, who owns it, key, lifetime, invalidation, and how to observe a hit/miss; commit `docs/caching-layers.md` with the diagram.
D2. Experiment matrix: for a page showing resources (stable) and availability (changing): configure (a) fully dynamic, (b) cached resources with `cacheLife('minutes')` and dynamic availability, (c) fully static with tag revalidation. For each: response headers, route table, server logs for DB queries, number of DB calls over 100 requests, p95.
D3. Staleness proof: change a resource name in the DB directly; show how long each layer serves the old name; use `revalidateTag` to flush; show instant update.
D4. Personalization leak lab: cache a function that reads `cookies()` internally or key-less user data in a shared cache; hit it as user A then B and show leakage; fix by passing the user id as an argument and/or not caching.
D5. Router cache: navigate A to B to A; show instant back; change data; use `router.refresh()` and `revalidatePath` from an action; show which layers refresh; document staleTimes behavior in your installed version.
D6. Webhook-driven invalidation: expose `POST /api/revalidate` verified by HMAC (M6.3); a DB change triggers it; show the page updating without a deploy.
D7. Multi-instance test: run two `next start` instances behind Caddy (round robin); show inconsistency of local caches after `revalidateTag` hits only one instance; fix with a shared cache handler or by routing revalidation to all instances; document.
D8. CDN simulation: put Caddy or `varnish`-like caching in front of the lab; confirm static assets get `immutable` and HTML does not leak personalized content (`Cache-Control: private` on personalized routes).
D9. Compare debugging: cause a stale data bug on purpose (a tagged cache missing invalidation after a mutation); then use your layer map and logs to find the layer within 15 minutes; write the steps as a runbook.
D10. Decision matrix for slotlock (data types x staleness x invalidation trigger x layers) in NOTES.

### BREAK
- Cache a user-specific function key-less and leak data.
- Forget to tag a cached query then wonder why the mutation doesn't refresh it.
- Run two instances without shared cache and confuse customers.
- Trust the client cache after logout (router cache shows previous user's page on back button); verify and fix (clear on logout, `Cache-Control: no-store` for authenticated HTML).

### QUIZ (cold)
1. List every cache layer between a user and Postgres in a Next app.
2. What does request memoization do and when does it end?
3. What does `use cache` key on?
4. Which APIs make a route dynamic?
5. What does the router cache do and how is it invalidated?
6. `revalidatePath` vs `revalidateTag` vs `updateTag`?
7. How can personalized data leak through a cache?
8. What breaks with multiple instances?
9. How do you tell which layer served a response?
10. Which data in slotlock can be stale and for how long?

### GATE
Spoken (5 minutes): "My page shows old data. Walk me through finding which cache is lying" using your layer map and tools.
Pass: quiz 9/10, D1 to D10, tag `gate/M8.6`.

### DELIVERABLE AND FAILURE MODES
Deliverable: layer map, experiment matrix, leak lab, runbook.
Faking it: memorizing API names. If you can't diagram the layers cold, you fail.

---

## M8.7 Steelman, plus Solid lab

Hours: 6. Prerequisite: M8.6. Phase: 8.

### WHY
Senior engineers argue for the option they would not choose. You will write the strongest case for each rendering model, and touch a different reactivity model to loosen React's grip on your thinking.

### ATOMS
1. Steelmanning: state the opposing position so well its holders would endorse it; then name the conditions under which it wins.
2. Axes: team size and skill, time to market, SEO, personalization, interactivity density, device and network profile, hosting constraints, cost, ops complexity, hiring pool, longevity.
3. Cases for CSR: internal tools, authenticated dashboards, app-like experiences, simple hosting on a CDN, cheap servers, offline/PWA.
4. Cases for SSR/streaming: content with SEO needs, slow devices, first impressions, public pages with dynamic data.
5. Cases for SSG/ISR: docs, blogs, marketing; resilience and cost.
6. Cases for islands (Astro): content-heavy sites with limited interactivity; minimal JS.
7. Cases for RSC: data-heavy trees, colocated data access, reduced bundle, but with complexity, framework coupling, and a security surface; costs: mental model, debugging, ecosystem fit.
8. Cases for no framework: a server-rendered templates + sprinkles (HTMX/Alpine, Rails/Django/Laravel style) for CRUD apps; steelman it honestly (HTMX docs and "hypermedia systems").
9. Fine-grained reactivity (Solid, Svelte 5 runes, Vue): updates run only the code that depends on a changed signal; no virtual DOM diff; components run once; different mental model from React re-rendering; SolidStart does SSR/streaming with server functions.
10. Signals: reading a signal inside a tracking scope subscribes; writing triggers dependents; why destructuring props can break reactivity in Solid.
11. Choosing a stack for a real brief: write the one-paragraph recommendation with the top two risks.
12. Anti-patterns: choosing by hype; choosing by only what you know; rewriting without measurement.

### RESOURCES (in order)
1. HTMX docs, "Essays" and "Hypermedia Systems" (free web book): https://htmx.org/essays/ ; https://hypermedia.systems/ . Read the first 3 chapters.
2. Solid docs: Fundamentals (signals, effects, memos, control flow), "Reactivity": https://docs.solidjs.com/ . SolidStart intro: https://docs.solidjs.com/solid-start .
3. Ryan Carniato, "Solid.js: Reactivity" and "Fine-Grained Reactivity" talks/articles: https://dev.to/ryansolid/a-hands-on-introduction-to-fine-grained-reactivity-3ndf .
4. Astro docs, "Why Astro?": https://docs.astro.build/en/concepts/why-astro/ .
5. Rich Harris, "Frameworks, the compiler and the disappearing framework" (conceptual, Svelte): https://svelte.dev/blog (browse; optional).
6. Your own M8.2 metrics for evidence.

### START HERE (first 30 minutes)
1. Write six one-sentence positions: "CSR is best when...", "SSR...", "SSG...", "islands...", "RSC...", "HTMX-style server rendering...".
2. Pick the one you like least and argue for it for 5 minutes (voice memo).

### DRILLS
D1. Steelman document: 1 page per model (CSR, SSR/streaming, SSG/ISR, islands, RSC, hypermedia) with: the strongest argument, three conditions where it wins, one condition where it hurts, one piece of evidence from your lab.
D2. Solid lab: recreate the bookings list (client-side) in Solid with a resource and a mutation; count the updates when one row changes (Profiler or log); compare with React.
D3. Signals mini implementation: 60 lines (`signal`, `effect`, `computed`) with automatic dependency tracking; tests for glitch-free updates (diamond dependency).
D4. Three briefs: (a) a docs site for an open-source library, (b) a B2B admin tool for 20 internal users, (c) a public booking page for 100k monthly visitors with a personalized header. For each: stack recommendation, the four-question answers, the top two risks, and the metric you'd measure to validate.
D5. Peer review: have someone (friend, community) argue the opposite for brief (c); write the counter-arguments you hadn't considered.
D6. "Anti-hype" rule: write your personal rule for adopting new tech (what evidence, what trial, what exit plan) and commit to NOTES.

### BREAK
- Argue for your least favorite choice for brief (c) and find where it truly fails.
- Break Solid's reactivity by destructuring props and observe.

### QUIZ (cold)
1. Strongest case for CSR.
2. Strongest case for SSG.
3. Strongest case for RSC; the strongest case against.
4. What is a signal and how does dependency tracking work?
5. How does fine-grained reactivity differ from React's rendering model?
6. When would HTMX-style server rendering beat an SPA?
7. Which metrics would validate your choice for a public booking page?
8. What is your rule for adopting new frameworks?
9. What are two common reasons for a rewrite and when is a rewrite justified?
10. Name the top risk of each of the six models.

### GATE
Spoken (4 minutes): argue for the model you reject, then explain why you still reject it for the booking page.
Pass: quiz 9/10, D1 to D6, tag `gate/M8.7`.

### DELIVERABLE AND FAILURE MODES
Deliverable: steelman doc, Solid lab, signals implementation, three briefs.
Faking it: balanced-sounding fluff without conditions or evidence.

---

## M8.8 Phase 8 exit talk

Hours: 6. Prerequisite: M8.7. Phase: 8.

### WHY
Teaching under constraints exposes gaps. A recorded 10-minute talk with no slides is the exam.

### ATOMS
1. Talk structure: problem (one concrete page), four questions, three models with timelines, measurements from your lab, hydration and streaming, caching layers, decision rule.
2. Constraints: 10 minutes, no slides (a whiteboard/paper/sketch is allowed), no reading from notes, no framework jargon without defining it, one live demo allowed (60 seconds).
3. Recording: screen/voice (OBS), phone camera, or a friend; 1 take allowed after the first review; transcript review for filler words and vague claims.
4. Evaluation rubric (below).
5. Public sharing as a portfolio artifact (unlisted video is fine).

### RESOURCES (in order)
1. Re-read your M8.1 to M8.7 notes only.
2. OBS Studio: https://obsproject.com/ . Whiteboard options: Excalidraw https://excalidraw.com/ .
3. Optional: any one conference talk on rendering for style (for example Ryan Florence, Jason Miller, or Dan Abramov talks on RSC/React); watch once for structure, not content.

### START HERE (first 30 minutes)
1. Write the 10-minute outline as 10 sentences, one per minute.
2. Practice minute 1 and 2 aloud.

### DRILLS
D1. Outline, rehearsal 1 (alone), rehearsal 2 (to a person or a mirror).
D2. Record take 1; review with the rubric; list the 5 weakest claims; fix; record take 2.
D3. Transcribe take 2; strike filler and unsupported claims; write a 1-page summary from the transcript.
D4. Q&A: have 5 hostile questions asked (or write them yourself) and answer each in 90 seconds on tape.
D5. Add the video link and the summary to the portfolio/README.

### BREAK
- Do the talk with a timer that cuts you off at 10:00.
- Do it once deliberately without jargon (to a non-technical friend).

### QUIZ (cold)
Use your own 10 hostile questions from D4; the quiz is answering 9 acceptably on a second pass without notes.

### PHASE 8 EXIT RUBRIC (all must be yes)
1. Four questions explained and applied to a real page.
2. A timeline drawn for CSR, SSR and streaming with correct labels.
3. At least three numbers from `render-metrics.md` used to support claims.
4. The server/client boundary and RSC payload explained correctly.
5. A server function explained as an HTTP endpoint with auth/validation.
6. Hydration mismatch and the uncanny valley explained with fixes.
7. Cache layers named and staleness debugging described.
8. A stated decision rule with named conditions.
9. Under 10 minutes, no slides, no notes.

### GATE
Pass: rubric all yes (self-graded, then graded by a peer or an LLM given the rubric and your transcript, with you reconciling disagreements), quiz 9/10, tags `gate/M8.8` and `gate/phase8`.

### DELIVERABLE AND FAILURE MODES
Deliverable: recorded talk, transcript, summary.
Faking it: reading a script. The Q&A is the test.


---

# PHASE 9. PRODUCTION

Repo: `slotlock` (API, web, jobs) plus a real VPS. From here you run what you built. Every module ends with something observable in production or a document a hiring manager can read. Budget: a small VPS (about 5 to 10 USD/month) and a domain; keep the managed deploy from M6.4 as the staging environment.

---

## M9.1 Structured logs and request identity

Hours: 8. Prerequisite: M8.8. Phase: 9.

### WHY
When something breaks at 2 a.m., logs are the first and sometimes only evidence. Unstructured `console.log` can't be queried; logs without a request id can't be correlated.

### ATOMS
1. Logs vs metrics vs traces: logs are discrete events with context (high cardinality OK, expensive at scale); metrics are aggregated numbers over time (cheap, low cardinality); traces are causal timelines of one request across components. Use all three for different questions.
2. Structured logging: one JSON object per line (NDJSON) with stable keys (`time`, `level`, `msg`, `service`, `env`, `version`, `requestId`, `userId`, `workspaceId`, `route`, `method`, `status`, `durationMs`, `err`).
3. Levels: `fatal`, `error` (needs human action), `warn` (unexpected but handled), `info` (business and lifecycle events), `debug`/`trace` (off in prod by default); changing level at runtime.
4. Request identity: generate a request id at the edge (or accept a validated inbound `X-Request-Id`), store it in `AsyncLocalStorage`, include it automatically in every log line, return it in the response header and in error bodies, pass it to downstream calls and jobs.
5. `AsyncLocalStorage` (`node:async_hooks`): context that follows async call chains without parameter threading; how it works (async resource propagation); Bun support; pitfalls (lost context with some callbacks/event emitters, overhead).
6. Pino: fast JSON logger; child loggers (`logger.child({requestId})`); serializers for `err`, `req`, `res`; `redact` paths (`req.headers.authorization`, `*.password`, `*.token`); transports run off-thread (`pino.transport`); pretty printing only in dev (`pino-pretty`).
7. Access log line per request (method, route template not raw path to limit cardinality, status, duration, bytes, user agent hash, ip policy).
8. What not to log: passwords, tokens, session ids, full request bodies, secrets, PII (emails in plain text depend on policy), payment data; log ids not content; GDPR retention.
9. Logging errors correctly: log once at the boundary with stack and context (not at every layer), include `cause` chain, distinguish operational errors (expected, 4xx) from programmer errors (bugs, 5xx), don't log-and-rethrow repeatedly.
10. Log volume and cost: sampling (log 1 in N debug lines), rate-limited repeated errors, avoiding per-row logs in loops, log size limits.
11. Log aggregation: stdout to the platform's collector; self-hosted options (Loki + Grafana, Vector/Fluent Bit shippers, OpenSearch) and SaaS (Axiom, Datadog, Better Stack); query languages (LogQL); retention and indexing trade-offs.
12. Correlation across services/jobs: include `requestId` in job payloads and webhook deliveries; `traceparent` header (W3C Trace Context) for traces (M9.3).
13. Audit logs vs application logs: audit logs are product data with retention rules (M5.6), not debug logs.
14. Time: UTC ISO timestamps (or epoch ms), monotonic duration measurement with `performance.now()`/`process.hrtime.bigint()`.
15. Testing logs: assert on emitted records for critical events (login failure, permission denial) using a test destination.

### RESOURCES (in order)
1. Pino docs: https://getpino.io/ . Read: API (child, serializers, redact), transports, "Pino in Production"/best practices, web framework integrations: https://getpino.io/#/docs/web .
2. Node.js docs, `AsyncLocalStorage`: https://nodejs.org/api/async_context.html . Full.
3. OWASP Logging Cheat Sheet: https://cheatsheetseries.owasp.org/cheatsheets/Logging_Cheat_Sheet.html . Full.
4. Google SRE book, "Monitoring Distributed Systems" (chapter 6): https://sre.google/sre-book/monitoring-distributed-systems/ . Read the logs/metrics sections and the four golden signals.
5. Grafana Loki docs, "Get started" and LogQL basics: https://grafana.com/docs/loki/latest/get-started/ ; https://grafana.com/docs/loki/latest/query/ .
6. Charity Majors, "Structured logs and observability" (Honeycomb blog, wide events): https://www.honeycomb.io/blog/ (search "wide events"); and Stripe's "canonical log lines": https://stripe.com/blog/canonical-log-lines . Read the Stripe post fully.
7. W3C Trace Context: https://www.w3.org/TR/trace-context/ (sections 1 to 3).
8. Hono `hono/logger` and `requestId` middleware docs: https://hono.dev/docs/middleware/builtin/request-id .

### START HERE (first 30 minutes)
1. Replace one `console.log` in slotlock with `pino` and run `jq . < logs`. Observe the shape.
2. Hit the API twice and write down the question you could not answer from the logs (which request, which user, how long).

### DRILLS
D1. Logging module `logger.ts`: Pino config with env-based level, `redact` list, serializers, base fields (`service`, `env`, `version` from git SHA).
D2. Request middleware: generate id (validated format, replace untrusted values), store in `AsyncLocalStorage`, child logger available via `getLogger()`, `X-Request-Id` response header, one "canonical log line" per request (route template, status, duration, userId, workspaceId, bytes in/out, db query count and total db ms collected via a per-request counter).
D3. Error handling: global error handler logs once with stack and `requestId`; operational vs programmer errors; 5xx logs include `cause`; error body returns requestId (M6.1). Test by throwing in a handler.
D4. Propagation: `requestId` included in job payloads, job logs, outbound webhook deliveries (`X-Request-Id`), and SQL comments (`/* requestId=... */` optional) so a Postgres slow query can be tied to a request.
D5. Redaction tests: log objects with `password`, `authorization`, `set-cookie`, `token`; assert none appear in output. Add a CI test that greps a log capture of the integration suite for known fake secrets.
D6. Ship logs: run Loki + Grafana (+ Vector or Promtail) with docker compose locally; ingest slotlock stdout; build a Grafana Explore query that shows all lines for one requestId, error rate by route, and the slowest 10 requests in the last hour.
D7. Volume experiment: add logging inside a hot loop, measure throughput difference (autocannon) with sync vs async destination; set a rule for hot paths.
D8. Incident drill: a teammate (or you, secretly, next day) changes one bug in the code; you find the failing request starting only from a user's report ("my booking failed at about 14:05") using logs. Time it.
D9. Cardinality: log raw paths vs route templates and show the explosion in a label-based store (Loki labels); decide labels (service, env, level) vs fields (requestId).
D10. Log policy doc: what we log, retention, PII rules, who can read logs, how to purge on a deletion request.

### BREAK
- Log a full request body with a password and then redact properly.
- Lose the ALS context by using a callback pattern that escapes it; detect with a test and fix.
- Log at `debug` in prod and measure the cost.
- Use raw `req.url` in a metric label and watch the cardinality.

### QUIZ (cold)
1. Logs vs metrics vs traces: when each?
2. What are the keys of a good log line?
3. How does `AsyncLocalStorage` carry a request id?
4. What must never be logged?
5. Where should an error be logged and why only once?
6. What is a canonical log line?
7. How do you correlate an API request, its job and its webhook?
8. What's high cardinality and why does it matter?
9. How do you control log cost?
10. How do you find one user's failing request?

### GATE
Spoken (4 minutes): from a user report "booking failed at 14:05" to the root cause using logs only, naming each query and field.
Pass: quiz 9/10, D1 to D10, tag `gate/M9.1`.

### DELIVERABLE AND FAILURE MODES
Deliverable: logger, middleware, redaction tests, Loki/Grafana dashboard JSON, log policy.
Faking it: `console.log(JSON.stringify(...))` and calling it structured. The request-id correlation across jobs is the proof.

---

## M9.2 Error tracking, source maps, one business metric

Hours: 8. Prerequisite: M9.1. Phase: 9.

### WHY
Logs tell you what the server saw; error trackers tell you what users hit, grouped and deduped, with the stack mapped to source. You also add the first business metric, because uptime is not value.

### ATOMS
1. Error tracking concepts: events grouped into issues by fingerprint (stack + message); occurrence counts, affected users, first/last seen, release association, regressions.
2. Sentry SDK setup: DSN (public identifier; not a secret but treat sensibly), `environment`, `release` (git SHA), `tracesSampleRate`, `sendDefaultPii: false`, `beforeSend` to scrub, `ignoreErrors`, `denyUrls`.
3. Server integration (Hono/Node/Bun): capture unhandled exceptions/rejections, handler errors, job failures; set user context (id only), tags (`workspaceId`, `route`), breadcrumbs, request id as a tag linking to logs.
4. Client integration (React): `Sentry.init`, error boundaries (`Sentry.ErrorBoundary`), unhandled promise rejections, console breadcrumbs, network breadcrumbs, `captureException` in query/mutation error handlers (only unexpected ones, not 4xx validation).
5. Source maps: the minified bundle's stack trace maps to original code via `.map` files; generate (`build.sourcemap: 'hidden'` in Vite), upload to Sentry in CI with `sentry-cli`/Vite plugin tied to the `release`, don't serve maps publicly (or accept the trade-off), verify by throwing a test error and reading the original file/line.
6. Release tracking and deploys: associate commits and releases, mark regressions, "resolved in next release", suspect commits.
7. Alerting rules: new issue, issue regression, spike frequency, error rate threshold; route to Slack/email; avoid alert fatigue (see M9.3 on actionable alerts).
8. Sampling and quota: events per project; rate limits; sampling of noisy errors; `beforeSend` dedupe; grouping rules with custom fingerprints.
9. Privacy: scrub PII, avoid sending bodies, IPs; data processing agreements; self-hosting (GlitchTip, Sentry self-hosted) as an alternative.
10. Business metrics (product observability): bookings created per hour, confirmation rate, cancellations, overlap conflicts, idempotent replays, time-to-first-booking, active workspaces; measure outcomes not just plumbing.
11. Where to emit business metrics: a counter in app code with labels (low cardinality), or events to the DB and a SQL view; a tiny internal `/admin/metrics` page or a Grafana panel from SQL.
12. SLIs for the business: "booking confirm success rate", "p95 time to confirm"; used by M9.3.
13. User feedback: Sentry user feedback widget or an error page showing the request id to quote to support.
14. Release hygiene: tag releases with the git SHA in logs, Sentry and the UI footer (`/version` endpoint).

### RESOURCES (in order)
1. Sentry docs for Node/Bun/Hono and React: https://docs.sentry.io/platforms/javascript/guides/node/ ; https://docs.sentry.io/platforms/javascript/guides/react/ ; "Source Maps": https://docs.sentry.io/platforms/javascript/sourcemaps/ . Read setup, configuration options, source maps, releases.
2. Sentry docs, "Data Management" (scrubbing sensitive data): https://docs.sentry.io/security-legal-pii/scrubbing/ .
3. Sentry docs, Alerts: https://docs.sentry.io/product/alerts/ .
4. GlitchTip (open-source Sentry-compatible): https://glitchtip.com/documentation .
5. Vite docs, `build.sourcemap`: https://vite.dev/config/build-options.html#build-sourcemap .
6. Google SRE book, "Service Level Objectives" (chapter 4): https://sre.google/sre-book/service-level-objectives/ . Read SLI definition and examples.
7. Alistair Croll and Ben Yoskovitz, *Lean Analytics* (concepts of one metric that matters) or a blog summary: https://leananalyticsbook.com/ (optional).

### START HERE (first 30 minutes)
1. Create a Sentry project (or GlitchTip), initialize it in the API, and add a `GET /debug/boom` route (admin only) that throws.
2. Hit it; see the issue; read the stack.

### DRILLS
D1. Server integration: capture handler errors and job failures with tags `requestId`, `workspaceId`, `route`, `jobType`; user context id only; `sendDefaultPii: false`; `beforeSend` scrubs cookies and auth headers. Test by throwing in a handler and in a job.
D2. Client integration: init in the web app, error boundary at route level and at app root, network breadcrumbs, capture unexpected errors from mutations (skip expected `ApiError`s with `code`).
D3. Source maps: build with hidden sourcemaps, upload in CI keyed by release SHA; throw a client error in production build; screenshot the Sentry issue showing the original TypeScript line. Verify that without upload the stack is minified.
D4. Release tracking: set `release` on server and client with the git SHA; expose `/version`; deploy a fix and see "resolved in release" and a regression when you reintroduce the bug.
D5. Alerting: rules for new issue (Slack/email), regression, and error spike (more than 10 events in 5 minutes); trigger each deliberately.
D6. Triage practice: create 5 different bugs (null deref, unhandled rejection, network failure, validation edge, a failing job); for each, find in Sentry: first occurrence, release, affected user, request id, link to logs, and fix.
D7. Business metric implementation: `bookings_created_total`, `bookings_confirmed_total{result}`, `booking_conflicts_total`; surface as a SQL-based Grafana panel or an `/admin/metrics` page. Choose one "metric that matters" and write why in NOTES.
D8. SLI definition: "confirm success rate" = confirmed / (confirmed + server errors); compute from data for the last 24h; propose a target (SLO) and an error budget.
D9. Error page with request id and "copy" button; support flow in docs: how a user report maps to a log query (M9.1) and a Sentry issue.
D10. Privacy review: send a test event containing an email and a bearer token in the URL; show scrubbing; document what you send.

### BREAK
- Ship sourcemaps publicly and read the source from the browser; decide policy.
- Send every 404 and validation error to Sentry and drown the signal; fix with filters.
- Forget to set `release` and try to find which deploy introduced a bug.

### QUIZ (cold)
1. How are Sentry issues grouped?
2. How do source maps work and what happens without them?
3. What is a release and why tag it?
4. What should and shouldn't be sent to an error tracker?
5. Why one business metric?
6. Define an SLI and an SLO with an example.
7. What belongs in an error page shown to users?
8. How do you link Sentry to logs?
9. What's alert fatigue and how do you prevent it?
10. How would you triage a spike after a deploy?

### GATE
Spoken (4 minutes): deploy a bug on purpose; explain how you learn of it (alert), find the release and line (Sentry + source maps), and the request (log query).
Pass: quiz 9/10, D1 to D10, tag `gate/M9.2`.

### DELIVERABLE AND FAILURE MODES
Deliverable: Sentry (or GlitchTip) on server and client with sourcemaps, alerts, business metric panel, SLI doc.
Faking it: installing the SDK and never reading an issue. The triage drill is the evidence.

---

## M9.3 RED metrics and one trace

Hours: 10. Prerequisite: M9.2. Phase: 9.

### WHY
Metrics answer "is it broken, how badly, since when?" cheaply. Traces answer "where did the time go?" You will implement RED metrics, build one dashboard, one alert you would actually page on, and one distributed trace.

### ATOMS
1. Golden signals (SRE): latency, traffic, errors, saturation. RED (request-oriented): Rate, Errors, Duration. USE (resource-oriented): Utilization, Saturation, Errors.
2. Metric types (Prometheus): counter (monotonic; use `rate()`), gauge (up/down), histogram (bucketed observations; compute quantiles with `histogram_quantile`), summary (client-side quantiles; not aggregatable).
3. Labels: dimensions (`method`, `route`, `status_class`); keep cardinality low (never user id, raw path or request id); each unique label set is a time series.
4. Histogram buckets: choose around SLO thresholds (e.g., 0.05, 0.1, 0.25, 0.5, 1, 2.5, 5, 10 seconds); averages hide tail latency; use p50/p95/p99; percentiles can't be averaged across instances (aggregate histograms).
5. Instrumentation in the app: middleware recording `http_requests_total{route,method,status}` and `http_request_duration_seconds` histogram; DB pool metrics (in use, waiting), query duration; job queue depth and oldest age (M6.3); event loop lag (`perf_hooks.monitorEventLoopDelay`), memory, CPU, GC.
6. Prometheus model: pull-based scraping `/metrics` in text exposition format; scrape interval; targets; `up` metric; PromQL basics: instant vs range vectors, `rate`, `sum by`, `histogram_quantile`, `increase`.
7. Grafana: dashboards as code (JSON provisioning), RED panels per service/route; time ranges; annotations for deploys.
8. Saturation signals: event loop lag, pool wait time, queue depth, CPU throttling, memory growth, disk fill, connection counts (Postgres `max_connections`), file descriptors.
9. Alerting philosophy: alert on symptoms that need human action now (SLO burn rate, error rate, failing readiness, no data), not on causes (CPU 70%); every alert has a runbook link; page vs ticket vs dashboard-only; multi-window multi-burn-rate alerts (concept) for SLOs.
10. SLOs and error budgets: SLI (good events/valid events), SLO target over a window (99.5% over 28 days), error budget = 1 - SLO; burn rate; use budgets to decide release pace.
11. Tracing concepts: trace = tree of spans (name, start, duration, attributes, status); context propagation via `traceparent`; sampling (head vs tail); span attributes vs logs.
12. OpenTelemetry (OTel): vendor-neutral APIs/SDKs, auto-instrumentation (HTTP, `pg`/`postgres`), the OTLP protocol, the Collector (receive, process, export), exporters to Jaeger/Tempo/Honeycomb/Sentry.
13. Linking the signals: put `trace_id` into logs, exemplars on histograms, Sentry events with trace ids; one click from a slow request to its spans.
14. Reading a trace: critical path, sequential vs parallel spans, N+1 patterns visible as many identical DB spans, gaps (uninstrumented time), clock skew.
15. Black-box probes: synthetic monitoring (`blackbox_exporter`, UptimeRobot, Better Stack) checking `/ready` and a login-and-book flow from outside; catches DNS, TLS and network failures your app can't see.
16. Uptime math: 99.9% allows about 43 minutes down per 30 days; each added nine costs more.

### RESOURCES (in order)
1. Google SRE book chapters: "Monitoring Distributed Systems" (6), "Service Level Objectives" (4), and SRE workbook "Alerting on SLOs": https://sre.google/sre-book/monitoring-distributed-systems/ ; https://sre.google/workbook/alerting-on-slos/ .
2. Tom Wilkie, "The RED Method": https://grafana.com/blog/2018/08/02/the-red-method-how-to-instrument-your-services/ ; Brendan Gregg, "The USE Method": https://www.brendangregg.com/usemethod.html .
3. Prometheus docs: Overview, Data model, Metric types, Histograms and summaries best practices, Querying basics: https://prometheus.io/docs/introduction/overview/ ; https://prometheus.io/docs/practices/histograms/ ; https://prometheus.io/docs/prometheus/latest/querying/basics/ .
4. `prom-client` README (Node): https://github.com/siimon/prom-client .
5. Grafana docs, "Provisioning dashboards" and "Build your first dashboard": https://grafana.com/docs/grafana/latest/getting-started/build-first-dashboard/ .
6. OpenTelemetry docs, JS getting started and Collector overview: https://opentelemetry.io/docs/languages/js/getting-started/nodejs/ ; https://opentelemetry.io/docs/collector/ .
7. Grafana Tempo or Jaeger quickstart: https://grafana.com/docs/tempo/latest/getting-started/ ; https://www.jaegertracing.io/docs/latest/getting-started/ .
8. Rob Ewaschuk, "My Philosophy on Alerting": https://docs.google.com/document/d/199PqyG3UsyXlwieHaqbGiWVa8eMWi8zzAn0YfcApr8Q/edit .
9. Jaana Dogan (rakyll), "Observability" and Honeycomb's "Observability 101" for the difference between monitoring and observability: https://www.honeycomb.io/blog/ (search "observability 101").

### START HERE (first 30 minutes)
1. Add `prom-client` and a `/metrics` route (bound to an internal port or protected by a token). Hit the API, `curl /metrics`, read the text format.
2. Run Prometheus and Grafana via docker compose; confirm the target is `UP`.

### DRILLS
D1. HTTP middleware metrics: counter and histogram labeled by `method`, `route template`, `status_class` (2xx,4xx,5xx); buckets tuned to your SLO; a test asserting that label cardinality is bounded (dynamic ids never appear).
D2. Runtime/saturation metrics: event loop delay histogram, process CPU/memory, DB pool in-use/waiting, Postgres connection count (via an exporter or a query), job queue depth and oldest job age.
D3. RED dashboard as code: panels for rate (req/s), error ratio, p50/p95/p99 duration per route; saturation row; business metric row; deploy annotations. Commit the provisioning JSON/YAML.
D4. PromQL exercises: write queries for error ratio over 5m, p95 by route, requests per second by status class, top 3 slowest routes, burn rate over 1h; each with an expected result tested against synthetic load.
D5. Alerts: define three alerts with runbook links: (a) `/ready` failing for 2 minutes (page), (b) 5xx ratio > 2% over 10 minutes (page), (c) job queue oldest age > 15 minutes (ticket). Fire each by sabotage and verify delivery to Slack/email (Alertmanager or Grafana alerting).
D6. SLO doc: choose two SLIs (availability for confirm endpoint, p95 latency for list endpoint), set SLOs, compute the budget over 28 days, write the "what we do when the budget is exhausted" policy.
D7. Tracing: instrument API + DB + job worker with OTel auto-instrumentation; export via the Collector to Tempo or Jaeger; one trace showing `POST /bookings -> pg insert -> enqueue job -> worker -> email send` with propagated context.
D8. Find the N+1: add a deliberately N+1 endpoint; capture its trace; show the many identical spans; fix and show the shortened trace.
D9. Link signals: add `trace_id` to Pino lines; in Grafana, jump from a slow trace to its logs and from a log line to its trace.
D10. Black-box probe: synthetic check hitting `/ready` and a scripted login-and-book flow every minute from outside your infra; alert on failure.
D11. Latency lie demo: compare average vs p99 under a workload with 1% slow requests; chart both; explain what each alert would have said.

### BREAK
- Label a metric with `userId` and watch Prometheus memory and series count grow.
- Alert on CPU > 70% and see it fire without user impact; delete it.
- Use `rate()` on a gauge or `avg()` of p95 across instances and see wrong answers.
- Sample 100% of traces at high load and measure overhead; set sampling.

### QUIZ (cold)
1. RED vs USE vs golden signals.
2. Counter vs gauge vs histogram: example of each.
3. Why can't you average percentiles?
4. What is label cardinality and why dangerous?
5. What makes an alert good? Name two bad alerts.
6. SLI, SLO, error budget with numbers.
7. What is a span and what does context propagation do?
8. How does a trace reveal an N+1?
9. What do black-box probes catch that white-box metrics miss?
10. How much downtime does 99.9% allow per month?

### GATE
Spoken (5 minutes): given "the site feels slow", walk through the dashboards (RED, saturation), then a trace, then logs, to isolate the cause; name the three alerts you would page on and why.
Pass: quiz 9/10, D1 to D11, tag `gate/M9.3`.

### DELIVERABLE AND FAILURE MODES
Deliverable: metrics, dashboards-as-code, three alerts with runbooks, SLO doc, one trace linked to logs.
Faking it: a Grafana import of someone else's dashboard. Build it from your metrics.

---

## M9.4 Deploy like an adult (VPS once)

Hours: 14. Prerequisite: M9.3. Phase: 9.

### WHY
Managed platforms hide the server. Doing it once on a bare VPS teaches the OS, TLS, reverse proxy, firewall, process management and backups, which is what you debug when the platform is down or expensive.

### ATOMS
Server basics
1. Provision a VPS (Hetzner, DigitalOcean, Linode, Vultr), pick region near users, Linux (Debian/Ubuntu LTS), SSH keys only, a non-root sudo user.
2. SSH hardening: disable password auth and root login (`PasswordAuthentication no`, `PermitRootLogin no`), change nothing else superstitious; `ssh-agent`, `~/.ssh/config`; `fail2ban`; keep a recovery console path.
3. Firewall: `ufw` (or nftables) default deny incoming; allow 22 (or restricted), 80, 443; Postgres never public; Docker bypasses ufw by default (published ports skip UFW rules) so bind containers to `127.0.0.1` or manage `DOCKER-USER` chain.
4. Automatic security updates: `unattended-upgrades`; reboot policy; kernel updates; monitoring disk and memory.
5. Time sync (`systemd-timesyncd`/chrony), locale, swap sizing, ulimits, log rotation (`journald` limits, `logrotate`).
6. Users and permissions: least privilege service users, file modes, secrets files `0600`, no secrets in shell history.

Runtime and proxy
7. Process supervision: `systemd` units (ExecStart, Restart=always, User, Environment/EnvironmentFile, hardening: `NoNewPrivileges`, `ProtectSystem`, `PrivateTmp`), or Docker/Compose with `restart: unless-stopped`; know both; logs via `journalctl -u`.
8. Reverse proxy: Caddy (automatic HTTPS via Let's Encrypt, simple config) or nginx; responsibilities: TLS termination, HTTP/2/3, compression (gzip/zstd/br), static files with caching headers, proxying to the app on localhost, request size/timeouts, security headers, access logs, rate limiting basics, `X-Forwarded-*` headers (and telling your app how many proxies to trust, M6.1).
9. TLS in practice: DNS A/AAAA records, ACME HTTP-01/TLS-ALPN-01/DNS-01 challenges, certificate renewal, OCSP stapling by Caddy, HSTS rollout (short max-age first, then long, preload only deliberately), TLS config via Mozilla SSL Config Generator.
10. DNS: A, AAAA, CNAME, MX, TXT (SPF/DKIM/DMARC), TTLs and propagation, apex CNAME limitations (ALIAS/ANAME), registrar vs DNS host; `dig` literacy; lowering TTL before migrations.
11. CDN in front (Cloudflare): benefits (caching static assets, DDoS absorption, TLS to edge), risks (cache rules on dynamic content, real client IP via `CF-Connecting-IP`, origin lockdown to CDN ranges); set cache rules for hashed static assets only (M2.4).
12. Deployment mechanism: build image/artifact in CI, push to a registry (GHCR), pull on the server, run migrations (expand step), start new containers, health check, switch traffic, keep the previous version for rollback; options: `docker compose pull && up -d`, a small deploy script with `ssh`, Kamal (Rails-style Docker deploy), systemd with symlinked releases.
13. Zero-downtime: two instances behind Caddy with health checks, rolling restart, graceful shutdown (M1.3); blue-green; database compatibility across versions (expand/contract, M4.4).
14. Postgres on the box: install or Docker with a volume, `postgresql.conf` basics (`shared_buffers`, `max_connections`, `work_mem`), `pg_hba.conf` for local-only, roles from M5.6, backups (below), monitoring via `postgres_exporter`.
15. Backups and restore: `pg_dump` for logical, `pg_basebackup` + WAL archiving (pgBackRest/WAL-G) for PITR; offsite storage (S3/B2) encrypted; schedule; retention; restore drills (an untested backup is not a backup); RPO/RTO definitions.
16. Secrets on the server: env file with `0600`, or `systemd-creds`/SOPS/age; not in images; rotation runbook (M5.6).
17. Observability on the box: node_exporter (CPU, memory, disk, network), cAdvisor/docker metrics, disk alerts, `journalctl`, `htop`, `ss -tulpn`, `df -h`, `free -m`, `iostat`; shipping logs/metrics from M9.1 and M9.3.
18. Cost and capacity: how many requests per second a 2 vCPU box handles (measured in M9.8), monthly cost, backup cost, bandwidth.
19. Infrastructure as code (concept and one taste): cloud-init or Ansible playbook for the base setup so the box is reproducible; Terraform exists (know where it fits); at least keep a written, tested runbook.
20. Docker hygiene: non-root user, read-only filesystem where possible, resource limits (`--memory`, `--cpus`), pinned image tags/digests, vulnerability scan (`trivy`), pruning disk.
21. Managed vs self-managed trade-off: when a VPS is worth it, when Fly/Railway/RDS are right; pricing, time cost, risk (you are the on-call).

### RESOURCES (in order)
1. DigitalOcean tutorials: "Initial Server Setup with Ubuntu" and "How To Set Up a Firewall with UFW": https://www.digitalocean.com/community/tutorials/initial-server-setup-with-ubuntu ; https://www.digitalocean.com/community/tutorials/how-to-set-up-a-firewall-with-ufw-on-ubuntu . Or Hetzner Community tutorials: https://community.hetzner.com/tutorials .
2. Caddy docs: Getting started, `reverse_proxy`, Automatic HTTPS, Caddyfile concepts: https://caddyserver.com/docs/ .
3. Docker docs: "Docker and iptables" (ufw bypass) and Compose: https://docs.docker.com/engine/network/packet-filtering-firewalls/ ; https://docs.docker.com/compose/ .
4. `systemd.service` man page and the Arch/DigitalOcean "systemd essentials": https://www.freedesktop.org/software/systemd/man/latest/systemd.service.html ; https://www.digitalocean.com/community/tutorials/systemd-essentials-working-with-services-units-and-the-journal .
5. Mozilla SSL Configuration Generator and Observatory: https://ssl-config.mozilla.org/ ; https://developer.mozilla.org/en-US/observatory .
6. Let's Encrypt docs, "How It Works" and challenge types: https://letsencrypt.org/how-it-works/ ; https://letsencrypt.org/docs/challenge-types/ .
7. PostgreSQL docs, Backup and Restore (chapter 25): https://www.postgresql.org/docs/current/backup.html . pgBackRest user guide: https://pgbackrest.org/user-guide.html .
8. Cloudflare docs: DNS and Cache (Cache Rules, default cacheable extensions): https://developers.cloudflare.com/cache/ .
9. Julia Evans, "How DNS works" zines/posts and `dig` usage: https://wizardzines.com/zines/dns/ (optional).
10. Kamal docs (optional deploy tool): https://kamal-deploy.org/ .
11. Brendan Gregg, "Linux Performance" tool map for the box: https://www.brendangregg.com/linuxperf.html .
12. Ansible getting started (one taste): https://docs.ansible.com/ansible/latest/getting_started/index.html .

### START HERE (first 30 minutes)
1. Create a VPS and your SSH key. Log in as root once, create a sudo user, copy your key, then log in as that user.
2. Write your own checklist in NOTES for what you will configure, in order.

### DRILLS
D1. Base hardening, in order: update packages; non-root user; SSH key-only; disable root/password login; ufw (22, 80, 443); fail2ban; unattended-upgrades; timezone/time sync. Verify with an external `nmap` scan that only expected ports are open and `ssh -o PasswordAuthentication=yes` fails.
D2. Domain and DNS: buy or use a domain, set A/AAAA, `www` CNAME, and check with `dig`; set TTL to 300 before the first deploy.
D3. Caddy: reverse proxy to the API on `127.0.0.1:3000` with automatic HTTPS; static files for the web app with `Cache-Control: public, max-age=31536000, immutable` for hashed assets and `no-cache` for `index.html`; compression; security headers (M5.2); real client IP handling and trusted proxy config in the app.
D4. App runtime: Docker Compose (app, worker, Caddy; Postgres either here or on the host) bound to localhost where needed; environment via env file `0600`; `restart: unless-stopped`; resource limits; logs to journald or Loki. Alternatively systemd units. Do both once if time allows; keep one.
D5. Database: Postgres with a volume, local-only access, app roles from M5.6 and `pg_hba.conf` restricting hosts; migrations applied through the migrator role; `pg_stat_statements` enabled.
D6. Deploy script: CI builds an image tagged with the git SHA and pushes to GHCR; the server pulls and rolls out with health checks; a `deploy.sh` or Kamal config; migration step before switching; automatic rollback on a failed health check.
D7. Zero-downtime verification: run a continuous request loop during a deploy (two app instances, rolling restart); count failed requests; target zero.
D8. Backups: nightly `pg_dump` (and WAL archiving if you can) to offsite object storage, encrypted with `age`; retention policy; alert if the last backup is older than 26 hours.
D9. Restore drill: restore the backup into a fresh VM or container; run the M4.4 verification script; measure RTO (time) and RPO (data loss window). Write them down.
D10. Observability on the box: node_exporter + postgres_exporter + the app metrics into Prometheus; disk, memory and CPU alerts; synthetic check from M9.3 pointing at the new domain.
D11. CDN (optional but valuable): put Cloudflare in front; configure cache rules for static assets only; verify `cf-cache-status` headers; lock origin to Cloudflare IP ranges or authenticated origin pulls; fix real IP logging.
D12. Security verification: external scan with SSL Labs (A+ target after HSTS), Mozilla Observatory, `nmap`, `trivy image`, and checking that Postgres is unreachable from the internet.
D13. Reproducibility: write a cloud-init or Ansible playbook that recreates the base setup; destroy and recreate the VPS from scratch following the runbook within 60 minutes (`docs/runbook-vps.md`).
D14. Migrate traffic: lower TTL, deploy to the VPS, switch DNS from the managed platform, watch both, roll back by DNS once as a drill.

### BREAK
- Publish a Docker port without binding to localhost and see it exposed despite `ufw`.
- Let the disk fill with logs and observe the failure; add rotation and alerts.
- Expire a certificate deliberately in a test (staging ACME) and handle renewal failure.
- Run a restore from a corrupted dump and learn how you'd detect it.
- Lock yourself out with a bad `sshd_config` using a second session safely, then recover through the provider console.

### QUIZ (cold)
1. What are the first five things you do on a new server?
2. Why does Docker bypass `ufw` and how do you prevent it?
3. What does a reverse proxy do for you?
4. How does ACME HTTP-01 work?
5. How do you deploy with zero downtime and what must the database allow?
6. RPO vs RTO; what does your backup give you?
7. Why is an untested backup not a backup?
8. What's HSTS and why roll it out gradually?
9. Where do secrets live on this server?
10. When is a VPS the wrong choice?

### GATE
Spoken (6 minutes): draw the request path from DNS to Postgres (DNS, CDN, Caddy/TLS, app, DB), the deploy path from `git push` to running containers, and the recovery path from a dead server.
Pass: quiz 9/10, D1 to D14 (D11 optional), restore drill timings recorded, tag `gate/M9.4`.

### DELIVERABLE AND FAILURE MODES
Deliverable: slotlock live on your domain on a hardened VPS with automated deploy and tested backups, `docs/runbook-vps.md`.
Faking it: following a tutorial verbatim without the restore drill and the destroy-and-recreate test.

---

## M9.5 Cause an incident, write the postmortem

Hours: 10. Prerequisite: M9.4. Phase: 9.

### WHY
Production reliability is mostly practiced response, not prevention. You will break your own system on purpose, respond as on-call, and write a blameless postmortem. Interviewers love real incident stories.

### ATOMS
1. Incident lifecycle: detect, triage, mitigate (stop the bleeding), diagnose, resolve, review. Mitigate before you understand (rollback, feature flag off, scale up, failover) then root-cause.
2. Roles (even solo): incident commander (decides), operator (types), communicator (updates), scribe (timeline). Solo: write a timeline as you go.
3. Severity levels (SEV1 to SEV3) tied to user impact; comms cadence; status page (Upptime, Instatus, a static page on separate infrastructure).
4. Common failure classes and signatures: bad deploy (error spike right after release), bad migration (lock held, long ALTER, app/schema mismatch), DB connection pool exhaustion (requests hang, `too many clients`), slow query/missing index (p95 up, CPU on DB), disk full (writes fail, DB crashes), memory leak/OOM kill (restarts, `Killed` in dmesg), certificate expiry, DNS misconfiguration, dependency outage (email/OAuth provider), thundering herd after restart, cache stampede, runaway job retries (queue explosion), cron overlap, clock skew, noisy neighbor or CPU throttling, DDoS/abusive client, leaked credential.
5. Diagnosis method: form hypotheses from symptoms; check recent changes first (deploys, config, migrations, traffic); use dashboards (RED/USE), logs, traces, `pg_stat_activity`, `pg_locks`, `top`/`htop`, `iostat`, `ss`, `dmesg`, `journalctl`; bisect releases; compare with a known-good time.
6. Mitigation toolbox: rollback image, disable feature flag, increase pool or limits temporarily, kill runaway query (`pg_cancel_backend`), restart safely, rate limit an abuser, put up a maintenance page, restore from backup (last resort), failover.
7. Blameless postmortem: focuses on systems and decisions, not people; sections: summary, impact (users, duration, data), timeline (UTC), root cause(s) (use "5 whys" carefully; there is rarely one cause), detection (how and how long), resolution, what went well, what went poorly, where we got lucky, action items (owner, due date, priority, type: prevent, detect, mitigate), lessons.
8. Action item quality: specific, owned, testable ("add alert X with threshold Y"), not vague ("be more careful"); track to completion.
9. Runbooks: a short doc per alert/failure: symptoms, dashboards, commands, mitigation, escalation; tested during game days.
10. Game days/chaos engineering: planned failure injection in a safe environment with a hypothesis, blast radius limits and abort conditions; start with staging.
11. Error budgets and incidents: incidents consume budget; policy for freezing releases (M9.3).
12. Communication: what to tell users; honest, short, time-stamped; avoid speculation; post an after-action summary.
13. Data incidents: breach/leak response basics (contain, rotate, assess scope, notify as legally required); keep out of scope except tabletop (M9.9).
14. Prevention vs resilience: timeouts, retries with backoff, circuit breakers, bulkheads, load shedding, graceful degradation (serve stale), backpressure, idempotency, health checks, limits on everything.
15. Learning culture: review near misses; keep an incident log; trend analysis (top recurring causes).

### RESOURCES (in order)
1. Google SRE book: "Managing Incidents" (14), "Postmortem Culture: Learning from Failure" (15), "Emergency Response" (13): https://sre.google/sre-book/managing-incidents/ ; https://sre.google/sre-book/postmortem-culture/ ; https://sre.google/sre-book/emergency-response/ .
2. PagerDuty Incident Response documentation (open source): https://response.pagerduty.com/ . Read "Before an incident", "During an incident" and the postmortem template.
3. Atlassian, "Incident postmortems" and template: https://www.atlassian.com/incident-management/postmortem . Etsy's "Debriefing Facilitation Guide": https://extfiles.etsy.com/DebriefingFacilitationGuide.pdf .
4. Public postmortems to read (read at least six, each with your own 5-line summary): the Dan Luu list https://github.com/danluu/post-mortems ; pick GitHub, Cloudflare, GitLab database incident (2017), Stripe, Slack, Heroku/Fly outages.
5. PostgreSQL docs: `pg_stat_activity`, `pg_locks`, "Explicit Locking": https://www.postgresql.org/docs/current/monitoring-stats.html ; https://www.postgresql.org/docs/current/explicit-locking.html . Postgres wiki "Lock Monitoring": https://wiki.postgresql.org/wiki/Lock_Monitoring .
6. AWS Builders' Library: "Using load shedding to avoid overload", "Avoiding fallback in distributed systems", "Timeouts, retries, and backoff with jitter": https://aws.amazon.com/builders-library/ .
7. Michael Nygard, *Release It!* (stability patterns: timeouts, circuit breakers, bulkheads): https://pragprog.com/titles/mnee2/release-it-second-edition/ . Read the summary of stability patterns.
8. Brendan Gregg, "Linux Performance Analysis in 60,000 Milliseconds": https://netflixtechblog.com/linux-performance-analysis-in-60-000-milliseconds-accc10403c55 .
9. Gremlin's chaos engineering primer: https://www.gremlin.com/chaos-engineering/ (concepts).

### START HERE (first 30 minutes)
1. Read one public postmortem (GitLab 2017) and write: what failed, what detected it, what made it worse, the action items.
2. Create `docs/incidents/` with the postmortem template (copy PagerDuty's or Atlassian's).

### DRILLS
D1. Runbooks first: write one-page runbooks for the three alerts from M9.3 and the five scenarios below (before causing them).
D2. Incident 1 (bad migration): in staging (and later prod if comfortable), run a migration that adds an index without `CONCURRENTLY` or alters a column type while a load test runs; observe lock waits (`pg_locks`, `pg_stat_activity`), the app timing out, and use `pg_cancel_backend`/rollback to mitigate. Record the timeline.
D3. Incident 2 (pool exhaustion): hold connections with a leaky handler (forget to release) or a long `pg_sleep` transaction; watch requests hang; diagnose using pool metrics and `pg_stat_activity`; mitigate; fix with timeouts (`statement_timeout`, `idle_in_transaction_session_timeout`) and limits.
D4. Incident 3 (disk full): fill the disk with logs or temp files in staging; observe Postgres failing; recover; add disk alert and log rotation.
D5. Incident 4 (bad deploy): ship a release that returns 500 on one route; detect via alert; time to detect, time to mitigate by rollback; compare with and without readiness checks.
D6. Incident 5 (dependency outage): make the email/OAuth provider return errors or time out; observe retries piling up; add timeouts, circuit breaker, and graceful degradation (queue and retry emails; show a friendly login error); verify.
D7. Choose the best two incidents and run them as proper game days: written hypothesis, abort condition, observers, a time-boxed response, and a debrief; one of them in production with a safe blast radius (e.g., off-peak, flagged).
D8. Postmortems: write two full blameless postmortems from your own incidents (one with data from logs, metrics, traces) with action items; implement at least three action items (alerts, limits, tests, runbook edits) and link the commits.
D9. Status page: a static status page hosted separately (GitHub Pages or Upptime) showing check results of your deployed app.
D10. Read six public postmortems and write what each teaches about detection, mitigation and prevention; extract five recurring patterns.
D11. Resilience patterns: implement timeouts on all outbound calls, retries with backoff and jitter on safe operations, a basic circuit breaker around the email provider, load shedding (return 503 with `Retry-After` when the event loop lag exceeds a threshold), and test each under fault injection.
D12. Interview story: write the STAR story (situation, task, action, result) for one incident in under 2 minutes.

### BREAK
- Hide a log message critical to diagnosis and attempt the incident without it, noting the observability gap.
- Run the same incident after fixing it to prove the fix (regression game day).
- Practice mitigation with a time limit: roll back within 5 minutes using only your runbook.

### QUIZ (cold)
1. Mitigate vs diagnose: why mitigate first?
2. What are the sections of a postmortem?
3. What makes a good action item?
4. Signature of pool exhaustion vs a slow query vs a lock wait?
5. What does blameless mean in practice?
6. List five resilience patterns and what each prevents.
7. How do you find a blocking query in Postgres?
8. What is a game day and what are its safeguards?
9. How do you decide severity?
10. What did you learn from one real public postmortem?

### GATE
Spoken (5 minutes): tell one of your incidents as a story: detection, mitigation, diagnosis, resolution, root causes, action items; answer two hostile follow-ups.
Pass: quiz 9/10, D1 to D12, two postmortems published in `docs/incidents/`, tag `gate/M9.5`.

### DELIVERABLE AND FAILURE MODES
Deliverable: runbooks, two postmortems, implemented resilience patterns, status page.
Faking it: describing incidents you didn't cause. Evidence: timelines with real timestamps and screenshots.

---

## M9.6 Tests that are not theater, plus CI

Hours: 14. Prerequisite: M9.5. Phase: 9.

### WHY
Tests exist to let you change code safely. Coverage numbers and mocks-of-everything produce confidence without safety. You will build a small suite that actually catches the bugs you caused, run in CI on every push.

### ATOMS
1. Purpose: prevent regressions, document behavior, enable refactoring, speed up debugging; cost: time, flakiness, coupling to implementation.
2. Test types: unit (pure logic, fast), integration (real DB, real HTTP through the app, multiple modules), contract (API schema vs implementation, consumer-driven), end-to-end (real browser, whole system), property-based (generated inputs, invariants), load (M9.8), security tests (M9.9), manual exploratory.
3. Shapes: pyramid (many unit), trophy (mostly integration), honeycomb; for a CRUD+DB app prefer integration tests through the HTTP layer with a real Postgres; unit-test pure domain logic (state machine, pricing, timezones, cursors).
4. What to test: behavior that matters and invariants: overlap prevention, idempotency, authz matrix, pagination correctness, money/time math, state transitions, error contract; edge cases (empty, one, many, boundaries, DST, unicode, huge input); concurrency.
5. What not to test: framework internals, trivial getters, implementation details (private function calls, internal state), snapshots of large outputs nobody reads.
6. Test doubles: dummy, stub, fake, spy, mock; prefer fakes and real dependencies you control (Postgres in Docker/Testcontainers) over mocks of your own code; mock only at system boundaries you don't own (third-party APIs via MSW or a local fake server, time, randomness).
7. Test data: builders/factories (`aBooking({status:'confirmed'})`), isolating tests via transactions rolled back, unique tenants per test, or schema-per-worker; deterministic seeds; no shared mutable fixtures.
8. DB isolation strategies: truncate between tests (slow), transaction-per-test with rollback (fast; careful with code that opens its own transactions/pools), template database cloning (`CREATE DATABASE ... TEMPLATE`), per-test tenant.
9. Time and randomness: inject a clock; use fake timers in unit tests; avoid `Date.now()` calls in domain logic; seed RNGs.
10. Flaky tests: causes (shared state, order dependence, time, concurrency, network, async races, resource leaks); policy: quarantine and fix within a week; never "retry until green" as a strategy; detect with repeated runs.
11. Test runner: Bun test/Vitest; watch mode; parallelism; coverage tools (c8/istanbul); `--bail` in CI; test naming and structure (arrange, act, assert); one behavior per test; helpful failure messages.
12. Coverage: a tool for finding untested code, not a goal; line coverage ≠ branch ≠ behavior; mutation testing (Stryker) measures whether tests would catch changes; do a one-time mutation run on the domain module.
13. Property-based testing with `fast-check`: define invariants (cursor traversal visits each row once; booking ranges never overlap after any sequence of create/cancel; `parse(serialize(x)) = x`); shrinking finds minimal counterexamples.
14. Concurrency tests: run N parallel requests (idempotency, overlap, double confirm) and assert invariants; use `Promise.all` and barriers for determinism.
15. E2E with Playwright: critical user journeys only (sign up, login, create booking, conflict, cancel, logout-all); use role/label locators; auto-waiting; trace on first retry; parallel workers with isolated data; visual checks sparingly; a11y checks (`@axe-core/playwright`); network mocking only for third-party edges.
16. Contract tests: OpenAPI (M6.4) validated against responses in integration tests; consumer expectations for the web client (types shared or generated).
17. Testing authorization and security: matrix tests (M5.4), CSRF tests (M5.3), headers tests, rate limit tests.
18. CI: GitHub Actions (or equivalent): triggers (`push`, `pull_request`), jobs (install, typecheck, lint, test, build, e2e), service containers for Postgres, dependency caching, matrix builds if needed, artifacts (traces, coverage), required checks and branch protection, secrets handling (fork PRs don't get secrets), concurrency groups (cancel superseded runs), timeouts, `bun install --frozen-lockfile`.
19. CI speed: parallelize jobs, shard tests, cache, run fast checks first (typecheck/lint) and e2e last; keep the main branch pipeline under 10 minutes.
20. Pre-commit hooks: lint-staged for format/lint on changed files, gitleaks; keep hooks fast.
21. Linting and formatting: ESLint (typescript-eslint, react-hooks, jsx-a11y) or Biome; Prettier/Biome format; rules that catch bugs (floating promises, no misused promises, exhaustive deps).
22. Release gates: required status checks; deployment only from green main; migration checks in CI (run migrations from scratch and from the previous release schema).
23. Test review: when reading a test, ask what bug would make it fail; delete tests that can't fail; delete tests that fail for non-bugs.
24. Debugging test failures: run a single test, `--inspect`, logs on failure, reproduce with the same seed, bisect with `git bisect run`.

### RESOURCES (in order)
1. Kent C. Dodds, "Write tests. Not too many. Mostly integration." and "The Testing Trophy": https://kentcdodds.com/blog/write-tests ; https://kentcdodds.com/blog/the-testing-trophy-and-testing-classifications .
2. Martin Fowler, "Mocks Aren't Stubs" and "TestPyramid": https://martinfowler.com/articles/mocksArentStubs.html ; https://martinfowler.com/bliki/TestPyramid.html .
3. Google Testing Blog, "Test Sizes" and "Flaky Tests at Google and How We Mitigate Them": https://testing.googleblog.com/2010/12/test-sizes.html ; https://testing.googleblog.com/2016/05/flaky-tests-at-google-and-how-we.html .
4. Vitest docs: https://vitest.dev/guide/ ; or Bun test: https://bun.sh/docs/cli/test . Testcontainers for Node: https://node.testcontainers.org/ .
5. `fast-check` docs, "Getting started" and "Properties and arbitraries": https://fast-check.dev/docs/introduction/ .
6. Playwright docs: Best Practices, Locators, Auto-waiting, Trace Viewer, CI: https://playwright.dev/docs/best-practices ; https://playwright.dev/docs/ci-intro .
7. GitHub Actions docs: workflow syntax, service containers, caching, concurrency, `GITHUB_TOKEN` permissions, security hardening: https://docs.github.com/en/actions/writing-workflows ; https://docs.github.com/en/actions/security-for-github-actions/security-guides/security-hardening-for-github-actions .
8. Stryker Mutator docs: https://stryker-mutator.io/docs/ .
9. Gerard Meszaros, *xUnit Test Patterns* (reference; read test smell summaries): http://xunitpatterns.com/ .
10. Hillel Wayne, "Property-Based Testing" blog posts (concepts): https://hillelwayne.com/ (search "property-based testing").
11. "Software Engineering at Google" chapters 11 to 14 (Testing Overview, Unit Testing, Test Doubles, Larger Testing): https://abseil.io/resources/swe-book/html/ch11.html .
12. typescript-eslint docs ("Typed linting"): https://typescript-eslint.io/getting-started/typed-linting .

### START HERE (first 30 minutes)
1. List the 10 worst bugs you hit in slotlock so far (from NOTES). Mark which test would have caught each.
2. Run the existing tests with a real Postgres container and measure runtime.

### DRILLS
D1. Test plan doc (`docs/testing.md`): shapes, what we test at each level, what we don't, data strategy, flakiness policy, CI gates.
D2. Integration harness: spin up Postgres (Testcontainers or compose), run migrations once, per-test isolation (transaction rollback or unique tenant), a helper to create users, workspaces, sessions and an `app.request()`-style client.
D3. Domain unit tests: state machine transitions (table-driven), cursor encode/decode/tamper, timezone conversion across DST, money math, idempotency key hashing.
D4. Integration tests for each rule: overlap constraint through the API, idempotent POST with 20 concurrency, authz matrix (actor x action x ownership), validation errors per contract, pagination correctness under concurrent inserts, rate limiting (fake clock), webhook signing and dedupe, job retry/idempotency.
D5. Property-based tests with `fast-check`: (a) random sequences of create/confirm/cancel never produce overlapping confirmed bookings; (b) cursor pagination across random data returns each row once; (c) `decode(encode(cursor))` roundtrip. Capture one shrunk counterexample from a deliberately injected bug.
D6. Mutation testing: run Stryker on the booking domain module; list surviving mutants and add tests for at least 5; record the score before/after.
D7. Flakiness hunt: run the suite 50 times in a loop (or `--repeat`); identify flaky tests and their causes; fix; record the pass rate.
D8. Playwright E2E for the three critical flows: sign up/login/logout-all; create-confirm-cancel booking with conflict handled by two browser contexts; invite a member and verify viewer restrictions. Traces on failure uploaded as CI artifacts; a11y scan on each page.
D9. CI pipeline: lint, `tsc --noEmit`, unit, integration (Postgres service), build, E2E (against a built app with a seeded DB), image build; caching; concurrency cancel; timeouts; required checks and branch protection on `main`. Fail on a deliberate bug in each stage to prove it gates.
D10. Pre-commit: lint-staged + gitleaks; measure hook time (under 5s).
D11. Migration CI: run all migrations from scratch; and apply the new migration on a DB seeded at the previous release's schema; run the verification script.
D12. Bug-first practice: take 5 bugs from your history (or re-inject), write the failing test first, fix, confirm green; commit test and fix in separate commits.
D13. Debugging drill: `git bisect run` to find a deliberately injected regression among 30 commits using your tests.
D14. Test smells review: take 10 existing tests and classify smells (mock-heavy, over-specified, unclear, slow, coupled to internals); rewrite 3.

### BREAK
- Replace the real DB with a mock repository and demonstrate a bug the mock hides (e.g., the exclusion constraint).
- Add `sleep(100)` to "wait" for async work and observe flakiness; fix with explicit awaits/polling with timeouts.
- Reduce coverage to 100% with trivial assertions and show it catches nothing; run Stryker.
- Give CI access to production secrets in a PR from a fork (understand the risk; then restrict).

### QUIZ (cold)
1. What is the testing trophy and why for this app?
2. Mock vs fake vs stub; when is mocking harmful?
3. How do you isolate DB state between tests?
4. Causes of flaky tests and your policy.
5. What does mutation testing measure that coverage doesn't?
6. What is a property-based test? Give one invariant from slotlock.
7. How do you test concurrency?
8. What belongs in E2E and what doesn't?
9. What does a good CI pipeline order look like and why?
10. How do you find the commit that broke something?

### GATE
Spoken (5 minutes): explain your test strategy to a skeptical lead who says "100% coverage or nothing": what you test, what bugs it caught, what it won't catch, and the CI gates.
Pass: quiz 9/10, D1 to D14, CI required on `main`, mutation score documented, tag `gate/M9.6`.

### DELIVERABLE AND FAILURE MODES
Deliverable: test suite, CI pipeline with branch protection, `docs/testing.md`, mutation and flake reports.
Faking it: tests that assert mocks were called. Each test must name the bug it would catch.

---

## M9.7 The performance loop on slotlock

Hours: 12. Prerequisite: M9.6. Phase: 9.

### WHY
Performance work is a loop, not a trick: measure, find the bottleneck, change one thing, measure again, record. You will run it across frontend, server and database on your own app.

### ATOMS
1. The loop: define the user-facing metric (LCP, INP, p95 API latency, throughput), establish a baseline under controlled conditions, profile to find the dominant cost (Amdahl: optimize what's largest), form a hypothesis, change one thing, re-measure, keep or revert, record.
2. Don't optimize without evidence; most speed comes from removing work (less data, fewer requests, fewer rows, less JS), then caching, then parallelizing, then micro-optimizing.
3. Budgets: JS size, LCP, INP, CLS, p95 latency per endpoint; Lighthouse CI with budgets fails PRs that regress.
4. Frontend profiling: Performance panel (main thread flame chart, long tasks, layout/style recalcs), Coverage panel (unused JS/CSS), Lighthouse treemap/bundle visualizers, React Profiler (commits, why re-rendered), Network waterfall, `web-vitals` in RUM, `PerformanceObserver` (long-animation-frame, event timing for INP).
5. Frontend levers (from M3): reduce JS (code splitting, lazy routes, drop dependencies, `import()`), image optimization (AVIF/WebP, `srcset`, dimensions, priority hints `fetchpriority`), font loading (`font-display`, preload, subsetting), critical CSS, preloading the LCP resource, avoid layout thrash, virtualization for long lists, memoization only where profiled, debouncing input work, `content-visibility`, avoiding client waterfalls (M7.2).
6. Backend profiling: CPU profiles (`--cpu-prof` in Node, `bun --inspect`/Chrome DevTools, `clinic.js` flame/doctor, `0x`), heap snapshots for leaks, event loop lag, async hooks/traces (OTel) for time-in-DB vs time-in-app, `autocannon` for load.
7. Typical backend wins: avoid N+1, batch queries, `SELECT` only needed columns, JSON serialization cost (large payloads, `JSON.stringify` blocking), compression, connection pooling sizes, keep-alive, caching hot reads (HTTP cache headers, in-process LRU with invalidation, `ETag`), moving work to jobs, pagination limits, avoiding synchronous CPU work in the event loop, `Promise.all` for independent I/O.
8. Database profiling: `pg_stat_statements` (top by total time, mean time, calls), `auto_explain`, `EXPLAIN (ANALYZE, BUFFERS)`, slow query log (`log_min_duration_statement`), index usage (`pg_stat_user_indexes`), bloat, cache hit ratio, lock waits; typical wins: missing/wrong index, covering index, partial index, rewriting a query, avoiding functions on indexed columns, limiting rows, denormalized read tables or materialized views (with refresh trade-offs).
9. Network: HTTP/2/3, compression (brotli for static; gzip/zstd dynamic), CDN caching of static assets, `preconnect` to origins, reducing round trips (combine API calls for a screen), `Cache-Control` and `ETag` on API reads where allowed.
10. Caching strategy hierarchy: don't compute (remove), compute once (memoize/cache), compute closer (CDN/edge), compute later (async); cache invalidation cost and correctness risk (M8.6).
11. Real user monitoring (RUM) vs lab: p75 field data by device/network, CrUX, `web-vitals` beacon to your API; lab for debugging, field for truth.
12. Statistical hygiene: repeat runs, report medians/percentiles, same hardware and load, warm vs cold, avoid noisy neighbors, don't compare across different conditions.
13. Regression prevention: CI performance checks (Lighthouse CI, bundle size limits like `size-limit`, a micro-benchmark with thresholds, k6 smoke thresholds), dashboards with p95 per route, release annotations.
14. Writing it up: before/after table with conditions, root cause, change, trade-off, and what you'd do next.

### RESOURCES (in order)
1. web.dev, Core Web Vitals optimization guides (LCP, INP, CLS) and "Performance budgets 101": https://web.dev/articles/optimize-lcp ; https://web.dev/articles/optimize-inp ; https://web.dev/articles/performance-budgets-101 .
2. Chrome DevTools docs, "Analyze runtime performance" and "Performance features reference": https://developer.chrome.com/docs/devtools/performance .
3. Lighthouse CI docs: https://github.com/GoogleChrome/lighthouse-ci ; `size-limit`: https://github.com/ai/size-limit .
4. Node.js docs, "Profiling" and "Easy profiling for Node.js applications"; Clinic.js docs: https://nodejs.org/en/learn/getting-started/profiling ; https://clinicjs.org/documentation/ . Bun profiling docs: https://bun.sh/docs/runtime/debugger (use Chrome DevTools profiler).
5. PostgreSQL docs, `pg_stat_statements`, `auto_explain`, "Using EXPLAIN": https://www.postgresql.org/docs/current/pgstatstatements.html ; https://www.postgresql.org/docs/current/auto-explain.html ; https://www.postgresql.org/docs/current/using-explain.html . depesz explain visualizer: https://explain.depesz.com/ ; pgMustard docs: https://www.pgmustard.com/docs/explain .
6. Brendan Gregg, "Flame Graphs": https://www.brendangregg.com/flamegraphs.html .
7. React docs, "Profiler API" and "Render and Commit": https://react.dev/reference/react/Profiler ; https://react.dev/learn/render-and-commit .
8. web.dev, "Optimize Long Tasks", "Browser-level image lazy loading", "Preload critical assets", `fetchpriority`: https://web.dev/articles/optimize-long-tasks ; https://web.dev/articles/fetch-priority .
9. Gil Tene, "How NOT to Measure Latency" (talk; also relevant in M9.8): https://www.youtube.com/watch?v=lJ8ydIuPFeU .
10. Amdahl's law (Wikipedia): https://en.wikipedia.org/wiki/Amdahl%27s_law (for the optimization targeting principle).

### START HERE (first 30 minutes)
1. Pick the three user-visible metrics you will improve: LCP on the bookings page (mobile), p95 of `GET /bookings`, and one slow SQL query.
2. Record baselines using your M3.4 protocol and `autocannon` against the deployed app and `pg_stat_statements`.

### DRILLS
D1. Baselines: Lighthouse mobile median-of-10 for the list and detail pages, a RUM snippet capturing LCP/INP/CLS to `/rum` stored in Postgres, `autocannon` p50/p95/p99 for three endpoints at fixed concurrency, `pg_stat_statements` top 10 by total time. All in `docs/perf-baseline.md`.
D2. Frontend loop x3: for three separate findings (e.g., large bundle, unoptimized hero image, long task on filter change), do hypothesis, change, re-measure, keep/revert. Table with metric deltas; at least one change must be a revert.
D3. Backend loop x3: profile with `--cpu-prof` and a flame graph; find one CPU hotspot (JSON serialization, regex, sync crypto), one I/O waste (N+1 or serial awaits), one payload issue (over-fetching); fix; re-measure p95 and throughput.
D4. DB loop x3: using `pg_stat_statements` + `EXPLAIN (ANALYZE, BUFFERS)`: add a composite index for the list endpoint, rewrite a correlated subquery to a join/window function, add a partial index for `status='confirmed'`; show plan and latency before/after; check write overhead and index size.
D5. HTTP caching win: add `ETag`/`Cache-Control: private, max-age=0, must-revalidate` or short `max-age` to cacheable GETs (resources list); show 304 hits and bytes saved; verify no authorization leaks (`Vary: Cookie` if shared caches could apply).
D6. Cache with invalidation: add a small in-process LRU for hot reads (resource list per workspace) with explicit invalidation on mutation and a TTL fallback; test correctness (stale read never after write by the same user); measure gains; record the added complexity cost.
D7. Frontend budget enforcement: `size-limit` in CI for the main bundle; Lighthouse CI assertions for LCP/CLS/INP-proxies (TBT) on the list page; make a PR that violates the budget and watch CI fail.
D8. RUM: produce a p75 LCP/INP by page and device class from your RUM table after generating realistic traffic with Playwright under throttling; compare to lab.
D9. Perf regression test: a k6 or autocannon smoke test in CI against a seeded DB with thresholds (p95 < X); inject a slow query; watch it fail.
D10. Write-up: `docs/performance-case-study.md` with the three before/after tables, flame graph screenshots, query plans, and trade-offs. 1,500 words.
D11. Memory: find a deliberate leak (an unbounded array/cache) via heap snapshots over time; fix; show flat RSS in Grafana.
D12. Interview prep: explain "the page is slow" as a 5-minute checklist covering network, server, database and browser with commands for each.

### BREAK
- Optimize something unmeasured and show no change in the user metric.
- Add an index that slows writes and measure the throughput drop.
- Use `memo` everywhere and measure worse render time due to comparison overhead.
- Compare results taken under different conditions and see a false improvement.

### QUIZ (cold)
1. State the performance loop.
2. Where do you look first for a slow page vs slow API vs slow query?
3. Lab vs field data: when each?
4. What is a flame graph and what does width mean?
5. How do you find the top costly SQL queries?
6. Name five frontend, five backend, five DB levers.
7. When can caching hurt?
8. How do you prevent regressions after fixing?
9. What is Amdahl's law in this context?
10. What stats hygiene rules do you follow?

### GATE
Spoken (6 minutes): present one full loop (baseline, profile, hypothesis, change, result, trade-off) with numbers and screenshots; then answer "the page is slow, what do you do?" as a methodical checklist.
Pass: quiz 9/10, D1 to D12, budgets enforced in CI, case study published, tag `gate/M9.7`.

### DELIVERABLE AND FAILURE MODES
Deliverable: baseline, nine documented loops (3 per layer), budgets in CI, case study.
Faking it: "I optimized X" with no baseline. Numbers with conditions or it doesn't count.

---

## M9.8 Load testing

Hours: 12. Prerequisite: M9.7. Phase: 9.

### WHY
Load tests find the knee of the curve where latency explodes, the first bottleneck (pool, CPU, locks), and failure behavior. Most load tests lie because of the load model; you will learn to measure honestly.

### ATOMS
1. Goals: capacity (how much before SLO breaks), bottleneck discovery, regression detection, resilience (behavior past capacity), soak (leaks over hours), spike (sudden surge), breakpoint (ramp until failure).
2. Load models: closed (fixed number of virtual users, each waits for a response; self-throttling, hides overload) vs open (arrivals at a fixed rate regardless of responses; models real traffic and exposes queueing); use open models (`constant-arrival-rate` in k6) for capacity.
3. Coordinated omission: when the load generator waits for slow responses before sending the next request, it under-samples slow periods and reports falsely good latency; fix with open-model generators or corrected histograms (HdrHistogram, wrk2).
4. Percentiles: report p50/p90/p99/p99.9/max; plot latency distribution; don't average; sample sizes needed for tail percentiles.
5. Little's law: `L = λW` (concurrency = arrival rate × latency); estimate the required connection pool and instances; utilization vs latency (queueing theory: latency explodes as utilization → 100%; keep headroom at 60 to 70%).
6. Test environment: production-like (same DB size and indexes, same instance size, realistic data distribution and skew), isolated from other load, the generator on a separate machine so it doesn't steal CPU, same region/network; never load test production without safeguards and warning; avoid third-party services (mock them) so you don't hammer others.
7. Realistic workloads: mix of endpoints by ratio (70% reads, 20% list, 8% create, 2% cancel), think time, data variety (many users/tenants, hot and cold keys), auth flows (sessions reused), payload sizes; seeded data at scale (1M bookings, 5k workspaces).
8. Tools: k6 (JS scripts, thresholds, scenarios, good open model support), `autocannon` (quick HTTP), `wrk2` (constant throughput, corrected latency), Gatling, Locust, Vegeta, `hey`; use k6 as the main tool.
9. k6 concepts: `options` with `scenarios` (`constant-arrival-rate`, `ramping-arrival-rate`, `ramping-vus`), `thresholds` (pass/fail criteria), checks, groups, tags, `setup()` seeding, custom metrics, output to Prometheus/Grafana (`--out experimental-prometheus-rw`) or JSON/CSV.
10. Test types in practice: smoke (1 VU sanity), load (expected peak), stress (beyond peak), spike, soak (hours), breakpoint.
11. Reading results: throughput vs latency curve; error rates by status (429 from your limiter vs 5xx); saturation indicators on the server during the test (CPU, event loop lag, DB connections, lock waits, pool waiting, disk I/O, network), correlation with dashboards (M9.3).
12. Bottleneck hunting: change one variable at a time; typical order: DB pool size, DB CPU/IO or missing index, single-threaded app CPU (run multiple processes/workers), lock contention (hot rows like counters or a single resource's bookings), N+1, serialization, logging overhead, TLS/proxy, file descriptor limits, ephemeral port exhaustion, kernel TCP backlog.
13. Hot-spot contention: many users booking the same resource slot; the exclusion constraint + `SERIALIZABLE` retries; measure throughput under contention and error rates (409 vs 500); lock queue behavior.
14. Scaling choices: vertical (bigger box), horizontal app instances (stateless app; shared session in DB; sticky sessions not needed), DB read replicas (replication lag effects, read-your-writes), connection pooling with PgBouncer (transaction mode caveats: prepared statements, `SET LOCAL`), caching, queueing/backpressure.
15. Backpressure and load shedding: bounded queues, timeouts, 429/503 with `Retry-After`, circuit breakers; verify the system degrades gracefully rather than collapsing (retries amplifying load: retry storms).
16. Capacity model: from measured saturation point compute capacity per instance, headroom, cost per 1,000 requests, and expected traffic growth; document "what breaks first" at 2x, 5x, 10x.
17. Soak test: run for 4 to 8 hours at moderate load; watch memory, connection leaks, queue age, disk growth, log volume, p99 drift.
18. Failure injection during load: kill an instance, restart DB, add latency (`tc netem`, Toxiproxy); watch recovery and error budget burn.
19. Reporting: test plan, environment, workload model, results (charts), bottleneck analysis, changes made, new capacity, risks, recommendations.

### RESOURCES (in order)
1. k6 docs: "Running k6", Scenarios and executors, Thresholds, Metrics, Test types: https://grafana.com/docs/k6/latest/ ; https://grafana.com/docs/k6/latest/using-k6/scenarios/executors/ ; https://grafana.com/docs/k6/latest/testing-guides/test-types/ .
2. Gil Tene, "How NOT to Measure Latency": https://www.youtube.com/watch?v=lJ8ydIuPFeU ; HdrHistogram: https://hdrhistogram.github.io/HdrHistogram/ ; `wrk2`: https://github.com/giltene/wrk2 .
3. Brendan Gregg, "Systems Performance" (Chapter 2 methodologies and USE) and the web summary: https://www.brendangregg.com/usemethod.html .
4. Little's Law and queueing intuition: "Queueing theory for software engineers" posts, e.g., Marc Brooker, "Telling Stories About Little's Law": https://brooker.co.za/blog/2018/06/20/littles-law.html and "Open and closed models" (Schroeder et al., paper): https://www.usenix.org/legacy/event/nsdi06/tech/full_papers/schroeder/schroeder.pdf .
5. Marc Brooker's blog on timeouts/retries/overload: https://brooker.co.za/blog/ ; AWS Builders' Library, "Using load shedding to avoid overload": https://aws.amazon.com/builders-library/using-load-shedding-to-avoid-overload/ .
6. PgBouncer docs (pool modes, limitations): https://www.pgbouncer.org/features.html ; postgres.js docs on pooling and prepared statements: https://github.com/porsager/postgres .
7. Toxiproxy README for fault injection: https://github.com/Shopify/toxiproxy ; Linux `tc netem` tutorial: https://wiki.linuxfoundation.org/networking/netem .
8. "Software Engineering at Google" ch. 14 (Larger Testing) and Google SRE workbook "Managing Load": https://sre.google/workbook/managing-load/ .
9. `autocannon` README: https://github.com/mcollina/autocannon .
10. Node.js docs, `cluster`/worker threads and Bun `reusePort` for multi-process scaling: https://nodejs.org/api/cluster.html .

### START HERE (first 30 minutes)
1. Install k6. Write a 20-line smoke test hitting `GET /ready` and one authenticated list endpoint with a session cookie from `setup()`.
2. Run it at 1 VU and read the summary: http_req_duration percentiles, error rate.

### DRILLS
D1. Test environment: a staging stack with production-sized data (seed 1M bookings, 5k workspaces with Zipf distribution so some tenants are huge); document specs and `postgresql.conf`; generator on a separate machine.
D2. Workload model doc: endpoint mix, think times, auth/session reuse, data variety, with rationale from your access logs or assumptions.
D3. Smoke and load tests in k6 using open models (`ramping-arrival-rate` to find the knee, then `constant-arrival-rate` at 70% of the knee for 30 minutes); thresholds on p95 and error rate; Prometheus remote-write into Grafana alongside server metrics.
D4. Show coordinated omission: run the same endpoint with a closed model (VUs) and an open model during an injected 5s stall (SIGSTOP the app for 5s via `kill -STOP/-CONT`); compare p99 and max between the two; chart the lie.
D5. Breakpoint test: ramp until p95 exceeds your SLO or errors exceed 1%; record RPS at the knee and the first saturated resource; identify the bottleneck from dashboards; fix one bottleneck; re-run; repeat for three iterations; table of knee RPS per iteration with the change made.
D6. Pool sizing experiment: vary `max` connections (5, 10, 20, 50) with a fixed load; plot throughput and latency; explain the optimum with Little's law and DB cores (also test PgBouncer in transaction mode with `SET LOCAL` and prepared statements pitfalls).
D7. Contention test: 200 concurrent users trying to book the same slot and then 200 different slots; measure success rates (one winner), p99, 409 vs 5xx; verify no double-booking in the DB; tune retry logic for serialization failures with jitter.
D8. Single-process ceiling: run the app as multiple processes/instances behind Caddy; show scaling from 1 to 2 to 4 instances; identify when the DB becomes the limit.
D9. Overload behavior: at 150% of capacity, measure error modes without protection (timeouts, retries amplify), then with protection (limits, queue caps, load shedding, `Retry-After`); show goodput (successful requests per second) staying stable.
D10. Soak: 4 to 8 hours at 50% capacity; memory/FD/connection/queue age/disk charts; write what drifted.
D11. Chaos during load: kill the DB or an instance during a steady test (staging); measure error burst, recovery time, and error budget burn; add Toxiproxy latency (500ms) on DB connections and observe timeouts/pool behavior.
D12. Capacity report: `docs/capacity.md`: single instance knee, headroom policy (70%), cost per 1k requests, "what breaks first at 2x/5x/10x", and the scaling plan (what you'd do and in what order).
D13. CI smoke: a short k6 test with thresholds in CI (against a seeded DB) to catch large regressions (M9.7).
D14. Review a "bad" public load test result (find an article with p50-only results or a closed-model hype benchmark) and list five flaws.

### BREAK
- Run the generator on the same machine as the server and see skewed results.
- Use an empty DB and be proud of fake numbers; then use the real size.
- Ignore errors and look only at average latency.
- Set client retries without backoff and trigger a retry storm in an overload test.

### QUIZ (cold)
1. Open vs closed workload models; which to use for capacity?
2. What is coordinated omission and how do you avoid it?
3. State Little's law and use it for pool sizing.
4. Why does latency explode near 100% utilization?
5. What do p99.9 and max tell you that p95 doesn't?
6. Describe the first five bottlenecks you'd check.
7. What is a soak test for?
8. How do you test overload behavior safely?
9. Why must load test data resemble production?
10. What belongs in a capacity report?

### GATE
Spoken (6 minutes): present the knee, the bottleneck you found, what you changed, the new knee, the overload behavior, and the capacity statement ("one 2 vCPU instance handles X RPS at p95 < Y").
Pass: quiz 9/10, D1 to D14, capacity doc published, tag `gate/M9.8`.

### DELIVERABLE AND FAILURE MODES
Deliverable: k6 scripts, workload model, results charts, capacity report, CI smoke test.
Faking it: a single `autocannon` run on localhost with an empty database.

---

## M9.9 Security pass: OWASP API Top 10

Hours: 12. Prerequisite: M9.8. Phase: 9.

### WHY
You studied attacks per topic. Now audit the whole system against a standard list, as an attacker and as a defender, and write the report a security-aware company expects.

### ATOMS
1. Threat modeling: assets (accounts, bookings, attachments, secrets), actors (anonymous, user, workspace admin, other tenant, malicious insider, compromised dependency), trust boundaries (browser, CDN, proxy, app, DB, third-party APIs), entry points; STRIDE (Spoofing, Tampering, Repudiation, Information disclosure, DoS, Elevation of privilege); data-flow diagrams; abuse cases; risk = likelihood × impact.
2. OWASP API Security Top 10 (2023) mapped to slotlock: API1 BOLA, API2 Broken Authentication, API3 Broken Object Property Level Authorization, API4 Unrestricted Resource Consumption, API5 BFLA, API6 Unrestricted Access to Sensitive Business Flows, API7 SSRF, API8 Security Misconfiguration, API9 Improper Inventory Management, API10 Unsafe Consumption of APIs.
3. OWASP Top 10 (web) and ASVS (Application Security Verification Standard) as a checklist; level 1 as the target; ASVS chapters for sessions, access control, validation, cryptography, error handling, logging, files, API.
4. Business-flow abuse: booking scalping, mass cancelling, enumerating invite emails, spamming confirmation emails (email bombing), coupon abuse; defenses: per-flow rate limits, quotas, CAPTCHAs, anomaly detection, friction at high-risk steps.
5. Security misconfiguration checks: default credentials, verbose errors, open admin routes, debug endpoints (`/debug/boom`), permissive CORS, missing security headers, directory listing, exposed `.git`/`.env`, open ports, outdated TLS, public buckets, default Postgres settings, Docker socket exposure, overly broad IAM/cloud roles.
6. Inventory: list every route, host, version, environment, third-party integration; deprecate and remove old versions; docs match reality (M6.4 OpenAPI).
7. Unsafe consumption: trusting data from third-party APIs/webhooks without validation; validate responses, limits, timeouts, TLS, signatures (M6.3).
8. Dependency and supply-chain security: `npm audit`/`bun audit`, OSV-Scanner, Dependabot/Renovate, lockfile review, pin GitHub Actions by SHA, minimal permissions for `GITHUB_TOKEN`, npm provenance, avoid install scripts risk (`--ignore-scripts` where feasible), SBOM (CycloneDX), review new dependencies (maintainers, download counts, size), typosquatting.
9. Static and dynamic scanning: Semgrep (SAST with community rules), CodeQL, `eslint-plugin-security`, gitleaks/trufflehog (secrets), `trivy` (images and IaC), OWASP ZAP baseline/API scan (DAST), Nuclei templates, `nmap`; triage false positives; do not run active scans against systems you don't own.
10. Manual testing: Burp Suite Community or ZAP proxy; replay requests; tamper IDs, roles, types (string vs number vs array vs object: type juggling), negative numbers, huge values, Unicode normalization collisions, HTTP verb tampering, parameter pollution, content-type confusion, JSON key duplication, mass assignment, race conditions (limit overrun via parallel requests), path traversal, host header injection (password reset link poisoning), open redirects (`next=`), cache poisoning/deception, request smuggling (concept; proxy-dependent), prototype pollution (JS), ReDoS, SSRF, XXE (XML if ever used), insecure deserialization.
11. Specific JS/Node issues: prototype pollution in object merges, `eval`/`Function`, `child_process` with user input (command injection), path traversal with `path.join`, regex DoS, `JSON.parse` of huge input, unsafe `Object.assign` with user objects, timing-safe comparisons, `crypto.randomInt` vs `Math.random`, unhandled promise rejections crashing, SSR template injection.
12. Host header and URL building: never build absolute URLs (reset links, redirects) from the incoming `Host`; use configured `APP_ORIGIN`.
13. Open redirects: validate `next` params to same-origin relative paths; allowlist.
14. Cryptography hygiene: use platform primitives (`crypto`, `libsodium`), AEAD (AES-GCM/ChaCha20-Poly1305), unique nonces, no custom crypto, key management (M5.6), password hashing (M5.1), random from CSPRNG, constant-time compare, TLS 1.2+ only.
15. Privacy and compliance basics: data inventory, minimization, retention, deletion/export endpoints, consent for cookies/analytics, subprocessors, breach notification timelines (GDPR 72 hours, concept), SOC 2/ISO 27001 vocabulary (what they are and when customers ask).
16. Security headers and cookie review (M5.2/M2.2); CORS review (M2.3); CSP report review.
17. Incident readiness for security: key rotation runbooks, session revocation, forced password reset, audit log review, contact and disclosure policy (`/.well-known/security.txt`).
18. Vulnerability management: severity (CVSS) vs exploitability and exposure; patch SLAs; triage; coordinated disclosure; bug bounty (concept).
19. Report writing: executive summary, scope, methodology, findings with ID, severity, description, reproduction steps, impact, remediation, status, evidence; retest results.

### RESOURCES (in order)
1. OWASP API Security Top 10 2023, all ten in full: https://owasp.org/API-Security/editions/2023/en/0x00-header/ .
2. OWASP Application Security Verification Standard (ASVS) 4.0.3 or 5.0: https://owasp.org/www-project-application-security-verification-standard/ . Use Level 1 as a checklist; github repo with CSV: https://github.com/OWASP/ASVS .
3. OWASP Cheat Sheet Series index (reread sheets for areas where you find gaps): https://cheatsheetseries.owasp.org/IndexTopTen.html ; Threat Modeling CS: https://cheatsheetseries.owasp.org/cheatsheets/Threat_Modeling_Cheat_Sheet.html ; Nodejs Security CS: https://cheatsheetseries.owasp.org/cheatsheets/Nodejs_Security_Cheat_Sheet.html .
4. Adam Shostack, *Threat Modeling: Designing for Security* (chapters 1 to 4) or the Microsoft "Threat Modeling Security Fundamentals" learning path: https://learn.microsoft.com/en-us/training/paths/tm-threat-modeling-fundamentals/ .
5. PortSwigger Web Security Academy, free labs: API testing, SSRF, race conditions, host header attacks, open redirect, prototype pollution (server-side and client-side), web cache poisoning, request smuggling (concepts): https://portswigger.net/web-security/all-topics . Do at least 15 labs total in this module.
6. OWASP ZAP docs, baseline scan and API scan: https://www.zaproxy.org/docs/docker/baseline-scan/ ; https://www.zaproxy.org/docs/docker/api-scan/ .
7. Semgrep docs, quickstart and rule registry: https://semgrep.dev/docs/getting-started/ ; OSV-Scanner: https://google.github.io/osv-scanner/ ; Trivy docs: https://trivy.dev/latest/docs/ ; gitleaks.
8. GitHub docs, "Security hardening for GitHub Actions" and "Using third-party actions" (pinning by SHA): https://docs.github.com/en/actions/security-for-github-actions/security-guides/security-hardening-for-github-actions .
9. Node.js security best practices: https://nodejs.org/en/learn/getting-started/security-best-practices .
10. security.txt (RFC 9116): https://www.rfc-editor.org/rfc/rfc9116 ; securitytxt.org.
11. CVSS v3.1/v4 user guide: https://www.first.org/cvss/ ; EPSS (exploit prediction): https://www.first.org/epss/ .
12. Burp Suite Community docs: https://portswigger.net/burp/documentation/desktop ; or ZAP desktop.
13. Dafydd Stuttard, *The Web Application Hacker's Handbook* (reference, optional) and "Real-World Bug Hunting" for attack imagination.
14. Ethical rules: only test systems you own or have written permission to test; HackerOne disclosure guidelines: https://www.hackerone.com/disclosure-guidelines .

### START HERE (first 30 minutes)
1. Draw the data-flow diagram of slotlock: user browser, CDN, Caddy, app, worker, Postgres, MinIO/S3, email provider, OAuth provider, webhook receivers. Mark trust boundaries.
2. List 10 assets/abuse cases ("attacker books all slots for tomorrow", "tenant reads another tenant's attachments").

### DRILLS
D1. Threat model doc `docs/threat-model.md`: DFD, STRIDE table per component/flow, top 15 risks with likelihood x impact, existing controls and gaps.
D2. API Top 10 audit: for each of the ten categories, write the test cases you ran, evidence (curl/Burp/ZAP outputs), result (pass/fail), and fix (commit). At least three real findings in your own app expected.
D3. ASVS L1 checklist: go through every L1 requirement relevant to an API+SPA; mark pass/fail/NA with evidence or justification; link tests.
D4. Automated scanning in CI: Semgrep, gitleaks, `bun audit`/OSV, Trivy on the image, and ZAP API scan against staging using the OpenAPI doc; gate on high severity; triage and suppress false positives with justification comments.
D5. Manual attack session (4 hours): using Burp/ZAP, attempt: IDOR on every ID, role tampering, mass assignment, type juggling (send arrays/objects/negative numbers/null), parameter pollution, HTTP method override, large payloads, deep JSON, race conditions on single-use tokens and limit counters (parallel requests with a barrier), host header injection on password reset, open redirect on `next`, CORS misconfiguration probes, cache deception probes, file upload attacks (M6.3), SSRF via webhook URLs.
D6. Business-flow abuse: script a bot that books and cancels repeatedly, sends many invites, and triggers many confirmation emails; add per-flow quotas, cooldowns and rate limits; measure and verify.
D7. Prototype pollution lab: reproduce a pollution bug in an object merge utility used with user JSON (`__proto__`, `constructor.prototype`); fix with schema validation (`strict()` Zod), `Object.create(null)`, safe merges; add a regression test.
D8. Command/path injection lab: a deliberately vulnerable image-conversion helper using `child_process.exec` with a user filename and `path.join` with user input; exploit; fix with `execFile`/argument arrays, allowlists, `path.resolve` containment checks.
D9. ReDoS lab: craft a catastrophic regex input against a naive validator; fix with a safe regex/time limits/`re2`.
D10. Race condition lab: redeem a single-use invite token or coupon concurrently with 50 parallel requests; show double use; fix with an atomic `UPDATE ... WHERE used_at IS NULL RETURNING`.
D11. Dependency review: generate an SBOM (CycloneDX); list direct dependencies with purpose and a keep/replace/remove decision; remove or replace at least three; pin Actions by SHA; set `GITHUB_TOKEN` to least privilege; enable Dependabot or Renovate with grouped updates.
D12. Add `/.well-known/security.txt` (contact, expiry, policy), a vulnerability disclosure policy page, and a patch SLA doc.
D13. Pen-test report: `docs/security-assessment.md` with executive summary, scope, method, findings (ID, severity via CVSS, repro, impact, fix, retest), residual risks. Fix all high/critical findings and retest.
D14. PortSwigger: complete 15 labs across the listed topics; table of labs and lessons.
D15. Tabletop: a leaked database credential scenario; walk through detection, containment (rotate, revoke sessions), eradication, recovery, notification; time-box 60 minutes; write the checklist.

### BREAK
- Leave `/debug/boom` open in staging and find it with a scanner.
- Build a reset link from the `Host` header and poison it.
- Run ZAP against a system you don't own (don't); instead recognize why consent matters and write the rule.
- Disable a dependency audit gate "temporarily" and see how long it stays disabled (put a dated expiry on every suppression).

### QUIZ (cold)
1. Name the OWASP API Top 10 (2023) and a slotlock example for each.
2. What is STRIDE?
3. Why is business-flow abuse a security issue even without a vulnerability?
4. How does host header injection poison password resets?
5. What is prototype pollution and how do you defend?
6. How do you test for race-condition limit overruns?
7. SAST vs DAST vs dependency scanning vs secrets scanning.
8. CVSS severity vs exploitability: how do you prioritize?
9. What belongs in security.txt and a disclosure policy?
10. What are your rules for ethical testing?

### GATE
Spoken (6 minutes): present your threat model and the three most important findings from your audit: how you found them, what the impact was, how you fixed and retested.
Pass: quiz 9/10, D1 to D15, assessment doc published, scans in CI, tag `gate/M9.9`.

### DELIVERABLE AND FAILURE MODES
Deliverable: threat model, ASVS L1 checklist, API Top 10 audit, scanning pipeline, assessment report, disclosure files.
Faking it: running a scanner and pasting its output. Manual attack evidence and fixes are mandatory.

---

## M9.10 Delete and simplify

Hours: 8. Prerequisite: M9.9. Phase: 9.

### WHY
Seniority is subtraction. Code you don't have has no bugs. You will remove features, dependencies, abstractions and config you don't need, and write down what you would not build again.

### ATOMS
1. Costs of code: maintenance, bugs, cognitive load, build time, attack surface, onboarding time; every line is a liability.
2. Simplicity vs ease: simple is few interleaved concerns (Rich Hickey, "Simple Made Easy"); easy is familiar; complexity comes from coupling and state.
3. Essential vs accidental complexity (Brooks); domain complexity (booking rules) is essential; framework ceremony and over-abstraction are accidental.
4. Deletion candidates: unused routes/features (check logs and metrics), unused dependencies (`knip`, `depcheck`, `ts-prune`), dead code (`ts-unused-exports`, coverage + traces), duplicate utilities, stale feature flags, commented code, config options nobody changes, abstractions with one implementation, wrapper layers that only forward calls, generic "frameworks" built before the second use case, premature microservices/modules, over-granular files, unnecessary caches, outdated migrations (squash only with care).
5. Rule of three: abstract after the third repetition; duplication is cheaper than the wrong abstraction (Sandi Metz).
6. Deep modules (Ousterhout): simple interfaces hiding significant functionality; shallow modules add interface cost without hiding complexity; refactor toward depth.
7. Dependency policy: prefer standard library/platform features; each dependency needs a reason, owner, upgrade path and exit plan; measure size and transitive count; remove "convenience" packages (left-pad class).
8. Data deletion: unused tables and columns (expand/contract: stop writes, stop reads, drop), old indexes (`pg_stat_user_indexes` with zero scans over a long window), old jobs and logs retention, old uploaded files.
9. Feature flags lifecycle: add with an owner and a removal date; remove after full rollout; stale flags are branching complexity.
10. Measuring the effect: LOC (a poor proxy but informative), number of dependencies, build time, bundle size, test time, number of config options, number of concepts a newcomer must learn; incident count after deletion.
11. How to delete safely: tests green, deploy behind a flag or small PR, monitor, ability to revert; commit message explains why; keep deletes in separate commits from refactors from features.
12. Refactoring techniques: small steps under tests, extract function/inline function, rename, move, replace conditional with polymorphism/table, introduce parameter object, separate query from command, make illegal states unrepresentable (discriminated unions), functional core/imperative shell, strangler pattern for larger replacements.
13. Architecture decision records (ADRs): context, decision, consequences; superseded status; keep them short; document deletions as decisions.
14. Documenting what you wouldn't build again: a "regrets" doc useful for interviews and for the next project.
15. Resisting second-system syndrome and rewrites: incremental improvement beats rewrite unless tests, budget and evidence justify it.

### RESOURCES (in order)
1. Rich Hickey, "Simple Made Easy" (talk): https://www.infoq.com/presentations/Simple-Made-Easy/ .
2. John Ousterhout, *A Philosophy of Software Design* (chapters 1 to 10; talk summary at Google: https://www.youtube.com/watch?v=bmSAYlu0NcY ).
3. Sandi Metz, "The Wrong Abstraction": https://sandimetz.com/blog/2016/1/20/the-wrong-abstraction .
4. Fred Brooks, "No Silver Bullet" (essential vs accidental complexity): https://worrydream.com/refs/Brooks-NoSilverBullet.pdf .
5. Martin Fowler, *Refactoring* (2nd ed.) catalog: https://refactoring.com/catalog/ . Read the catalog index and ten refactorings in detail.
6. `knip` docs (unused files, exports, dependencies): https://knip.dev/ . `depcheck`: https://github.com/depcheck/depcheck .
7. Dan Abramov, "Goodbye, Clean Code": https://overreacted.io/goodbye-clean-code/ . Kent C. Dodds, "AHA Programming": https://kentcdodds.com/blog/aha-programming .
8. Gary Bernhardt, "Boundaries" (functional core, imperative shell): https://www.destroyallsoftware.com/talks/boundaries .
9. Michael Nygard ADR article (reread) and ADR tools: https://adr.github.io/ .
10. Casey Muratori and Jonathan Blow on over-abstraction are controversial; read critically; optional: "Semantic Compression": https://caseymuratori.com/blog_0015 .
11. Fred Hebert, "Complexity Has to Live Somewhere": https://ferd.ca/complexity-has-to-live-somewhere.html .

### START HERE (first 30 minutes)
1. Run `knip` on the repo and read the output. Count unused files, exports and dependencies.
2. Check access logs and metrics: which routes had zero traffic in 30 days?

### DRILLS
D1. Inventory: table of every route, feature, dependency, config option, table/column, index, job type and flag with last-used evidence (logs, metrics, `pg_stat_user_indexes`, coverage) and a keep/simplify/delete decision.
D2. Delete at least 10 items from the inventory in separate small commits with tests green; each commit message states why and the evidence.
D3. Dependency diet: remove or replace at least 5 dependencies with platform features or small local code (e.g., date helpers, lodash functions, HTTP client wrappers); record bundle size, install size and build time before/after.
D4. Abstraction audit: find three abstractions with one implementation or pass-through wrappers; inline or deepen them; show the diff and what got simpler.
D5. Refactoring kata under tests: pick the three ugliest functions (long, many branches); apply small refactorings (extract, table-driven, discriminated unions); ensure behavior unchanged via existing and new characterization tests; measure cyclomatic complexity before/after (`eslint complexity` or `lizard`).
D6. Make illegal states unrepresentable: refactor booking and form state with discriminated unions; delete defensive checks that the types now make impossible; keep runtime validation at boundaries.
D7. Data cleanup: drop unused columns/indexes with expand/contract migrations and verification; record write throughput and storage changes.
D8. Flag cleanup: list all flags with owner and removal date; remove stale flags; add a lint/test that fails if a flag lacks an expiry.
D9. Before/after metrics: LOC, dependencies (direct/transitive), build time, test time, bundle KB, docs pages, concepts to learn; one table in `docs/simplification.md`.
D10. ADRs: write three ADRs for deletions and one "things I would not build again" doc with honest regrets and what you'd do instead.
D11. Review exercise: read an open-source PR that removes code; write what makes a good deletion PR.

### BREAK
- Delete a "dead" function that's used via reflection or dynamic import and find the gap in tests; add coverage.
- Inline an abstraction that was actually protecting you from a repeated bug and learn the lesson; revert if needed.
- Do a deletion without monitoring and miss the production impact.

### QUIZ (cold)
1. Essential vs accidental complexity, with slotlock examples.
2. Why is the wrong abstraction worse than duplication?
3. What is a deep module?
4. How do you prove something is unused?
5. How do you delete a database column safely?
6. What belongs in a dependency policy?
7. What is a feature flag lifecycle?
8. Name five refactorings and when each applies.
9. When is a rewrite justified?
10. How do you measure that simplification helped?

### GATE
Spoken (4 minutes): present your before/after table and two deletions you were nervous about, how you verified safety, and what you learned.
Pass: quiz 9/10, D1 to D11, tag `gate/M9.10`.

### DELIVERABLE AND FAILURE MODES
Deliverable: inventory, deletions, dependency diet, simplification metrics, ADRs, regrets doc.
Faking it: deleting only comments and unused imports. Real deletions touch behavior, dependencies, and data.

---

## M9.11 Technical post and mock interviews

Hours: 16. Prerequisite: M9.10. Phase: 9.

### WHY
Hiring is a communication problem. A public technical post proves you can explain, and mock interviews turn knowledge into performance under pressure.

### ATOMS
Writing
1. Audience and thesis: one reader (a junior who is you six months ago), one claim, one takeaway; title that promises something concrete ("What happens if the job runs twice: idempotent jobs in Postgres").
2. Structure: hook (problem and stakes), minimal context, the investigation or build (with evidence), the pitfalls, the result with numbers, what you'd do differently, links to code.
3. Evidence over opinion: graphs, benchmarks with conditions, code snippets trimmed to essentials, reproducible repo.
4. Style: short sentences, concrete nouns, active verbs, no hype, no unexplained jargon, headings that carry the argument, code blocks with filenames, alt text for images.
5. Editing passes: structure, claims (cut unsupported), clarity (read aloud), line edit (cut 20%), technical review by a peer, proofreading.
6. Publishing: personal blog (static site with RSS), dev.to/Hashnode cross-post with canonical URL, Hacker News/Reddit only if the content earns it, share in communities, respond to feedback, publish corrections.
7. Topic options (pick one with real data): idempotency keys in Postgres with crash recovery; keyset pagination under concurrent writes with a benchmark; what a server function really sends over HTTP; building a query cache in 150 lines; CORS from the browser's point of view (34 atoms condensed); a real incident postmortem; load testing lies (coordinated omission demo); exclusion constraints for booking.
8. Portfolio integration: link the post from the README and your profile; publish the repository for reproducibility.

Interviews
9. Interview types for web roles: recruiter screen, coding (algorithms/data structures; M10.2), practical coding (build a small feature or fix a bug in a repo; frontend tasks like building a component or fetching data), take-home, system design (M10.1), behavioral (STAR), code review exercise, domain deep-dives (HTTP, browser, SQL, auth), team/culture.
10. Preparation system: a question bank (Part E), spoken answers recorded and reviewed, timed practice, feedback from peers or paid mock services (Pramp, interviewing.io), collaborative editors; keep a mistake log.
11. Spoken answer structure: restate, clarify, outline, answer, check; give one concrete example from your projects; admit unknowns and reason aloud; "I'd measure X" > guessing.
12. Behavioral story bank: 8 stories mapped to themes (conflict, failure, leadership, ambiguity, deadline, learning, impact, disagreement); STAR; quantify results; avoid blame.
13. Practical coding in interviews: clarify requirements, write tests/examples first, small steps, run code often, handle edge cases, talk through trade-offs, keep the code simple; know your runtime/standard library cold (`Map`, `Set`, array methods, string APIs, `Intl`, `fetch`, `Promise` combinators, `URL`).
14. Frontend-specific prompts: debounce/throttle, event delegation, promise utilities (`Promise.all` implementation, retry with backoff, timeouts), LRU cache, deep clone/equality, `useFetch`/`useDebounce`/`useLocalStorage` hooks, autocomplete, infinite scroll, modal with focus trap, star rating, todo with persistence, virtualized list, data table with sorting/filtering, form with validation, accessibility questions.
15. Backend-specific prompts: design a rate limiter, idempotent payment endpoint, pagination, authentication/session design, job queue, webhook delivery, URL shortener, booking system with no double-booking, SQL queries (window functions, joins, top-N), debugging a slow query, handling a failing deploy.
16. Negotiation and process: ask about team, on-call, testing practice, deployment cadence, code review culture; levels and compensation research (levels.fyi), competing offers, never lie, clarify equity/benefits, take time to decide.

### RESOURCES (in order)
1. Julia Evans, her posts on writing and blogging (search her blog for "blogging"): https://jvns.ca/blog/good-questions/ . Read at least three of her posts for style.
2. Brandur Leach's articles as models of technical writing: https://brandur.org/articles . Read "Idempotency keys" and one more for structure.
3. Google Technical Writing One and Two (free courses): https://developers.google.com/tech-writing/one ; https://developers.google.com/tech-writing/two . Do One fully.
4. Orwell, "Politics and the English Language" (rules for clarity): https://www.orwellfoundation.com/the-orwell-foundation/orwell/essays-and-other-works/politics-and-the-english-language/ .
5. Joel Spolsky, "The Guerrilla Guide to Interviewing" (what interviewers look for): https://www.joelonsoftware.com/2006/10/25/the-guerrilla-guide-to-interviewing-version-30/ .
6. Gayle Laakmann McDowell, *Cracking the Coding Interview* (the behavioral and process chapters): https://www.crackingthecodinginterview.com/ .
7. The Front End Interview Handbook: https://www.frontendinterviewhandbook.com/ ; GreatFrontEnd (practice prompts): https://www.greatfrontend.com/ ; Tech Interview Handbook (behavioral and resume): https://www.techinterviewhandbook.org/ .
8. Pramp: https://www.pramp.com/ ; interviewing.io: https://interviewing.io/ ; mock partners from community Discords or meetup groups.
9. `exercism.org` JavaScript/TypeScript track for fluent practice: https://exercism.org/tracks/typescript .
10. levels.fyi for compensation research and ladders: https://www.levels.fyi/ ; Patrick McKenzie, "Salary Negotiation: Make More Money, Be More Valued": https://www.kalzumeus.com/2012/01/23/salary-negotiation/ .
11. Static site options: Astro blog starter (https://docs.astro.build/en/tutorial/0-introduction/), Eleventy (https://www.11ty.dev/), or Hugo.

### START HERE (first 30 minutes)
1. List the 5 most interesting things you learned with measurements. Pick the one with a graph.
2. Write the title and a one-sentence thesis. Write the first paragraph as a hook with the problem.

### DRILLS
D1. Post outline with thesis, sections, evidence list (graphs, code, numbers), and the repo link.
D2. Draft 1 (2,000 to 3,000 words), edit 1 (structure and claims), edit 2 (read aloud and cut 20%), peer review by at least two people (one technical, one non-technical), final edit.
D3. Publish on your own domain (static site with RSS) and cross-post with canonical link; add to README and profiles; share in two relevant communities; log feedback and answer comments; publish at least one correction or improvement based on feedback.
D4. Reproducibility check: someone else clones the repo and reproduces your key result in under 15 minutes (or you do on a fresh machine).
D5. Question bank sessions (Part E interview bank): do 10 timed spoken answers on HTTP/CORS/cookies/caching, 10 on SQL/transactions, 10 on auth/security, 10 on React/rendering, 10 on production/system design; record and grade each against a rubric (correct, complete, concise, concrete example).
D6. Six mock interviews: 2 practical coding (frontend + backend), 2 system design (M10.1 once available; or lightweight design now), 1 behavioral, 1 code review/debugging. Use peers or platforms; record or take notes; write what to improve; re-do the weakest type.
D7. Behavioral story bank: 8 STAR stories in `docs/stories.md` drawn from your real projects (incident, conflict-free disagreement, failure, deleting code, learning fast, shipping under constraint); each under 2 minutes spoken and quantified.
D8. Résumé and links: one page; projects first with outcomes ("handles 800 RPS at p95 120ms; zero double bookings under 200 concurrent requests"); GitHub pinned repos with strong READMEs; a personal site with the post, slotlock case study, and contact.
D9. Practical coding drills: implement under 30 minutes each (no AI): debounce, throttle, `Promise.all`, retry with backoff, LRU, deep equal, event emitter, autocomplete with abort, infinite scroll hook, modal with focus trap; test each.
D10. Negotiation prep: research ranges for your target roles/locations; write your questions for employers (10) and your priorities; role-play a negotiation once.

### BREAK
- Do a mock interview after deliberately not preparing and note what fails first (stress response).
- Publish the post without peer review and see which mistakes readers find.
- Give a spoken answer with no example and compare to one with an example.

### QUIZ (cold)
1. What is your post's thesis in one sentence?
2. Name three sections every technical post needs.
3. How do you structure a spoken technical answer?
4. STAR: give one of your stories in 90 seconds.
5. How do you handle a question you don't know?
6. What do interviewers look for in practical coding?
7. List five frontend interview prompts and the core skill each tests.
8. List five backend prompts and the core skill each tests.
9. How do you prepare for a system design round?
10. What do you ask an employer?

### GATE
Spoken (10 minutes): present your published post's key idea and then take 3 live hostile technical questions from someone else on its content.
Pass: quiz 9/10, D1 to D10, post public, six mock interviews logged, tag `gate/M9.11`.

### DELIVERABLE AND FAILURE MODES
Deliverable: published technical post, repo, mock interview log, stories, résumé.
Faking it: AI-written post. The no-AI-first-draft rule applies; reviewers can tell.

---

## M9.12 The hireable artifact (Phase 9 exit)

Hours: 12. Prerequisite: M9.11. Phase: 9.

### WHY
A hiring manager gives you five minutes. The artifact must show correctness, operations and judgment fast: a live app, a clear README, evidence of rigor, and your voice.

### ATOMS
1. The artifact: a deployed `slotlock` with README, architecture doc, case studies, runbooks and tests; plus the blog post(s) and the talk.
2. README structure: one-line pitch, live demo link and demo credentials, screenshot/GIF, features, what's interesting (the hard problems solved: no double booking, idempotency, tenant isolation, jobs), architecture diagram, tech stack with reasons, run locally in under 5 commands, tests and how to run them, deployment, links to docs (API contract, threat model, capacity, incidents, ADRs), limitations and roadmap.
3. Architecture doc: context diagram, container diagram (C4 model), data model ERD, key flows (sequence diagrams), invariants and how enforced, failure modes, decisions (ADR index), scaling story.
4. Evidence pages: EXPLAIN plans, load test results and capacity, perf case study, postmortems, security assessment, test strategy and mutation score, SLOs and dashboards screenshots.
5. Demo script: a 3-minute guided tour (login, create booking, conflict, idempotent retry, watch dashboard/logs/trace) and a 10-minute technical deep-dive; recorded video (Loom/OBS) linked from README.
6. Seed/demo data and a reset command; a read-only demo account; no real personal data; cost control (rate limits, quotas, budget alerts).
7. Quality bar: no broken links; no TODOs in the main path; CI green badge; consistent code style; no secrets in history (`gitleaks --log-opts`); license; CONTRIBUTING and issue templates optional; dependency updates current.
8. Case study write-up: problem, constraints, decisions with trade-offs, results with numbers, what went wrong, what you'd change; 1,000 to 1,500 words.
9. Profile: GitHub profile README, pinned repos, LinkedIn headline with the claim ("Full-stack engineer: Postgres, HTTP, production"), portfolio site, résumé aligned with evidence.
10. Application strategy: target list of 50 companies (startups with small teams value this), referrals, cold emails referencing specific product details and a 3-line proof of competence, apply early (from M6.4), track in a CRM-like sheet; volume and follow-ups.
11. Freelance/OSS alternative paths: small contracts (first project via network), open-source contributions (good first issues in Hono, TanStack, Drizzle, Playwright docs/tests), maintainers' expectations.
12. Review with strangers: ask 3 engineers to review the repo for 10 minutes and tell you what they conclude; fix the gaps.
13. Maintenance mode: keep it deployed, monitored, patched; a stale demo is a negative signal.

### RESOURCES (in order)
1. C4 model: https://c4model.com/ ; Mermaid docs for diagrams in Markdown: https://mermaid.js.org/ ; Structurizr (optional): https://structurizr.com/ .
2. Make a README (guide): https://www.makeareadme.com/ ; "Awesome README" examples: https://github.com/matiassingers/awesome-readme . Study 10 READMEs of well-known projects (Hono, Drizzle, TanStack Query, Caddy, Litestream).
3. Tech Interview Handbook, resume and portfolio sections: https://www.techinterviewhandbook.org/resume/ .
4. Loom or OBS for recording: https://www.loom.com/ ; https://obsproject.com/ .
5. Open-source contribution guides: "How to Contribute to Open Source": https://opensource.guide/how-to-contribute/ ; `good first issue` finder: https://goodfirstissue.dev/ .
6. Hacker News monthly "Ask HN: Who is hiring?" threads for real job postings and what they ask for: https://news.ycombinator.com/ (search the title).
7. Wellfound (AngelList Talent) and Work at a Startup (YC) for startup roles: https://wellfound.com/ ; https://www.workatastartup.com/ .
8. Patrick McKenzie, "Don't Call Yourself A Programmer, And Other Career Advice": https://www.kalzumeus.com/2011/10/28/dont-call-yourself-a-programmer/ .

### START HERE (first 30 minutes)
1. Open your repo in a private window as a stranger. Spend 5 minutes only on the README and the live link. Write what you concluded about the author in three bullets.
2. Compare with the target conclusion: "careful, can ship, can operate, can explain".

### DRILLS
D1. README rewrite per the structure, with screenshots/GIF and an architecture diagram; live demo with a reset script and a read-only demo account.
D2. `docs/architecture.md` with C4 context/container diagrams, ERD, three sequence diagrams (login, confirm booking with idempotency, job + webhook delivery), invariants table (invariant, enforcement mechanism, test).
D3. Evidence index: a `docs/README.md` linking every artifact (API contract, EXPLAIN.md, perf case study, capacity, threat model, security assessment, incidents, ADRs, runbooks, testing); verify every link in CI with a link checker.
D4. Case study post (1,000 to 1,500 words) on your site: "Building slotlock: hard problems and what I measured".
D5. Demo video (3 minutes tour; optional 10-minute deep-dive) linked from README; rehearsed live demo with a fallback if the network fails.
D6. Quality gate: CI green; Lighthouse on landing/login pages; a11y pass; secret scan across history; dependency freshness; license; remove all TODO/FIXME in the main path; Sentry quiet for 7 days; uptime probe green for 14 days.
D7. Stranger review: three engineers each spend 10 minutes and answer: what is this, what is impressive, what worries you, what's missing; incorporate feedback in a changelog.
D8. Application kit: résumé (1 page), GitHub profile README, LinkedIn, portfolio site, 50-company target list with a personalized hook for the top 15, cold-email template (3 lines + proof), tracking sheet.
D9. Apply: send at least 15 tailored applications/outreach this week (continuing applications started after M6.4); follow-up schedule; log responses.
D10. OSS: make 2 contributions (docs fix, test, or small bug fix) to a project you used (Hono, TanStack, Playwright, Drizzle, Caddy, etc.), following their guidelines; link in profile.
D11. Self-audit: run the hireable artifact checklist below honestly.

### BREAK
- Ask a friend to find the first broken thing in your demo flow in 5 minutes; fix it and repeat once.
- Take the demo offline (kill the app) and see how your README and alerts behave; add a status badge.

### QUIZ (cold)
Spoken (the 10 minute deep-dive) replaces a written quiz; the questions: What are the three hardest problems in slotlock? How do you know it's correct? How would it scale 10x? What would you do differently? What was the worst incident? What did you delete? What's the security posture? How do you deploy and roll back? What broke in load testing? What would you teach a new hire first?

### PHASE 9 EXIT: THE HIREABLE ARTIFACT CHECKLIST (all must be yes)
Product and code
1. Live URL with HTTPS, demo credentials, seeded data, reset script.
2. Zero double bookings under 200 concurrent requests (test + load evidence).
3. Idempotent POST with crash recovery, proven by tests.
4. Tenant isolation proven by authz tests, RLS defense in depth.
5. Cursor pagination correct under concurrent inserts, with benchmark.
6. Jobs and webhooks idempotent; "job runs twice" test exists.
7. CI required on `main` (lint, types, unit, integration, E2E, security scans).
Operations
8. Structured logs with request ids, Sentry with source maps, RED dashboard, three actionable alerts with runbooks, one trace.
9. Hardened VPS deploy, tested backup restore with RTO/RPO, rollback drill done.
10. Two postmortems from induced incidents, with implemented action items.
11. Load test with capacity statement and overload behavior.
12. Security assessment and threat model with fixes and retests.
Communication
13. README, architecture doc, evidence index, ADRs.
14. Published technical post and the 10-minute Phase 8 talk.
15. Six mock interviews logged; 8 behavioral stories; résumé and profile aligned.
16. 15+ applications sent; 2 OSS contributions.

### GATE
Spoken: the 10-minute deep-dive, recorded, plus the checklist signed with evidence links.
Pass: all 16 checklist items yes; stranger-review issues closed; tags `gate/M9.12` and `gate/phase9`.

### DELIVERABLE AND FAILURE MODES
Deliverable: the repository, site, video, post, résumé.
Faking it: a checklist item without a link. Evidence or no.

---

# PHASE 10. SYSTEMS THINKING AND ENGINEERING HABITS

---

## M10.1 System design for web engineers

Hours: 24. Prerequisite: M9.12. Phase: 10.

### WHY
System design interviews and real design work ask you to reason about scale, failure and trade-offs without code. You have built a monolith that works; now you learn when and how it stops being enough, and how to say so clearly.

### ATOMS
Method
1. Framework for a design conversation: clarify functional requirements, non-functional requirements (latency, availability, consistency, durability, scale, cost, security, compliance), constraints; estimate load (back-of-envelope); define the API and data model; sketch a high-level design; deep dive on the hardest 1 or 2 components; identify bottlenecks and failure modes; discuss trade-offs and evolution; summarize.
2. Back-of-envelope: powers of two; latency numbers (memory ~100 ns, SSD read ~100 µs, same-datacenter RTT ~0.5 ms, cross-region ~50 to 150 ms); QPS from DAU (DAU × actions per day / 86,400; peak = 2 to 10× average); storage = items × size × replication × retention; bandwidth = QPS × payload; cache sizing (80/20 rule); a single Postgres node handles thousands of simple QPS; know typical limits (a 2 vCPU app server: hundreds to thousands of RPS for light handlers).
3. Requirements trade-off vocabulary: latency vs throughput, consistency vs availability (CAP: under partition choose C or A; PACELC: else latency vs consistency), durability vs write speed, cost vs redundancy, simplicity vs flexibility.

Building blocks
4. Load balancing: L4 vs L7, algorithms (round robin, least connections, consistent hashing), health checks, sticky sessions (avoid), TLS termination, connection draining, global load balancing (DNS, anycast).
5. Caching layers: browser, CDN, reverse proxy, application cache (in-process, Redis/Memcached), DB buffer cache; patterns: cache-aside, read-through, write-through, write-back, refresh-ahead; invalidation strategies (TTL, event-based, versioned keys); stampede protection (single flight, locks, probabilistic early expiry); negative caching; hot keys; cache consistency and the two-hard-problems joke; Redis data structures and persistence basics (RDB/AOF), eviction policies (LRU/LFU), when Redis is justified (shared counters, rate limits, sessions at scale, leaderboards, pub/sub, distributed locks with caveats).
6. Databases: SQL vs NoSQL (document, key-value, wide-column, graph, time-series, search); when Postgres is enough (usually); vertical scaling, read replicas (async replication, replication lag, read-your-writes issues, routing), connection pooling (PgBouncer), partitioning (declarative), sharding (by key; shard key choice; hot shards; resharding pain; cross-shard queries and transactions), consistent hashing, multi-tenant sharding by tenant, denormalization, materialized views, CQRS (separate read models), event sourcing (log of facts; replay; complexity), CDC (Debezium, logical decoding) to feed search/analytics.
7. Replication and consensus: leader/follower, multi-leader, leaderless (quorums, `R + W > N`), conflict resolution (LWW, CRDTs, vector clocks concept), Raft/Paxos conceptually (leader election, log replication), split brain, fencing tokens, distributed locks (Redlock controversy; use DB constraints/leases), clocks (no global time; logical clocks; NTP drift; Hybrid logical clocks), linearizability vs serializability vs eventual consistency, causal consistency, read-your-writes, monotonic reads.
8. Messaging and streams: queues (SQS/RabbitMQ semantics: at-least-once, visibility timeout, DLQ), logs/streams (Kafka: partitions, offsets, consumer groups, retention, ordering per partition), pub/sub, delivery guarantees (at-most-once, at-least-once, effectively-once via idempotency), backpressure, ordering and partition keys, outbox pattern, saga pattern for distributed transactions, idempotent consumers, poison messages, batch vs stream processing.
9. Storage: object storage (S3: durability, eventual vs strong consistency now strong for new objects, lifecycle, presigned URLs, multipart), block vs file vs object, CDN for blobs, content-addressable storage, dedupe, thumbnails pipelines.
10. Search: inverted index, tokenization, ranking (TF-IDF/BM25), Postgres full-text vs Elasticsearch/OpenSearch/Meilisearch/Typesense, indexing pipelines via CDC, eventual consistency of search, autocomplete (prefix tries/n-grams).
11. Networking and edge: DNS (GeoDNS), CDN mechanics (POPs, cache keys, origin shield, purge), WebSockets/SSE fan-out at scale (connection limits, sticky routing, pub/sub backplane), HTTP/2/3, gRPC (know where it fits), API gateway, service discovery, service mesh (know the name), rate limiting at the edge.
12. Reliability patterns: timeouts everywhere, retries with exponential backoff + jitter, retry budgets, idempotency, circuit breakers, bulkheads, load shedding, graceful degradation, hedged requests, health checks, redundancy (N+1, multi-AZ, multi-region active-passive vs active-active), failover, chaos testing, SLO-driven design, blast radius and cell-based architecture, DR (backup/restore, RPO/RTO tiers: backup & restore, pilot light, warm standby, active-active).
13. Consistency in practice: unique constraints and transactions as the ultimate arbiters; optimistic vs pessimistic locking; distributed transactions (2PC) and why to avoid; sagas with compensation; exactly-once effects through idempotent writes; read repair; "do you really need strong consistency here?"
14. Data modeling for scale: access patterns first; hot partitions; time-series bucketing; fan-out on write vs fan-out on read (feeds); precomputation; counters (sharded counters, approximate counting, HyperLogLog), top-K (heaps, count-min sketch), Bloom filters (membership), geohash/quad trees (location), unique ID generation (UUIDv7, Snowflake, ULID; ordering vs coordination).
15. Observability and ops in design: metrics/logs/traces per component, capacity planning, autoscaling signals (CPU, queue depth), deployment strategies (blue-green, canary, feature flags), config management, multi-environment promotion.
16. Security in design: authn/authz boundaries, tenant isolation, encryption at rest/in transit, secrets, least privilege, audit logging, abuse prevention, privacy/compliance (data residency, deletion).
17. Cost awareness: egress costs, cross-AZ traffic, managed vs self-hosted, reserved capacity, cache hit ratio vs spend; "boring technology" (Dan McKinley's innovation tokens).
18. Evolutionary architecture: monolith first, modular monolith, extract services when you have team/scale/deploy-independence reasons; strangler fig; database per service pitfalls; the distributed monolith anti-pattern; Conway's law.

Canonical designs (you will write each)
19. URL shortener (ID generation, redirect latency, caching, analytics).
20. Rate limiter (algorithms, distributed counters, Redis/Lua atomicity, consistency vs accuracy).
21. News feed/timeline (fan-out on write vs read, celebrity problem, caching).
22. Chat/messaging (WebSockets, delivery receipts, ordering, presence, offline sync, storage).
23. Notification system (multi-channel, templates, retries, preferences, idempotency, rate limits, priority).
24. Booking/reservation system (slotlock at scale: inventory, holds with expiry, double booking prevention, payments, cancellations, overbooking policy, multi-region).
25. File storage/sharing (Dropbox-like: chunking, dedupe, sync, metadata store, conflict resolution).
26. Search autocomplete/typeahead (tries, caching, ranking, freshness).
27. Web crawler or job scheduler (politeness, dedupe, distributed queues, retries, exactly-once-ish).
28. Collaborative document editing (OT vs CRDT concept, websockets, snapshots), as a stretch.
29. Payments flow (idempotency, double-entry ledger, reconciliation, webhooks, PCI scope reduction via Stripe).
30. Video streaming/CDN-heavy system (adaptive bitrate, transcoding pipeline), as a stretch.

### RESOURCES (in order)
1. Martin Kleppmann, *Designing Data-Intensive Applications* (DDIA), 2nd edition: read Part I (chapters 1 to 4) and Part II (replication, partitioning, transactions, trouble with distributed systems, consistency and consensus) fully; Part III skim batch/stream. https://dataintensive.net/ . The core of this module.
2. Alex Xu, *System Design Interview* vol. 1 and 2 (worked designs; use as a source of questions and patterns, but practice your own answers first): https://bytebytego.com/ .
3. The System Design Primer (GitHub): https://github.com/donnemartin/system-design-primer . Work through the index, scalability video link, and the Anki cards.
4. Google SRE Book (chapters on load balancing, handling overload, cascading failures, addressing cascading failures, data integrity): https://sre.google/sre-book/table-of-contents/ . Read chapters 19 to 22 and 26.
5. AWS Builders' Library (read at least 10 articles: timeouts/retries/jitter, load shedding, avoiding insurmountable queue backlogs, caching challenges, static stability, workload isolation using shuffle-sharding, leader election, reliability, idempotent APIs, avoiding fallback): https://aws.amazon.com/builders-library/ .
6. Stripe engineering and blog posts: "Designing robust and predictable APIs with idempotency" https://stripe.com/blog/idempotency ; "Online migrations at scale" https://stripe.com/blog/online-migrations ; "Scaling your API with rate limiters" (M6.1).
7. Jeff Dean, "Numbers Every Programmer Should Know" (updated latency table): https://gist.github.com/jboner/2841832 ; Colin Scott's interactive version: https://colin-scott.github.io/personal_website/research/interactive_latency.html .
8. Papers (read abstracts + key sections): Dynamo (https://www.allthingsdistributed.com/files/amazon-dynamo-sosp2007.pdf), Google Spanner (https://research.google/pubs/spanner-googles-globally-distributed-database-2/), Kafka (intro to the log abstraction: https://kafka.apache.org/documentation/#intro_concepts_and_terms; Jay Kreps, "The Log" essay is the classic reading), Raft (https://raft.github.io/raft.pdf and the visualization https://raft.github.io/), "Life Beyond Distributed Transactions" (Pat Helland), "Time, Clocks, and the Ordering of Events" (Lamport).
9. Jepsen analyses and "Consistency Models" map: https://jepsen.io/consistency ; https://aphyr.com/ ("Designing distributed systems" posts).
10. Redis docs, data types and eviction: https://redis.io/docs/latest/develop/data-types/ ; https://redis.io/docs/latest/develop/reference/eviction/ ; Martin Kleppmann, "How to do distributed locking": https://martin.kleppmann.com/2016/02/08/how-to-do-distributed-locking.html .
11. Dan McKinley, "Choose Boring Technology": https://boringtechnology.club/ . Sam Newman, *Building Microservices* (2nd ed., chapters 1 to 3, monolith-first discussion) or Martin Fowler's "MonolithFirst": https://martinfowler.com/bliki/MonolithFirst.html .
12. PostgreSQL docs: high availability, replication, logical replication, partitioning: https://www.postgresql.org/docs/current/high-availability.html ; Citus/Vitess docs for sharding concepts: https://www.vitess.io/docs/ .
13. High Scalability blog archives for architecture case studies (read 10): http://highscalability.com/ . Engineering blogs: Discord ("How Discord stores billions of messages"), Instagram (sharding & IDs), Uber, Netflix (tech blog), Shopify (pods/sharding), Figma (Postgres scaling).
14. Hello Interview and Exponent system design guides (free articles) for structure: https://www.hellointerview.com/learn/system-design/in-a-hurry/introduction .
15. "Hybrid Logical Clocks", UUIDv7 (RFC 9562): https://www.rfc-editor.org/rfc/rfc9562 ; Twitter Snowflake write-up.
16. Mermaid or Excalidraw for diagrams: https://mermaid.js.org/ ; https://excalidraw.com/ .

### START HERE (first 30 minutes)
1. Take the design framework (atom 1) and write it on a card. Memorize the order.
2. Do the numbers: if slotlock had 1M DAU each making 10 requests/day, compute average and peak QPS and storage per year. Write the numbers.

### DRILLS
D1. Numbers drill: 20 estimation problems (QPS, storage, bandwidth, cache size, number of servers) answered in under 3 minutes each with explicit assumptions; check against references; keep a cheat sheet.
D2. Weekly cadence: 10 designs written over the module, each with the framework: requirements, estimates, API, data model, diagram (Mermaid/Excalidraw), deep dive, bottlenecks/failures, trade-offs. Use the canonical list (atoms 19 to 30), prioritizing 19 to 27, 29. Time-box 45 minutes writing, then 30 minutes comparing with a reference and noting gaps in `docs/system-design/<name>.md`.
D3. Deep-dive notes (1 page each, with diagrams) on: consistent hashing; cache-aside with stampede protection; replication and replication lag with read-your-writes solutions; Kafka-style log partitions and consumer groups; outbox + idempotent consumers; leader election with leases; Raft summary; 2PC vs saga; rate limiter algorithms; fan-out on write vs read.
D4. Small experiments to ground concepts: (a) set up Postgres primary + replica in Docker, observe lag and read-your-writes issue, implement sticky reads after write; (b) implement consistent hashing in 50 lines and show key movement when nodes change; (c) simulate cache stampede and fix with single-flight; (d) run Redis, implement a token bucket in a Lua script and test atomicity under concurrency; (e) implement a toy Raft leader election or run the Raft visualization and write what you observed; (f) run Kafka or Redpanda locally and observe consumer group rebalancing.
D5. Slotlock at 100x: write `docs/scaling-slotlock.md`: current capacity (M9.8), the first 5 bottlenecks in order, what you'd do at each stage (indexes, pool/PgBouncer, caching, read replicas, partitioning by workspace, queue offload, CDN, multi-region), what you would not do and why, cost estimates, and failure scenarios.
D6. Failure analysis: for each of 5 designs, list the top 5 failure modes (node loss, partition, slow dependency, hot key, bad deploy), detection, mitigation and data impact.
D7. Mock interviews (via peers/platforms): 6 system design sessions of 45 minutes; record; grade with a rubric (requirements clarity, estimation, API/data, architecture, depth, trade-offs, communication); redo the weakest.
D8. Paper reading group: read Dynamo, Raft, and the Kafka log article; write a 1-page summary each in your own words and one design decision you would apply.
D9. Architecture decision exercises: five "what would you choose and why" prompts (Postgres vs DynamoDB for bookings; Redis vs Postgres for sessions; WebSockets vs SSE vs polling; monolith vs services for a 5-person team; sync vs async payments); each with a decision matrix and a revisit trigger.
D10. Case study reading: read 10 engineering posts (Discord messages, Figma Postgres scaling, Shopify pods, Stripe online migrations, Instagram IDs, GitHub MySQL, Notion sharding, Slack job queue, Uber geospatial, Cloudflare incident); one-paragraph summaries including the decisions and trade-offs.
D11. Teach-back: explain one design (booking system) to a peer with a whiteboard in 20 minutes; collect the questions you couldn't answer; fill the gaps in notes.

### BREAK
- Do a design without asking requirement questions and note which assumption bit you.
- Choose Kafka for a problem that a Postgres table solves; defend, then argue the opposite.
- Remove the numbers from a design and see what you can't justify.
- Run the replication lag experiment with a stale read causing a user-visible bug.

### QUIZ (cold)
1. The design framework in order.
2. Do an estimate: 10M DAU, 5 requests/user/day: QPS average/peak?
3. CAP and PACELC in plain language with an example.
4. Cache-aside, write-through, write-back: pros and cons.
5. How do read replicas break read-your-writes and what are three fixes?
6. Fan-out on write vs read.
7. At-least-once vs exactly-once: how do you get effectively-once?
8. Sagas vs 2PC.
9. When is sharding the answer and what is the pain?
10. Name six reliability patterns and what each prevents.

### GATE
Spoken (45 minutes, mock): a full design of "a booking system for 100M users" with estimates, API, data model, architecture diagram, deep dive on double-booking prevention and holds, failure modes, and an evolution path from slotlock. Graded against the rubric by a peer or an experienced reviewer.
Pass: quiz 9/10, D1 to D11, 10 design docs, rubric score at least 80%, tag `gate/M10.1`.

### DELIVERABLE AND FAILURE MODES
Deliverable: 10 design docs, experiments, scaling-slotlock doc, mock interview logs.
Faking it: memorizing Alex Xu diagrams. Your own designs, written first, then compared.

---

## M10.2 DSA track (parallel)

Hours: 60. Prerequisite: M4.5 (start after Phase 4; runs in parallel on Saturdays and light days, about 1 hour per day, until done). Phase: 10.

### WHY
Many companies screen with algorithmic problems. This is a bounded skill: learn the patterns, practice with spacing, and stop. It is not the curriculum's center, but it is a gate to many interviews. It also sharpens reasoning about the interval/booking logic you built.

### ATOMS
Foundations
1. Complexity: Big-O/Θ/Ω; time vs space; amortized analysis (dynamic array append); best/average/worst; common growth rates (O(1), log n, n, n log n, n², 2ⁿ, n!); analyzing loops, recursion (recurrence intuition, master theorem informally); input size limits to infer target complexity (n ≤ 10⁵ needs O(n log n)).
2. Language fluency (TypeScript/JavaScript): arrays (push/pop O(1), shift/unshift O(n)), `Map`/`Set`, string immutability and building strings, sort (`Array.prototype.sort` with comparator and its stability), integer limits (53-bit, BigInt), bit operations, typed arrays, recursion depth, `structuredClone`; implement missing structures (heap, deque, union-find, trie).
3. Problem-solving procedure (for every problem): restate, examples and edge cases, brute force first with complexity, find the bottleneck, apply a pattern, code cleanly, test with examples and edge cases, analyze complexity, then optimize or generalize.

Data structures
4. Arrays and strings: two pointers, sliding window (fixed and variable), prefix sums, difference arrays, in-place partitioning, Kadane's algorithm, matrix traversal/rotation, string building, anagram/frequency maps.
5. Hashing: frequency counting, two-sum patterns, grouping (group anagrams), sets for dedupe and membership, hash of tuples/strings keys, collision intuition, rolling hash (Rabin-Karp concept), LRU cache (hash map + doubly linked list).
6. Stacks and queues: monotonic stack (next greater element, histogram), monotonic deque (sliding window maximum), valid parentheses, expression evaluation, queue via two stacks, BFS queues.
7. Linked lists: reverse, fast/slow pointers (cycle detection, middle), merge, dummy head, remove nth from end, reorder, copy with random pointer.
8. Trees: binary tree traversals (recursive and iterative: pre/in/post/level), depth/height, balanced check, LCA, serialize/deserialize, BST properties (validate, search, insert, delete, kth smallest), path sums, diameter, tries (prefix tree), segment tree and Fenwick tree (range queries; know existence, implement one), balanced BST concept (AVL/red-black; know but don't implement).
9. Heaps/priority queues: top-K, merge K sorted lists, running median (two heaps), scheduling (task scheduler, meeting rooms II), Dijkstra; implement a binary heap.
10. Graphs: representations (adjacency list/matrix), BFS (shortest unweighted path, levels, multi-source), DFS (components, cycle detection, topological sort via DFS/Kahn), grids as graphs (islands, flood fill), Dijkstra, Bellman-Ford (concept), Floyd-Warshall (concept), MST (Kruskal with union-find, Prim), union-find with path compression and union by rank, bipartite check, strongly connected components (concept), bit-masking states.
11. Sorting and searching: merge sort, quick sort (partition, quickselect), counting/radix sort (concept), binary search (classic, on answer space, rotated array, first/last occurrence), search in 2D matrix, bucket sort for top-K frequency.
12. Recursion and backtracking: subsets, permutations, combinations, N-Queens, word search, generate parentheses, pruning, memoization transition into DP; state space trees.
13. Dynamic programming: identify overlapping subproblems and optimal substructure; top-down memoization vs bottom-up; 1D (climbing stairs, house robber, coin change, LIS), 2D (unique paths, edit distance, LCS), knapsack (0/1, unbounded), partition problems, string DP (palindromes, word break), interval DP (concept), state compression, DP on trees/graphs (concept), bitmask DP (concept).
14. Greedy: interval scheduling, merge intervals, meeting rooms, jump game, gas station, task scheduling; proving greedy via exchange argument; counterexamples.
15. Intervals (directly relevant to booking): sort by start, merge, insert, overlap detection (`a.start < b.end && b.start < a.end` with half-open intervals), sweep line (max concurrent bookings), meeting rooms I/II, interval trees (concept), free/busy slot finding.
16. Math and bits: gcd, primes (sieve), modular arithmetic, fast exponentiation, bit tricks (XOR single number, `n & (n-1)`, counting bits), reservoir sampling, random shuffle (Fisher-Yates), probability basics for design.
17. Advanced topics (recognize, rarely required): KMP/Z algorithm, suffix arrays, Manacher, FFT, flows, bitset tricks; spend no time unless an interview target asks.

Practice protocol
18. Plan: NeetCode 150 (or Blind 75 first), by pattern in the order of atoms; 150 problems at about 20 to 25 minutes each plus review ≈ 60 hours with spaced re-solves; re-solve any problem you needed hints for after 3 days, 7 days, 21 days (cold, 20 minutes).
19. Time-boxing: 20 to 25 minutes of honest attempt; then read a hint, then a solution; understand, close it, re-implement from memory the next day; log pattern, mistake type and time.
20. Solve vs study mix: 60 percent solving new, 40 percent re-solving and pattern review; weekly mock contest (LeetCode weekly contest or Codeforces Div 3/4, optional) for time pressure.
21. Communication practice: speak while solving; whiteboard or plain editor without autocomplete or AI; practice in the interview language and its standard library.
22. Rubric of mastery: for each pattern, you can state when to use it, code the template from memory in 5 minutes, and solve two unseen mediums in 25 minutes each.
23. Stopping rule: after 150 problems with the mastery rubric met and mock interviews passing, drop to maintenance of 2 problems per week; do not grind 800 problems.

### RESOURCES (in order)
1. NeetCode roadmap and NeetCode 150 list with video explanations: https://neetcode.io/roadmap ; https://neetcode.io/practice .
2. LeetCode (practice, Explore cards: Arrays 101, Linked List, Binary Tree, Recursion, Heap, Graph, Dynamic Programming): https://leetcode.com/ ; https://leetcode.com/explore/ .
3. MIT OpenCourseWare 6.006 Introduction to Algorithms (lectures and problem sets; use for foundations): https://ocw.mit.edu/courses/6-006-introduction-to-algorithms-spring-2020/ .
4. Steven Skiena, *The Algorithm Design Manual* (chapters 1 to 5, 7, 8, and the war stories); and Sedgewick/Wayne *Algorithms* (Princeton Coursera course "Algorithms, Part I and II" free audit): https://algs4.cs.princeton.edu/ .
5. Tushar Roy, Back To Back SWE (video explanations, especially DP) and William Fiset (graph theory playlist): https://www.youtube.com/@WilliamFiset-videos ; https://www.youtube.com/@BackToBackSWE .
6. Big-O cheat sheet and `visualgo.net` for visualizing algorithms: https://www.bigocheatsheet.com/ ; https://visualgo.net/ .
7. Cracking the Coding Interview (practice and chapter intros): https://www.crackingthecodinginterview.com/ ; Elements of Programming Interviews (optional deeper practice).
8. Tech Interview Handbook algorithms study cheatsheet: https://www.techinterviewhandbook.org/algorithms/study-cheatsheet/ .
9. Codeforces EDU and AtCoder Beginner Contest for harder structured practice (optional): https://codeforces.com/edu/courses ; https://atcoder.jp/ .
10. TypeScript algorithms repos for reference implementations (read after attempting): https://github.com/trekhleb/javascript-algorithms .
11. Spaced repetition: Anki with pattern cards: https://apps.ankiweb.net/ ; or your own spreadsheet (problem, date, pattern, retry dates).
12. Jeff Erickson, *Algorithms* (free, theory-heavy; reference): https://jeffe.cs.illinois.edu/teaching/algorithms/ .

### START HERE (first 30 minutes)
1. Create `dsa/` repo or folder with a README log table (date, problem, pattern, time, outcome, mistake, next retry).
2. Solve Two Sum and Valid Anagram. Write complexities. Implement a binary heap skeleton and an LRU cache skeleton for later.

### DRILLS
D1. Foundations (week 1 of the track): complexity exercises (analyze 20 code snippets), JS data structure complexity table, implement dynamic array, hash map (open addressing or chaining), stack/queue/deque, linked list, binary heap, trie, union-find; unit tests for each.
D2. Pattern blocks, each with: learn (video/reading, 1h), 10 to 15 problems from NeetCode 150 for the pattern, spaced re-solves, a one-page pattern note (when to use, template code, pitfalls): (a) arrays/hashing/two pointers/sliding window (25 problems); (b) stack/binary search/linked list (22); (c) trees/tries/heap (30); (d) backtracking/graphs/advanced graphs/union-find (28); (e) 1D/2D DP/greedy/intervals/math & bits (45).
D3. Spaced retrieval of problems: re-solve at +3/+7/+21 days; a weekly "cold five": five random previously solved problems under 20 minutes each; track success rate; remedy repeated failures with pattern notes.
D4. Booking-relevant set: meeting rooms I/II, merge intervals, insert interval, non-overlapping intervals, car pooling, my calendar I/II/III, employee free time; implement the sweep-line in TypeScript and compare with your SQL range queries (M4.3, M4.5); write a note linking the two.
D5. Complexity-to-limits: for 20 problems, state the input constraints and the complexity target before reading solutions; verify.
D6. Without tools: 20 problems solved in a plain editor with no autocomplete, no AI, no internet, 30 minutes each, speaking aloud.
D7. Contest practice: 4 LeetCode weekly contests or Codeforces Div 3 rounds for time pressure; analyze every missed problem.
D8. Mock coding interviews: 6 live mocks with peers/platforms (with M9.11), 45 minutes each; record, review communication and bugs.
D9. Data structure from scratch under time: implement LRU cache, min-heap, trie, union-find, and a rate limiter (sliding window) in under 20 minutes each from memory.
D10. Mastery check: per pattern, two unseen medium problems in 25 minutes each with correct complexity statement; pass at least 80%; revisit weak patterns.

### BREAK
- Solve a problem, then rewrite it with a different pattern (e.g., sliding window vs prefix sums) and compare.
- Attempt a hard problem for 60 minutes without hints to learn what being stuck feels like; then study the solution thoroughly.
- Use an off-by-one-prone approach (closed intervals) and see bugs; switch to half-open conventions.

### QUIZ (cold)
1. Complexity of: array shift, hash map lookup, heap push, sort, BFS on a graph, DP over `n × m`.
2. When do you use two pointers vs sliding window vs prefix sums?
3. Template for binary search on the answer.
4. BFS vs DFS: when each?
5. Describe union-find and its complexity with optimizations.
6. When does greedy work (exchange argument)?
7. Memoization vs tabulation.
8. How do you detect a cycle in a linked list and in a directed graph?
9. How do you derive the target complexity from constraints?
10. Interval overlap condition for half-open intervals.

### GATE
Spoken and live: solve two unseen medium problems (45 minutes total) in a plain editor while explaining; one must be a graph or DP problem.
Pass: quiz 9/10, D1 to D10, 150 problems logged, mastery check at least 80%, tag `gate/M10.2`. Start threshold: begin after M4.5; interviews for internships (Part E) are realistic from about 60 solved problems.

### DELIVERABLE AND FAILURE MODES
Deliverable: `dsa/` repo with solutions, pattern notes, spaced log, mock interview records.
Faking it: reading solutions and moving on. A problem counts only after a cold re-solve.

---

## M10.3 Code review, design docs, reading other people's code

Hours: 8. Prerequisite: M9.12. Phase: 10.

### WHY
Most of a senior engineer's output is influence: reviewing, designing, and understanding code you didn't write. These are learnable habits.

### ATOMS
Code review
1. Purpose: catch defects, spread knowledge, maintain consistency and design quality; not gatekeeping or ego.
2. Reviewing: understand the intent (PR description, linked issue), read tests first, review design before details, check correctness and edge cases, security, performance implications, error handling, observability, migration safety, backward compatibility, naming and readability, test quality, docs/changelog; ask questions instead of declaring; separate blocking from nit; suggest alternatives with reasons; approve with comments when risk is low.
3. Checklists: correctness (off-by-one, null/undefined, concurrency, idempotency, transactions), data (migrations expand/contract, indexes, locks), security (authz on every route, input validation, secrets, logging PII), API (compat, error contract), frontend (accessibility, loading/error states, key props, effects deps, memoization myths), operability (logs, metrics, flags, rollback plan), tests (fail without the change, cover edge cases).
4. Receiving review: assume good intent, respond to every comment, push back with data, don't take it personally, keep PRs small (under 400 lines), split refactors from behavior changes, explain the why in descriptions, self-review before requesting.
5. PR hygiene: small focused changes, descriptive titles, linked issue, screenshots/GIFs for UI, test plan, risk/rollback notes, commits that tell a story (atomic; messages: imperative subject ≤ 72 chars, body explains why), squash vs rebase vs merge policy.
6. Review speed: respond within one working day; large PRs get split; pair on complex changes; use draft PRs for early feedback.
7. Tooling: GitHub review features (suggestions, review requests, CODEOWNERS, required reviews, status checks), `gh pr` CLI, linters/formatters to remove style debates, bots (Dependabot), danger-style checks.
8. Review culture: Google's eng practices (the standard: improve overall code health), conventional comments (prefix: `nit`, `suggestion`, `question`, `issue`, `praise`), kindness and precision.

Design docs and decisions
9. When to write a design doc: ambiguous, costly to reverse, cross-team, risky (migration, security), or when alignment is needed; skip for obvious small changes.
10. Structure: context/background, goals and non-goals, requirements, proposed design (diagram, API, data model, flows), alternatives considered (with pros/cons), trade-offs, risks and mitigations, rollout plan (feature flags, migration steps, rollback), observability/testing plan, security/privacy, open questions, timeline, ownership.
11. Writing quality: lead with the decision needed, concrete examples, diagrams, quantify, short sections, explicit assumptions; invite review early; track decisions in ADRs; update after implementation.
12. RFC process for teams: draft, review period, comments resolution, decision recorded, supersede when obsolete.
13. Estimation and planning basics: break down work into vertical slices, identify unknowns early (spikes), estimate ranges not points, communicate risk, track scope; avoid false precision; "plan the next two weeks well, the rest roughly".

Reading code
14. Strategy for unfamiliar repos: read README/docs, run it, run tests, find the entry point, trace one request/feature end to end with a debugger, draw the module map, read tests as documentation, search usage with `rg`, check git history (`git log -p`, `git blame`, PR descriptions), read issues, identify the core abstractions and invariants, note the conventions.
15. Tools: `rg`, `fd`, `git log -S/-G`, `git blame -w -C`, `git bisect`, code intelligence in the editor (go to definition/references), debugger with breakpoints, `tsc --noEmit` and type navigation, call graph tools (`madge`), flame graphs for runtime behavior, dependency graphs.
16. Reading library source: Hono (router, middleware compose, context), TanStack Query core (QueryClient, QueryCache, Observer), `postgres.js` (connection, protocol), `pino` (serializers, transports), React (conceptually, reconciler overview), Zod (parsing); focus on small cores and note the design patterns.
17. Contributing: start with docs and tests, reproduce bugs, small PRs, follow CONTRIBUTING, respond to feedback, be patient.
18. Legacy code: characterization tests, find seams, strangler, refactor under tests, boy-scout rule, document discoveries.
19. Mentoring and communication habits: clear written updates, asking good questions (what I tried, what I expect, minimal repro), explaining trade-offs to non-engineers, giving credit, writing postmortems, running short demos.
20. Engineering judgment heuristics: reversibility (one-way vs two-way doors), blast radius, boring technology, YAGNI with exceptions for hard-to-change decisions (data model, public API, security), delete over add, measure before optimizing, choose defaults that make the right thing easy.

### RESOURCES (in order)
1. Google Engineering Practices, "How to do a code review" and "The CL author's guide": https://google.github.io/eng-practices/review/ . Full.
2. Conventional Comments: https://conventionalcomments.org/ . Michael Lynch, "How to Do Code Reviews Like a Human": https://mtlynch.io/human-code-reviews-1/ . Gergely Orosz, "Good code reviews, better code reviews": https://blog.pragmaticengineer.com/good-code-reviews-better-code-reviews/ .
3. "Software Engineering at Google", chapters 9 (Code Review) and 10 (Documentation) and 7 (Measuring Engineering Productivity optional): https://abseil.io/resources/swe-book/html/ch09.html .
4. Design doc references: Google's design doc culture (Industrial Empathy, "Design Docs at Google"): https://www.industrialempathy.com/posts/design-docs-at-google/ ; Gergely Orosz, "Software Engineering RFCs and Design Documents": https://blog.pragmaticengineer.com/rfcs-and-design-docs/ ; Oxide RFD process: https://rfd.shared.oxide.computer/rfd/0001 .
5. Chris Beams, "How to Write a Git Commit Message": https://cbea.ms/git-commit/ . Pro Git book (history rewriting, bisect): https://git-scm.com/book/en/v2 .
6. Michael Feathers, *Working Effectively with Legacy Code* (concepts: seams, characterization tests); summary article: https://michaelfeathers.silvrback.com/ (browse).
7. Reading code: Spinellis, *Code Reading* (reference) or Amy Ko's "How to read code" notes; Hono source: https://github.com/honojs/hono ; TanStack Query core: https://github.com/TanStack/query/tree/main/packages/query-core ; Pino: https://github.com/pinojs/pino .
8. GitHub docs: CODEOWNERS, protected branches, `gh` CLI manual: https://docs.github.com/en/repositories/managing-your-repositorys-settings-and-features/customizing-your-repository/about-code-owners ; https://cli.github.com/manual/ .
9. Jeff Bezos on one-way vs two-way doors (2015 shareholder letter): https://www.sec.gov/Archives/edgar/data/1018724/000119312516530910/d168744dex991.htm .
10. Julia Evans, "How to ask good questions" and "Debugging strategies": https://jvns.ca/blog/good-questions/ ; https://jvns.ca/blog/debugging-attitude-matters/ .
11. Lara Hogan, "Resilient Management" or "Demystifying Public Speaking" (optional communication).

### START HERE (first 30 minutes)
1. Open a PR in any popular repo (read-only) and review it silently using the Google checklist; write three comments you would leave (one `question`, one `suggestion`, one `praise`).
2. Read the first page of "How to do a code review" and note the standard of review.

### DRILLS
D1. Review practice: review 10 real PRs (5 small, 3 medium, 2 large) in repositories you use (Hono, TanStack, Drizzle, small OSS tools); write your review comments in a doc (not posted unless you're confident); compare against maintainers' actual feedback and note what you missed.
D2. Self-review ritual: for 5 of your own slotlock PRs (real or retroactive), do a checklist review before merging; record defects found; keep PRs under 400 lines; write descriptions with context, risk and rollout.
D3. Peer exchange: swap reviews with two peers on real PRs (via communities); give and receive at least 5 reviews using Conventional Comments; reflect on tone and effectiveness.
D4. Design doc 1: write a design doc for a real next feature in slotlock (e.g., recurring bookings or waitlists) with goals/non-goals, alternatives, risks, rollout, testing; get written feedback from two engineers; revise; record the decision as an ADR.
D5. Design doc 2: write a design doc for a risky migration (e.g., moving sessions to Redis, partitioning `bookings`, or splitting the jobs worker) with a rollback plan and verification steps.
D6. Reading code lab 1: pick Hono; trace a request from `app.fetch` to a handler, draw the middleware composition, describe the router choice; write a 1-page "how Hono works" and one improvement idea.
D7. Reading code lab 2: read TanStack Query core: trace `useQuery` to `QueryObserver` to `Query` to `fetch`; compare with your baby cache; list three design decisions you'd adopt.
D8. Reading code lab 3: pick an unfamiliar project of 5k to 20k lines; in 3 hours, set up, run tests, trace a feature, and write a module map and three questions for maintainers; optionally open a docs/test PR.
D9. Git archaeology: use `git log -S`, `blame -w -C`, `bisect run` to find when and why a behavior was introduced in an open-source repo; write the story (PR, issue, reasoning).
D10. Legacy kata: take a messy 200-line function (provided or from open source), write characterization tests, refactor in small steps, document the seams.
D11. Commit hygiene: rewrite the last 20 commits in a branch with interactive rebase to be atomic and well-messaged; practice `git add -p`, `fixup`, `autosquash`, `reflog` recovery.
D12. Communication drill: write three short updates (status, incident, decision request) in the format "what, so what, now what" and have them reviewed for clarity.
D13. Judgment log: maintain a log of 10 decisions with reversibility rating, evidence, and the revisit trigger; discuss two with a mentor/peer.

### BREAK
- Review a PR you disagree with in style only and confirm your comments are about substance; delete nitpicks that linters could catch.
- Write a design doc with no alternatives section and see how weak it feels; add them.
- Try to understand a large codebase by reading files in alphabetical order, then redo by tracing a request.

### QUIZ (cold)
1. What is the standard a code reviewer holds a change to?
2. What do you review first in a PR: tests, design, or details? Why?
3. List five things you check for a database migration PR.
4. What goes in a design doc? What do alternatives accomplish?
5. What makes a good commit message?
6. How do you read an unfamiliar codebase in the first hour?
7. What are one-way vs two-way doors?
8. How do you find why a line exists?
9. What is a characterization test?
10. How do you disagree in a review without friction?

### GATE
Spoken (5 minutes): "Review this PR" live: given a diff (with seeded bugs: missing authz check, N+1, non-idempotent job handler, unsafe migration) find at least 4 of 5 issues in 15 minutes and give constructive comments; then present one of your design docs in 3 minutes including the strongest alternative you rejected.
Pass: quiz 9/10, D1 to D13, two design docs with external feedback, tag `gate/M10.3`.

### DELIVERABLE AND FAILURE MODES
Deliverable: review log, two design docs, code reading notes, ADRs, git hygiene branch.
Faking it: only reviewing style. Substantive defects found in real PRs are the proof.


---

# PART D. CAPSTONE: `slotlock`

`slotlock` is a multi-tenant booking system (workspaces rent out resources by time slot: meeting rooms, equipment, appointment providers). It is deliberately a domain where correctness is hard: time, overlap, concurrency, money-adjacent flows, multi-tenancy. It is the one thing you ship. Every module in Phases 5 to 9 adds to it. This part is the spec: what it must do, the invariants and how they are enforced, and the order features land.

## D1. Product definition

- A user signs up, creates a workspace (tenant), and invites members.
- A workspace has resources (room, device, person) with a timezone, opening hours, and slot rules (minimum and maximum duration, buffer).
- Members create bookings for resources for a time range. Bookings have a lifecycle: `draft` to `confirmed` to `cancelled` (also `no_show`).
- Bookings can have attachments, a note, and attendees.
- The workspace owner sees a calendar/list, can export data, can configure webhooks, and can see an audit log.
- Customers outside the workspace may use a public booking page for a resource (read-only availability, create a booking request with email verification). This is the public page that drives the SEO/personalization decisions in Phase 8.
- Email notifications: confirmation, reminder (24 hours before), cancellation.

Non-goals (do not build): payments, mobile apps, recurring bookings (design doc only, M10.3), calendar sync (iCal/Google), complex pricing, internationalization beyond timezones, an admin super-panel, real-time collaboration. Anything outside the list requires an ADR and must replace something.

## D2. Domain model (ERD sketch)

Tables and key constraints (the SQL belongs in `pg-lab` first, then migrations in `slotlock`):

- `users(id, email citext unique, email_verified_at, password_hash null, created_at, disabled_at)`
- `identities(provider, provider_user_id, user_id, unique(provider, provider_user_id))`
- `sessions(id_hash, user_id, created_at, last_seen_at, expires_at, idle_expires_at, revoked_at, user_agent, ip)`
- `workspaces(id, slug unique, name, created_at, deleted_at)`
- `memberships(workspace_id, user_id, role check in ('owner','member','viewer'), created_at, primary key(workspace_id, user_id))`
- `invitations(id, workspace_id, email, role, token_hash, expires_at, accepted_at)`
- `resources(id, workspace_id, name, timezone, slot_min_minutes, slot_max_minutes, buffer_minutes, opening_hours jsonb, created_at, archived_at, unique(id, workspace_id))`
- `bookings(id, workspace_id, resource_id, created_by, status, period tstzrange, title, note, version, created_at, updated_at, foreign key (resource_id, workspace_id) references resources(id, workspace_id), exclude using gist (workspace_id with =, resource_id with =, period with &&) where (status = 'confirmed'))`
- `attachments(id, workspace_id, booking_id, storage_key, size, mime, sha256, created_by, created_at, status)`
- `idempotency_keys(user_id, key, request_hash, status, response_code, response_body, created_at, locked_at, primary key(user_id, key))`
- `jobs(...)` (M6.3), `email_log(booking_id, kind, unique)`, `webhook_endpoints`, `webhook_deliveries`, `inbound_events(event_id unique)`
- `audit_log(id, workspace_id, actor_id, action, resource_type, resource_id, result, request_id, at)` append-only
- `rum_events` (M9.7) and `business_metrics` views (M9.2)

Every tenant-scoped table carries `workspace_id`, indexed first in composite indexes, with composite foreign keys where a child references a tenant-owned parent so cross-tenant references are impossible by construction.

## D3. The invariants (the product)

Each invariant has an owner mechanism, a test, and a proof artifact. This table is the heart of the portfolio.

| # | Invariant | Enforced by | Test / proof | Module |
| --- | --- | --- | --- | --- |
| I1 | No two confirmed bookings for the same resource overlap | Postgres exclusion constraint on `tstzrange` | 200 concurrent confirm requests: exactly the non-overlapping set succeeds | M4.3, M6.1 |
| I2 | A retry of a create/confirm never creates a second booking | Idempotency key table with unique `(user_id, key)` and replay of the stored response | 20 concurrent duplicates yield one row and identical responses | M6.1 |
| I3 | A user can only read or change data in workspaces where they are a member, with role-appropriate rights | Membership middleware, tenant-scoped repository functions, permission matrix, RLS defense in depth | IDOR attack matrix, role matrix tests, RLS forgetful-query test | M5.4, M5.6 |
| I4 | Cursor pagination never duplicates or skips rows that exist throughout traversal | Keyset on `(start, id)` with signed cursors | Concurrent insert/delete traversal test | M6.2 |
| I5 | An email is sent at most once per booking event, even if a job runs twice | `email_log` unique constraint + provider idempotency key + state check | Forced double-execution test | M6.3 |
| I6 | Webhook receivers can verify authenticity and deduplicate | HMAC over timestamp+body, event id | Replay/tamper tests, fake receiver | M6.3 |
| I7 | Booking times are correct across DST and timezones | Store UTC `tstzrange`; convert using the resource timezone | DST gap/overlap tests | M7.3 |
| I8 | A booking cannot cross invalid states (e.g., cancelled to confirmed) | State machine table, one function | Table-driven tests | M6.1 |
| I9 | Sessions cannot be fixated, stolen from the DB dump, or survive logout-all | Rotation on login, hashed session ids, server-side revocation | Auth tests, DB-dump drill | M5.1 |
| I10 | A cross-site page cannot cause a mutation | SameSite, Origin/Fetch Metadata check, tokens | CSRF suite | M5.3 |
| I11 | A stored payload cannot execute script | Output encoding, CSP, upload isolation | XSS suite | M5.2, M6.3 |
| I12 | The system degrades predictably under overload | Timeouts, limits, load shedding, rate limits | Overload test | M9.8 |
| I13 | Data can be restored | Tested backup restore with RTO/RPO | Restore drill | M9.4 |
| I14 | An operator can find what happened | Request ids, structured logs, traces, audit log | "User report to root cause" drill | M9.1 to M9.3 |

## D4. API surface (initial; refined in `docs/api.md` at M6.1)

Auth and account
- `POST /auth/register`, `POST /auth/login`, `POST /auth/logout`, `POST /auth/logout-all`, `GET /auth/sessions`, `DELETE /auth/sessions/:id`
- `POST /auth/reset`, `POST /auth/reset/confirm`, `POST /auth/verify-email`
- `GET /auth/google/start`, `GET /auth/google/callback`
- `POST /tokens`, `DELETE /tokens/:id` (API tokens)

Workspaces and members
- `POST /workspaces`, `GET /workspaces`, `GET /workspaces/:wid`
- `GET/POST /workspaces/:wid/members`, `PATCH/DELETE /workspaces/:wid/members/:uid`
- `POST /workspaces/:wid/invitations`, `POST /invitations/:token/accept`

Resources and bookings
- `GET/POST /workspaces/:wid/resources`, `GET/PATCH/DELETE /workspaces/:wid/resources/:rid`
- `GET /workspaces/:wid/resources/:rid/availability?from&to`
- `GET/POST /workspaces/:wid/bookings` (cursor pagination, filters), `GET/PATCH /workspaces/:wid/bookings/:id` (ETag/If-Match)
- `POST /workspaces/:wid/bookings/:id/confirm`, `POST /workspaces/:wid/bookings/:id/cancel`
- `POST /workspaces/:wid/bookings/:id/attachments`, `GET /attachments/:id`

Integrations and ops
- `GET/POST/DELETE /workspaces/:wid/webhooks`, `POST /webhooks/inbound/:provider`
- `GET /workspaces/:wid/audit`, `GET /workspaces/:wid/export`
- `GET /health`, `GET /ready`, `GET /version`, `GET /metrics` (internal), `GET /openapi.json`
- Public: `GET /public/:workspaceSlug/:resourceSlug/availability`, `POST /public/.../requests`

Every route in the contract declares: auth, role, request schema, response schema, status codes, idempotency requirement, rate limit class.

## D5. Architecture (target)

```mermaid
flowchart LR
  B[Browser SPA / public page] -->|HTTPS| CDN[CDN optional]
  CDN --> C[Caddy: TLS, compression, static]
  C --> A1[API: Hono + postgres.js]
  C --> A2[API instance 2]
  A1 --> PG[(PostgreSQL)]
  A2 --> PG
  A1 --> S3[(Object storage)]
  W[Worker: jobs, webhooks, email] --> PG
  W --> S3
  W --> SMTP[Email provider]
  W --> EXT[Customer webhooks]
  A1 -. metrics/traces/logs .-> OBS[Prometheus, Loki, Tempo, Grafana, Sentry]
  W -. metrics/traces/logs .-> OBS
```

One TypeScript monorepo-lite (no Turborepo): `apps/api`, `apps/web`, `apps/worker`, shared `packages/schemas` (Zod), `migrations/`, `docs/`, `ops/` (compose, Caddyfile, Grafana provisioning, scripts), `e2e/`.

## D6. How slotlock is built, in order

| Module | What lands in slotlock |
| --- | --- |
| M6.1 | Repo, contract doc, validation, error contract, bookings API, idempotency, state machine, rate limiter |
| M6.2 | Cursor pagination, filters, indexes doc |
| M6.3 | Attachments, job queue, email, webhooks in/out, SSRF defense |
| M6.4 | OpenAPI, config, Docker, CI, first deploy, health/ready |
| M7.x | Web client: list/detail/edit, optimistic cancel, conflict UX, accessibility |
| M8.x | Public booking page rendering experiments (in `labs/render-lab`), decision recorded as ADR |
| M9.1 to M9.3 | Logging, Sentry, RED metrics, trace, dashboards, alerts |
| M9.4 | VPS deployment with backups |
| M9.5 | Incidents and postmortems |
| M9.6 | Test suite, mutation score, CI gating |
| M9.7 to M9.8 | Performance work, load test, capacity |
| M9.9 | Threat model, scanning, assessment |
| M9.10 | Deletions |
| M9.11 to M9.12 | Post, mock interviews, README, architecture doc, evidence index |

Auth (Phase 5) is built in `labs/auth-lab` and copied into `slotlock` at M6.1 as the first commit.

## D7. Definition of done for a feature (every feature, every time)

1. Contract line in `docs/api.md` (written first).
2. Zod schema; validation errors map to the error contract.
3. Authorization via `can()`; IDOR test for each ID.
4. DB migration (expand/contract if destructive), indexes with EXPLAIN evidence if it adds a query.
5. Integration tests (real Postgres) and any invariant tests.
6. Idempotency considered; double-submit and retry behavior stated.
7. Logs/metrics for the new path; error codes added to the catalog.
8. Accessibility pass for UI.
9. Docs/ADR if a decision was made.
10. Deployed to staging and verified before prod.

## D8. Capstone rules

- No new feature after M6.4 unless it replaces something or is required by a module drill.
- Every claim in the README links to evidence (a test, a plan, a chart).
- Never rewrite; refactor under tests.
- The repo history is part of the artifact: small commits, imperative messages, tags per gate.

---

# PART E. CAREER TRACK (runs in parallel)

The curriculum makes you capable. This part makes you hired. Start early: interviewing is a skill that decays without practice, and the feedback from real interviews is worth more than another week of reading.

## E1. Timeline

| When | Action |
| --- | --- |
| Week 1 | GitHub profile cleaned; set up a simple portfolio page (one page, 1 hour) |
| After M4.5 (about week 11) | Start DSA track (M10.2) in the Saturday slot; solve 60 problems before first coding interviews |
| After M6.4 (about week 16) | Deployed slotlock exists. Start applying for internships/junior roles; target 5 applications per week while continuing the curriculum |
| After M8.8 (about week 21) | The talk video and render metrics are public; add to the portfolio |
| After M9.4 | Production story (VPS, backups) enters your resume |
| After M9.11 | Blog post published; mock interview cadence 2 per week |
| After M9.12 | Full push: 15+ applications per week, referrals, OSS contributions in progress |
| Always | Keep the curriculum running. Interviews are practice, not a pause |

If you are a student with campus recruiting windows (autumn for summer internships in many countries), align the calendar so that M6.4 lands before the application deadlines. If it does not, keep applying year-round to startups, which hire on rolling timelines.

## E2. What to target

Roles, in order of realistic fit at the end of this curriculum: internships (summer or part-time during study), junior/associate fullstack roles at small companies and startups, freelance projects for small businesses (booking, forms, dashboards), contributions to open-source projects. Large-company new-grad pipelines emphasize DSA and system design lightly; startups value shipped systems and operational judgment. Apply to both; your slotlock evidence is differentiated.

Company selection criteria: small engineering team (5 to 50), production system with real users, uses TypeScript/Postgres (or something you can learn), has code review and on-call culture, mentors juniors. Ask about these in the first call.

Geography/remote: remote-friendly companies get applications from everywhere; compete on evidence. Local companies and startup communities respond to personal outreach and meetups.

## E3. The portfolio (minimal, strict)

1. `slotlock` README, live demo, architecture doc, evidence index (M9.12).
2. One technical post (M9.11) and at least one case study post.
3. The recorded Phase 8 talk.
4. Open source: 2 merged contributions (M9.12).
5. A one-page résumé; a simple personal site; GitHub profile README with 3 pinned repos (`slotlock`, `tiny-http` or `pg-lab`, `dsa` optional).

Do not pad the portfolio with tutorial clones. One deep project beats five shallow ones.

## E4. The résumé (rules)

- One page. Contact, links (GitHub, site, LinkedIn), 2-line summary.
- Projects first (before education if you have little experience): slotlock with outcomes: "Booking API where no two confirmed bookings can overlap (Postgres exclusion constraints); 0 double bookings under 200 concurrent requests; idempotent writes with crash recovery; p95 120ms at 600 RPS on a 2 vCPU VPS; deployed with tested backups, RTO 12 min".
- Bullet formula: action verb + what + how + measurable result.
- Skills section: only skills you can defend in a 15-minute deep-dive (the curriculum's frozen stack).
- No "proficient in everything"; no objective statement; no photos (country dependent); no tutorials listed as projects.
- Have two people read it for 30 seconds each and tell you what they remember.

## E5. Outreach

Cold email template (3 lines + proof):

```
Subject: <Role> - built a booking API with no double bookings (live demo)

Hi <name>, I'm <you>, a student building production-grade web systems. I read <specific thing about their product or a recent post>, and I think <specific observation or question about their domain>.
I built slotlock: <one line>. Live: <url>. Evidence: <link to architecture/evidence index>. I'd love to contribute as an intern/junior; is there someone I should talk to?
```

Rules: personalize the first sentence, one link to the evidence index, no attachments, follow up once after 5 business days, send 5 to 10 per week, track every message. Referrals multiply response rates; use meetups, community Discords, alumni, open-source maintainers you interacted with.

Applications: tailor the top third of the résumé to the job description keywords you genuinely have; apply within 48 hours of a posting; track in a sheet (company, role, date, source, contact, status, next action).

## E6. Mock interview bank (spoken, cold, 3 minutes each)

Use the standard AI prompt in A8 or a peer. Each answer must state the mechanism, a failure mode, and a concrete example from your work. A strong answer is correct, complete, concise and concrete (the four Cs).

HTTP and the browser
1. What happens when you type a URL and press Enter? (DNS, TCP, TLS, HTTP request, cache, response, parsing, render; mention caching and connection reuse.)
2. Explain CORS, including preflight, and why it doesn't protect against CSRF.
3. Explain cookies: attributes, SameSite, `__Host-`, origin vs site.
4. How does HTTP caching work: `Cache-Control`, ETag, conditional requests, `immutable`, `Vary`, shared caches?
5. HTTP/1.1 vs HTTP/2 vs HTTP/3; head-of-line blocking.
6. How would you stream a long-running result to a browser? SSE vs WebSockets vs polling.
7. What is the critical rendering path and what blocks it?
8. What do LCP, INP and CLS measure and how do you improve each?
9. What is the event loop; how does `async/await` differ from threads?
10. Closure, `this`, prototype: explain with an example of a bug.

Databases
11. Explain an index (B-tree); when won't it be used? How do you read `EXPLAIN ANALYZE`?
12. What are transaction isolation levels; show a lost update and three ways to prevent it.
13. How do you prevent double booking in SQL?
14. Explain N+1 and how you detect/fix it.
15. Explain a migration strategy for adding a NOT NULL column to a 100M-row table without downtime.
16. Window functions: top 3 bookings per resource.
17. When do you use a CTE, a materialized view, or partitioning?
18. What is MVCC and what does `VACUUM` do?

Auth and security
19. How do sessions work? Why not JWT for sessions? How would you revoke?
20. Explain how you store passwords and why.
21. XSS vs CSRF; defenses for each.
22. What is IDOR? How do you test for it?
23. Explain OAuth 2.0 Authorization Code with PKCE and OIDC.
24. RBAC vs ABAC vs ReBAC.
25. How do you design multi-tenant isolation in Postgres?
26. What is SSRF and how do you defend?

APIs and backend
27. Design `POST /orders` so retries cannot double-charge.
28. How do you paginate correctly? Why not offset?
29. How do you design webhooks (sending and receiving)?
30. A background job can run twice. What do you do?
31. How do you do rate limiting? Which algorithm and why?
32. REST vs RPC vs GraphQL; how do you choose?
33. How do you version an API?
34. What does your error contract look like and why?

Frontend and rendering
35. Server state vs client state; why TanStack Query?
36. What is hydration and what can go wrong?
37. CSR vs SSR vs SSG vs RSC: the four questions.
38. What does a server function actually do over HTTP and what must you secure?
39. How do you make a form accessible?
40. How do you prevent double submit in the UI and API?

Production
41. What do you log? Where do request ids come from?
42. RED vs USE; what do you alert on?
43. How do you deploy with zero downtime and roll back?
44. Walk me through an incident you handled.
45. How would you find why a page is slow?
46. What would you do if production Postgres ran out of connections?
47. How do you test? What don't you test?
48. How do you do a load test and avoid coordinated omission?

System design and engineering practice
49. Design a rate limiter / URL shortener / notification system / booking system.
50. Cache stampede: what is it and how do you prevent it?
51. CAP and PACELC; consistency models.
52. How do you review code? What do you look for?
53. How do you decide to delete or simplify?
54. Tell me about a technical decision you reversed.

Behavioral (STAR, 2 minutes)
55. A time you were wrong.
56. A time you had to learn something fast.
57. A time you disagreed with someone.
58. A time you shipped under a constraint.
59. Your biggest bug.
60. Why this company/role?

Scoring: for each answer, score 0 to 2 on correctness, completeness, concision, concreteness (max 8). Anything below 6 goes back into `recall/missed.md`. Practice until 50 of 60 questions score at least 7.

## E7. Interview day mechanics

Before: research the product and team; prepare 5 questions for them; check the tooling (camera, internet, editor, language); sleep; practice a warm-up problem.
During: restate the problem, clarify, propose an approach, discuss complexity, code, test, optimize; think aloud; if stuck, say what you've tried and what you're considering; ask for a hint after 5 minutes of silence rather than 20.
After: write what was asked within an hour; add missed items to recall; send a short thank-you; follow up on timelines.

Take-home assignments: timebox (cap at 4 to 6 hours unless paid); include tests, README, trade-offs and what you'd do next; deploy if asked; don't over-build; ask clarifying questions up front.

## E8. Practical interview prompts (practice with a timer)

Frontend (45 minutes each, plain editor): autocomplete with debounce and abort; infinite scroll list with error and retry; data table with sort/filter/pagination; modal with focus trap; form with validation and accessibility; image carousel; star rating; tabs (keyboard accessible); todo with persistence; countdown timer; drag-and-drop list.
Backend (45 minutes each): rate limiter middleware; idempotent create endpoint; cursor pagination query; job queue with retries; webhook signature verification; auth session middleware; LRU cache; URL shortener API; booking overlap checker with tests; SQL puzzles (top-N per group, gaps and islands, running totals, overlapping ranges).
Debugging: given a repo with 3 planted bugs (race, N+1, missing authz), find and fix in 30 minutes.

## E9. Freelance and open source

Freelance: start with 1 or 2 small projects for people you know or local businesses (booking page, internal tool, forms+dashboard). Write a one-page scope, fixed price per milestone, explicit exclusions, a written change process. Use your own practices: staging environment, backups, logs, handover docs. Keep contracts simple; get a deposit; never work without agreed scope. These projects supply the "consequences" seniors have.

Open source: pick a library you use (Hono, TanStack, Playwright, Drizzle, Caddy, Pino). Order: improve docs, add a test, fix a labeled `good first issue`, then a small feature after discussing it. Read `CONTRIBUTING.md`; reproduce first; keep PRs small; accept review gracefully. Two merged PRs are enough for the portfolio; more only if you enjoy it.

Community: attend one local meetup/month (or an online equivalent), answer questions on Stack Overflow/Discords once a week (teaching is retrieval practice), and keep a short "what I learned" post per month.

## E10. Negotiation and first-job survival

- Know your range (levels.fyi, local data), your alternatives, and your priorities (learning, mentorship, stability, pay).
- Never lie about competing offers; ask for time; negotiate once, politely, with reasons.
- Early-career priorities: a team that reviews your code seriously, on-call exposure with support, production systems, and a manager who gives feedback. Pay differences of 10 percent matter less than 2 years of high-quality mentorship.
- First 90 days: set up the dev environment and fix its docs; ship a small PR in week 1; ask questions with context (what I tried, what I expect); keep a work log; learn the deploy and on-call process; read the last 10 postmortems; find a mentor; write down what confuses you (documentation backlog).

## E11. The path from here to senior (honest)

- Year 0 to 1: be reliable (deliver small things correctly, review thoroughly, communicate status).
- Year 1 to 3: own a feature end to end, including operations; lead a small design doc; mentor an intern; reduce an incident class.
- Year 3 to 5: own a system; make cross-team technical decisions; shape standards; delete large chunks safely.
- Seniority is judgment under uncertainty, demonstrated repeatedly, plus the ability to make others effective. Systems you've run through incidents, migrations and growth are the evidence. This curriculum gives you the vocabulary, mechanisms and habits; time and responsibility give you the rest.

---

# PART F. APPENDICES

## F1. Self-audit (re-run at the end of every phase)

Score each statement 0 (cannot), 1 (with notes), 2 (cold, spoken, with a failure mode). Any 0 or 1 becomes a module revisit in the next calendar week.

HTTP and browser
- I can narrate a request from DNS to pixels, including connection reuse and caching.
- I can write correct `Cache-Control` for HTML, hashed assets, API JSON and private data.
- I can explain each CORS header and the preflight rules from memory.
- I can set up cookies safely and explain `SameSite`, site vs origin, `__Host-`.
- I can read a HAR and a Performance panel trace and name the bottleneck.
- I can explain HTTP/2 multiplexing, TCP HOL blocking, HTTP/3.
- I can choose between polling, SSE and WebSockets with reasons.

JavaScript and runtime
- I can predict the order of output for mixed promises, timers and microtasks.
- I can explain closures, `this`, prototypes, modules and bundling effects.
- I can write and cancel async flows with `AbortController` and handle races.
- I can explain serialization limits and what crosses a boundary.

Databases
- I can model a domain with constraints (including exclusion) and justify normalization.
- I can read `EXPLAIN ANALYZE` and fix a slow query with the right index.
- I can reproduce and fix a lost update and a write skew.
- I can run a zero-downtime migration.
- I can explain MVCC, vacuum, and connection pooling.

Auth and security
- I can implement session auth and explain every cookie flag.
- I can demonstrate and fix XSS, CSRF, IDOR, SSRF.
- I can trace OAuth/OIDC and describe state, PKCE, nonce.
- I can design an authorization model with RBAC/ABAC/ReBAC and RLS.

APIs
- I can design resource APIs with an error contract and compatibility strategy.
- I can implement idempotency with crash recovery.
- I can implement correct cursor pagination.
- I can design reliable webhooks and background jobs.

Client and rendering
- I can build a query cache and use TanStack Query knowingly.
- I can implement optimistic UI with rollback and conflict handling.
- I can build accessible forms and dialogs.
- I can state the four rendering questions for any page and defend a choice with measurements.
- I can explain RSC, server functions, hydration, streaming, caching layers.

Production
- I can set up logs, errors, metrics, traces and alerts that are actionable.
- I can deploy to a server, secure it, back it up, restore it.
- I can run and write up an incident.
- I can design tests that catch real bugs and run them in CI.
- I can run the performance loop and a load test and state capacity.
- I can threat-model and run a security audit.
- I can delete and simplify with evidence.

Engineering practice
- I can do a back-of-envelope estimate and a system design with trade-offs.
- I can review code, write a design doc and read an unfamiliar codebase.
- I can solve medium DSA problems in 25 minutes and explain them.
- I can write clearly and present a technical talk.

## F2. Master resource shelf

Use this only as a lookup. Module resources are authoritative; this shelf groups them.

Standards and primary sources
- RFC 9110 HTTP Semantics: https://www.rfc-editor.org/rfc/rfc9110 ; RFC 9111 HTTP Caching: https://www.rfc-editor.org/rfc/rfc9111 ; RFC 9112 HTTP/1.1 ; RFC 9113 HTTP/2 ; RFC 9114 HTTP/3 ; RFC 6265bis cookies: https://httpwg.org/http-extensions/draft-ietf-httpbis-rfc6265bis.html ; RFC 8446 TLS 1.3 ; RFC 6454 Origin.
- WHATWG Fetch Standard (CORS): https://fetch.spec.whatwg.org/ ; HTML Standard: https://html.spec.whatwg.org/ ; URL Standard: https://url.spec.whatwg.org/ .
- MDN Web Docs: https://developer.mozilla.org/ ; web.dev: https://web.dev/ ; Chrome for Developers: https://developer.chrome.com/ .
- ECMAScript spec: https://tc39.es/ecma262/ ; Node.js docs: https://nodejs.org/docs/latest/api/ ; Bun docs: https://bun.sh/docs ; TypeScript Handbook: https://www.typescriptlang.org/docs/ .
- PostgreSQL docs: https://www.postgresql.org/docs/current/ ; Use The Index, Luke: https://use-the-index-luke.com/ ; Postgres wiki: https://wiki.postgresql.org/ .
- OWASP Cheat Sheets: https://cheatsheetseries.owasp.org/ ; OWASP API Top 10: https://owasp.org/API-Security/ ; ASVS ; NIST 800-63B.

Books (read selectively as the modules direct)
- Martin Kleppmann, *Designing Data-Intensive Applications* (2nd ed.).
- Ilya Grigorik, *High Performance Browser Networking* (free): https://hpbn.co/ .
- Ivan Ristic, *Bulletproof TLS and PKI* (reference).
- Dafydd Stuttard and Marcus Pinto, *The Web Application Hacker's Handbook* (reference).
- Arnaud Lauret, *The Design of Web APIs*.
- Michael Nygard, *Release It!* (2nd ed.).
- Google, *Site Reliability Engineering* and *The SRE Workbook* (free): https://sre.google/books/ .
- Winters, Manshreck, Wright, *Software Engineering at Google* (free): https://abseil.io/resources/swe-book .
- John Ousterhout, *A Philosophy of Software Design*.
- Martin Fowler, *Refactoring* (2nd ed.).
- Marijn Haverbeke, *Eloquent JavaScript* (free): https://eloquentjavascript.net/ ; Kyle Simpson, *You Don't Know JS Yet* (free): https://github.com/getify/You-Dont-Know-JS .
- Sam Newman, *Building Microservices* (later, optional).
- Gene Kim et al., *The Phoenix Project* / *Accelerate* (context, optional).
- Gayle Laakmann McDowell, *Cracking the Coding Interview*; Skiena, *The Algorithm Design Manual*.

Courses and talks (free where possible)
- MIT 6.006 and 6.033/6.824 (distributed systems with Raft labs): https://pdos.csail.mit.edu/6.824/ .
- CS50 (foundations if needed): https://cs50.harvard.edu/x/ ; The Missing Semester of Your CS Education: https://missing.csail.mit.edu/ .
- Frontend Masters / Epic React (paid; optional); Josh Comeau's courses (paid; optional); React docs (free, primary).
- Philip Roberts, "What the heck is the event loop anyway?": https://www.youtube.com/watch?v=8aGhZQkoFbQ .
- Jake Archibald, "In The Loop": https://www.youtube.com/watch?v=cCOL7MC4Pl0 .
- Gil Tene, "How NOT to Measure Latency".
- Rich Hickey, "Simple Made Easy"; Gary Bernhardt, "Boundaries".

Blogs and sites worth reading regularly (bounded: 1 hour/week)
- Julia Evans (jvns.ca), Brandur Leach (brandur.org), TkDodo (tkdodo.eu), Dan Abramov (overreacted.io), Josh Comeau (joshwcomeau.com), web.dev/blog, Marc Brooker (brooker.co.za), AWS Builders' Library, Stripe Engineering blog, Netflix/Uber/Discord/Cloudflare engineering blogs, Kent C. Dodds, Phil Walton (philipwalton.com), Aphyr (jepsen.io), High Scalability.

Tools (frozen stack and the aids)
- Core: Bun or Node, TypeScript, Hono, PostgreSQL 16+, `postgres.js`, Zod, React 19, TanStack Query, Playwright, Pino, Sentry, Caddy, Docker, GitHub Actions.
- Aids: `curl`, `jq`, `httpie` optional, `mitmproxy`, `wireshark`/`tcpdump`, `dig`, `openssl s_client`, `psql`, `pgcli`, `pg_stat_statements`, `k6`, `autocannon`, Lighthouse, WebPageTest, Prometheus, Grafana, Loki, OpenTelemetry, Burp/ZAP, Semgrep, gitleaks, trivy.

## F3. The ignore list (protect your attention)

Until Phase 9 is passed, ignore:
- Framework launches, "X is dead" posts, benchmarks of frameworks, tier lists, "Top 10 libraries" videos.
- Tutorial playlists (more than one hour per week on video learning).
- Social feeds as a learning source (X/Twitter, LinkedIn thought leadership); use RSS/newsletters only for the blogs listed.
- Tool setup rabbit holes (dotfiles, terminals, themes) beyond the M0.1 baseline.
- Rewriting your repo in a new stack.
- Advanced topics on the banned list (A5): GraphQL, gRPC, Kafka, Kubernetes, microservices, Redis (except M10.1 conceptual), MongoDB, Firebase, monorepo tooling, micro-frontends.
- "Senior in X months" promises.
- Certifications (AWS/Azure) unless a specific job requires one.
- Comparing your progress to others.

## F4. Glossary (say each in one sentence, with an example)

Atomicity, availability, backpressure, BOLA, bearer token, cache-aside, canonical log line, CAP, CDN, circuit breaker, closure, CORS, CRDT, CSP, CSRF, cursor pagination, deadlock, DNS, DOM, double-spend, DPoP, DST, ETag, error budget, event loop, exclusion constraint, expand/contract, fan-out, Fetch Metadata, flame graph, hydration, idempotency, IDOR, index (B-tree/GIN/GiST), isolation level, JWT, keep-alive, lease, linearizability, load shedding, long task, MVCC, N+1, nonce, OIDC, optimistic locking, origin vs site, outbox, p99, PKCE, preflight, prototype pollution, RED, replication lag, resumability, RLS, RPO, RTO, RSC, saga, SameSite, SLI/SLO, SSG, SSR, SSE, SSRF, stale-while-revalidate, streaming SSR, TOCTOU, trace/span, transaction, TTFB, uncanny valley, USE, vacuum, WAL, WebSocket, write skew, XSS.

## F5. Mixed quiz bank (Saturday draws)

Use this as a source for the 20-question Saturday mixed draw, in addition to module quizzes. Answer cold, then check against module notes.

1. Order output: `console.log(1); setTimeout(()=>console.log(2)); Promise.resolve().then(()=>console.log(3)); queueMicrotask(()=>console.log(4)); console.log(5)`.
2. What does `Vary: Origin` do and when do you need it?
3. Why can't you use `Access-Control-Allow-Origin: *` with credentials?
4. What does a `304` response contain?
5. Give `Cache-Control` for `app.3f9a2.js`, `index.html`, `GET /api/me`.
6. What does `SameSite=Lax` allow?
7. Why is a request id not a trace id?
8. Why does `OFFSET` get slower?
9. When is a partial index useful?
10. Show a lost update and fix it with `SELECT ... FOR UPDATE`.
11. What does `SKIP LOCKED` enable?
12. Why `tstzrange` and a GiST exclusion constraint for bookings?
13. What happens to a long transaction and `VACUUM`?
14. What's wrong with `UPDATE ... SET balance = balance - 10` without a check constraint?
15. Why store `sha256(session_token)`?
16. Which attacks does CSP stop and which not?
17. What's the safe way to link OAuth accounts?
18. What does RLS protect against that app-level checks don't?
19. What does an idempotency key table store?
20. What does `useSyncExternalStore` require of `getSnapshot`?
21. What's wrong with `useEffect` for fetching?
22. What's the difference between `staleTime` and `gcTime`?
23. What is optimistic UI rollback?
24. What is the uncanny valley in SSR?
25. What can't be a prop from a server component to a client component?
26. What is in an RSC payload?
27. List the cache layers in a Next app.
28. What does `revalidateTag` do?
29. What's the difference between liveness and readiness?
30. What does a histogram bucket choice affect?
31. Why can't you average p95s?
32. What is coordinated omission?
33. What's the SLO error budget for 99.9% in 30 days?
34. What are three signs of pool exhaustion?
35. What goes in a blameless postmortem?
36. What does a mutation test measure?
37. What makes a test flaky?
38. Little's law: define and use.
39. What's expand/contract?
40. What's a dead letter queue?
41. What does SSRF defense need to check at connect time?
42. Why not trust `Content-Type` on upload?
43. What is a fencing token?
44. What's the difference between serializability and linearizability?
45. How does consistent hashing reduce key movement?
46. What are the four golden signals?
47. What problem does the outbox solve?
48. What does `Retry-After` do?
49. Why do retries need jitter?
50. What belongs in a design doc's non-goals?

## F6. Templates

### F6.1 Gate record (`NOTES.md`)

```
## M<id> gate (<date>)
Spoken memo: evidence/M<id>-spoken-<date>.m4a (duration, stalls: <n>)
Written answer: (from memory, under 60 minutes)
Quiz score: <n>/10 (questions saved in recall/questions.md)
Deliverable: <tag gate/M<id>> <commit sha>
Failed attempts: <n>; root cause if any:
```

### F6.2 ADR (`docs/adr/NNNN-title.md`)

```
# NNNN. <Decision title>
Status: proposed | accepted | superseded by NNNN
Date: <date>
Context: <the forces at play, constraints, evidence>
Options considered: <A, B, C with pros/cons>
Decision: <what we chose and why>
Consequences: <good, bad, what we must now do, revisit trigger>
```

### F6.3 Postmortem (`docs/incidents/YYYY-MM-DD-title.md`)

```
# <Title>
Date, duration, severity, author
Summary (3 sentences)
Impact (who, how many, what data)
Timeline (UTC): detection, escalation, mitigation, resolution
Root causes and contributing factors
Detection: how found; how long after start
Resolution
What went well / what went poorly / where we got lucky
Action items: (owner, due, type: prevent|detect|mitigate, link)
Lessons
```

### F6.4 Design doc

```
# <Title>
Author, reviewers, status, date
Context and problem
Goals / Non-goals
Requirements (functional, non-functional, constraints)
Proposed design (diagram, API, data model, flows)
Alternatives considered (and why not)
Risks, mitigations, open questions
Rollout and rollback plan
Testing, observability, security notes
Timeline and ownership
```

### F6.5 Weekly review (Saturday, 15 minutes)

```
Week <n>: modules attempted/passed:
Hours: planned <30>, actual <>:
Gates passed:
Recall misses:
Biggest misconception corrected:
Urges (shiny things I wanted to chase):
Next week's modules and risks:
Tracker updated: yes/no
```

### F6.6 Runbook

```
# Alert/Failure: <name>
Symptoms and dashboards
Impact
Immediate mitigation (commands)
Diagnosis steps
Escalation
Verification of recovery
Prevention follow-ups
Last tested: <date>
```

## F7. LEARNING-LOG template (daily, 5 minutes in the Close block)

File: `LEARNING-LOG.md` (append one block per day).

```
## <YYYY-MM-DD> (Mon..Sat)
Module / drills: M<id> D<n>..D<n>
Hours: study <h>, build <h>, break <h>
Energy (1-5): <n>  Sleep (h): <n>
Shipped today (one concrete artifact): <commit/link>
Stuck on: <specific question>
What I now understand that I didn't this morning: <one sentence>
What I faked (hand-waved) today and must revisit: <item>
Tomorrow's first action (to remove startup friction): <file/command>
```

Rules: no entry may say "learned about X"; write the mechanism or the bug. Review the last 7 entries each Saturday; count "faked" items and clear them before moving on.

## F8. Command cheat sheet (build muscle memory; M0.1 drills this)

```
# HTTP
curl -sS -i -H 'Accept: application/json' https://host/path
curl -sS -D - -o /dev/null https://host/            # headers only
curl -sS -X POST -H 'Content-Type: application/json' -d '{"a":1}' https://host/x
curl -sS --http2 -v https://host/ ; curl --http3 ...    # protocol checks
curl -sS -w '%{time_namelookup} %{time_connect} %{time_appconnect} %{time_starttransfer} %{time_total}\n' -o /dev/null https://host/
openssl s_client -connect host:443 -servername host -showcerts
dig +trace example.com ; dig A example.com @1.1.1.1
ss -tulpn ; lsof -i :3000

# Postgres
psql "$DATABASE_URL" -c '\d+ bookings'
EXPLAIN (ANALYZE, BUFFERS, VERBOSE) <query>;
select * from pg_stat_activity where state <> 'idle';
select pid, pg_blocking_pids(pid), query from pg_stat_activity where cardinality(pg_blocking_pids(pid)) > 0;
select query, calls, total_exec_time, mean_exec_time from pg_stat_statements order by total_exec_time desc limit 10;

# Git
git add -p ; git commit -m 'Add ...' ; git rebase -i HEAD~10 ; git bisect start/good/bad/run
git log -S 'needle' -- path ; git blame -w -C file ; git reflog

# Node/Bun
node --cpu-prof app.js ; node --inspect-brk app.js ; bun --inspect app.ts
autocannon -c 50 -d 20 http://localhost:3000/ ; k6 run script.js

# Linux ops
journalctl -u app -f ; systemctl status app ; df -h ; free -m ; top / htop ; iostat -x 1 ; dmesg -T | tail
ufw status verbose ; docker ps ; docker logs -f <c> ; docker stats
```

## F9. Final hireable checklist

The authoritative checklist is in M9.12. Copy it into `docs/hireable.md` and tick each item with a link to evidence. Re-run F1 (self-audit) and E6 (mock bank) one last time; any score below 6 returns to the module that owns it.

---

END OF DOCUMENT
