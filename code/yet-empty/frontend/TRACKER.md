# TRACKER

Source of truth for order, hours and gates: `~/downloads/markdown/web-engineering-master-curriculum.md` (Part B and Part C).
Replaces the old 48-week tracker that referenced the three deleted docs (they are in the system trash, recoverable).

Rules: one module at a time, in order. A module is done only when its gate is passed (spoken + written + 10-question quiz + tagged deliverable). Tick the box after `git tag gate/M<id>`. Phase exit tests are cold, one sitting.

Budget: ~712 core hours (M10.2 DSA is a parallel 60h track) = about 26 to 27 calendar weeks at 30h/week.


## Phase 0. Tooling foundation

- [ ] M0.1 Shell, Linux, git workflow (8h, needs none) | gate date: ____ | retrieval +1 +3 +7 +21 +60: ____
- [ ] M0.2 TypeScript and toolchain (12h, needs M0.1) | gate date: ____ | retrieval +1 +3 +7 +21 +60: ____
- [ ] M0.3 Debugging toolkit: curl, jq, debugger, psql, DevTools basics (8h, needs M0.2) | gate date: ____ | retrieval +1 +3 +7 +21 +60: ____

## Phase 1. JavaScript runtime

- [ ] M1.1 Values, scope, closures, `this`, modules (14h, needs M0.2) | gate date: ____ | retrieval +1 +3 +7 +21 +60: ____
- [ ] M1.2 Event loop, promises, async, cancellation (16h, needs M1.1) | gate date: ____ | retrieval +1 +3 +7 +21 +60: ____
- [ ] M1.3 Serialization and a raw HTTP server (`tiny-http`) (14h, needs M1.2) | gate date: ____ | retrieval +1 +3 +7 +21 +60: ____
- [ ] M1.4 Bundlers, threads, long tasks (Phase 1 exit) (10h, needs M1.3) | gate date: ____ | retrieval +1 +3 +7 +21 +60: ____
- [ ] **Phase 1 exit test** passed (cold, one sitting) | date: ____

## Phase 2. HTTP in depth

- [ ] M2.1 URLs, methods, status codes, headers (14h, needs M1.4) | gate date: ____ | retrieval +1 +3 +7 +21 +60: ____
- [ ] M2.2 Cookies, origin vs site (12h, needs M2.1) | gate date: ____ | retrieval +1 +3 +7 +21 +60: ____
- [ ] M2.3 CORS (14h, needs M2.2) | gate date: ____ | retrieval +1 +3 +7 +21 +60: ____
- [ ] M2.4 HTTP caching (14h, needs M2.1) | gate date: ____ | retrieval +1 +3 +7 +21 +60: ____
- [ ] M2.5 Transport: latency, TCP, TLS, HTTP/1.1, 2, 3 (12h, needs M2.4) | gate date: ____ | retrieval +1 +3 +7 +21 +60: ____
- [ ] M2.6 Compression, chunked, SSE, WebSockets (Phase 2 exit) (14h, needs M2.5) | gate date: ____ | retrieval +1 +3 +7 +21 +60: ____
- [ ] **Phase 2 exit test** passed (cold, one sitting) | date: ____

## Phase 3. Browser and performance

- [ ] M3.1 Critical rendering path (14h, needs M2.6) | gate date: ____ | retrieval +1 +3 +7 +21 +60: ____
- [ ] M3.2 Core Web Vitals, fonts, images (14h, needs M3.1) | gate date: ____ | retrieval +1 +3 +7 +21 +60: ____
- [ ] M3.3 DevTools fluency (8h, needs M3.2) | gate date: ____ | retrieval +1 +3 +7 +21 +60: ____
- [ ] M3.4 Hydration, streaming, JS cost (Phase 3 exit) (12h, needs M3.3) | gate date: ____ | retrieval +1 +3 +7 +21 +60: ____
- [ ] **Phase 3 exit test** passed (cold, one sitting) | date: ____

## Phase 4. PostgreSQL

- [ ] M4.1 SQL that is real (16h, needs M0.3) | gate date: ____ | retrieval +1 +3 +7 +21 +60: ____
- [ ] M4.2 Indexes and EXPLAIN (16h, needs M4.1) | gate date: ____ | retrieval +1 +3 +7 +21 +60: ____
- [ ] M4.3 Transactions, isolation, lost updates (18h, needs M4.2) | gate date: ____ | retrieval +1 +3 +7 +21 +60: ____
- [ ] M4.4 Pooling, N+1, migrations (12h, needs M4.3) | gate date: ____ | retrieval +1 +3 +7 +21 +60: ____
- [ ] M4.5 Window functions, CTEs, partitioning, vacuum (Phase 4 exit) (16h, needs M4.4) | gate date: ____ | retrieval +1 +3 +7 +21 +60: ____
- [ ] **Phase 4 exit test** passed (cold, one sitting) | date: ____

## Phase 5. Authentication and authorization

- [ ] M5.1 Passwords and sessions (14h, needs M4.5, M2.2) | gate date: ____ | retrieval +1 +3 +7 +21 +60: ____
- [ ] M5.2 XSS and CSP (10h, needs M5.1) | gate date: ____ | retrieval +1 +3 +7 +21 +60: ____
- [ ] M5.3 CSRF and Fetch Metadata (10h, needs M5.2) | gate date: ____ | retrieval +1 +3 +7 +21 +60: ____
- [ ] M5.4 Authorization and IDOR (12h, needs M5.3) | gate date: ____ | retrieval +1 +3 +7 +21 +60: ____
- [ ] M5.5 OAuth 2.0 and OIDC (12h, needs M5.4) | gate date: ____ | retrieval +1 +3 +7 +21 +60: ____
- [ ] M5.6 RBAC, ABAC, ReBAC, RLS, secrets (Phase 5 exit) (14h, needs M5.5) | gate date: ____ | retrieval +1 +3 +7 +21 +60: ____
- [ ] **Phase 5 exit test** passed (cold, one sitting) | date: ____

## Phase 6. API as a product

- [ ] M6.1 Resources, errors, validation, idempotency, rate limits (16h, needs M5.6) | gate date: ____ | retrieval +1 +3 +7 +21 +60: ____
- [ ] M6.2 Pagination, filtering, ORM peek (10h, needs M6.1) | gate date: ____ | retrieval +1 +3 +7 +21 +60: ____
- [ ] M6.3 Uploads, webhooks, background jobs (16h, needs M6.2) | gate date: ____ | retrieval +1 +3 +7 +21 +60: ____
- [ ] M6.4 Contract, health, first deploy (Phase 6 exit) (10h, needs M6.3) | gate date: ____ | retrieval +1 +3 +7 +21 +60: ____
- [ ] **Phase 6 exit test** passed (cold, one sitting) | date: ____

## Phase 7. Client data

- [ ] M7.1 Server state vs client state, baby query cache (12h, needs M6.4) | gate date: ____ | retrieval +1 +3 +7 +21 +60: ____
- [ ] M7.2 Query lifecycle, SWR, waterfalls, races (12h, needs M7.1) | gate date: ____ | retrieval +1 +3 +7 +21 +60: ____
- [ ] M7.3 Mutations, optimistic UI, double submit (14h, needs M7.2) | gate date: ____ | retrieval +1 +3 +7 +21 +60: ____
- [ ] M7.4 Same UI twice (Phase 7 exit) (12h, needs M7.3) | gate date: ____ | retrieval +1 +3 +7 +21 +60: ____
- [ ] **Phase 7 exit test** passed (cold, one sitting) | date: ____

## Phase 8. Where code runs

- [ ] M8.1 Rendering models and the four questions (8h, needs M7.4, M3.4) | gate date: ____ | retrieval +1 +3 +7 +21 +60: ____
- [ ] M8.2 Measuring CSR vs SSR on the same page (12h, needs M8.1) | gate date: ____ | retrieval +1 +3 +7 +21 +60: ____
- [ ] M8.3 React Server Components (16h, needs M8.2) | gate date: ____ | retrieval +1 +3 +7 +21 +60: ____
- [ ] M8.4 Server functions and actions (12h, needs M8.3) | gate date: ____ | retrieval +1 +3 +7 +21 +60: ____
- [ ] M8.5 Hydration bugs, streaming, Suspense (10h, needs M8.4) | gate date: ____ | retrieval +1 +3 +7 +21 +60: ____
- [ ] M8.6 Caching models in Next (12h, needs M8.5) | gate date: ____ | retrieval +1 +3 +7 +21 +60: ____
- [ ] M8.7 Steelman, plus Solid lab (6h, needs M8.6) | gate date: ____ | retrieval +1 +3 +7 +21 +60: ____
- [ ] M8.8 Phase 8 exit talk (6h, needs M8.7) | gate date: ____ | retrieval +1 +3 +7 +21 +60: ____
- [ ] **Phase 8 exit test** passed (cold, one sitting) | date: ____

## Phase 9. Production

- [ ] M9.1 Structured logs and request identity (8h, needs M8.8) | gate date: ____ | retrieval +1 +3 +7 +21 +60: ____
- [ ] M9.2 Error tracking, source maps, one business metric (8h, needs M9.1) | gate date: ____ | retrieval +1 +3 +7 +21 +60: ____
- [ ] M9.3 RED metrics and one trace (10h, needs M9.2) | gate date: ____ | retrieval +1 +3 +7 +21 +60: ____
- [ ] M9.4 Deploy like an adult (VPS once) (14h, needs M9.3) | gate date: ____ | retrieval +1 +3 +7 +21 +60: ____
- [ ] M9.5 Cause an incident, write the postmortem (10h, needs M9.4) | gate date: ____ | retrieval +1 +3 +7 +21 +60: ____
- [ ] M9.6 Tests that are not theater, plus CI (14h, needs M9.5) | gate date: ____ | retrieval +1 +3 +7 +21 +60: ____
- [ ] M9.7 The performance loop on slotlock (12h, needs M9.6) | gate date: ____ | retrieval +1 +3 +7 +21 +60: ____
- [ ] M9.8 Load testing (12h, needs M9.7) | gate date: ____ | retrieval +1 +3 +7 +21 +60: ____
- [ ] M9.9 Security pass: OWASP API Top 10 (12h, needs M9.8) | gate date: ____ | retrieval +1 +3 +7 +21 +60: ____
- [ ] M9.10 Delete and simplify (8h, needs M9.9) | gate date: ____ | retrieval +1 +3 +7 +21 +60: ____
- [ ] M9.11 Technical post and mock interviews (16h, needs M9.10) | gate date: ____ | retrieval +1 +3 +7 +21 +60: ____
- [ ] M9.12 The hireable artifact (Phase 9 exit) (12h, needs M9.11) | gate date: ____ | retrieval +1 +3 +7 +21 +60: ____
- [ ] **Phase 9 exit test** passed (cold, one sitting) | date: ____

## Phase 10. Systems thinking and habits

- [ ] M10.1 System design for web engineers (24h, needs M9.12) | gate date: ____ | retrieval +1 +3 +7 +21 +60: ____
- [ ] M10.2 DSA track (parallel, 1h/day Saturdays and light days) (60h, needs M4.5) | gate date: ____ | retrieval +1 +3 +7 +21 +60: ____
- [ ] M10.3 Code review, design docs, reading other people's code (8h, needs M9.12) | gate date: ____ | retrieval +1 +3 +7 +21 +60: ____

## Career track (Part E)

- [ ] After M4.5: start DSA track (M10.2), 60 problems before first coding interviews
- [ ] After M6.4: start internship/junior applications (5 per week)
- [ ] After M9.4: production story on resume
- [ ] After M9.11: post published, 2 mock interviews per week
- [ ] After M9.12: 15+ applications per week, 2 OSS contributions

## Weekly review (Saturday)

Copy template F6.5 from the curriculum here each week.
