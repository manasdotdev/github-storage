----

This guide is a complete learning map for mastering SQL using PostgreSQL as the main database.

It is written for a beginner who currently knows little or nothing about databases, but wants to become genuinely strong: able to design schemas, write serious SQL, understand performance, debug production issues, and eventually build database-backed tools that other developers respect.

This is not a weekend checklist. Treat it as a multi-month to multi-year roadmap.

## The Core Idea

SQL mastery is not just memorizing commands.

You need to master:

- Relational thinking
- Schema design
- PostgreSQL data types ==
- Constraints ==
- Joins ==
- Aggregation
- Subqueries
- Common table expressions
- Window functions
- Indexes
- Query planning
- Transactions
- Concurrency
- Migrations
- Security
- Production operations
- PostgreSQL-specific features
- Performance debugging
- Real application modeling

Your goal is to become someone who can look at a product requirement and design the data model, write the queries, predict performance issues, and protect the system from bad data and race conditions.

## 1. SQL Foundations

Start here. Do not skip this layer.

Learn:

- What a relational database is
- What a table is
- What rows and columns are
- What a schema is
- What a database is
- What SQL is used for
- Difference between SQL and PostgreSQL
- Difference between database, schema, table, relation, row, and column
- Basic PostgreSQL data types
- How to use `psql`
- How to run SQL files
- How to inspect tables
- How to read PostgreSQL error messages

Core commands:

```sql
CREATE DATABASE app;
CREATE SCHEMA app;
CREATE TABLE users (...);
DROP TABLE users;
ALTER TABLE users ADD COLUMN ...;
INSERT INTO users ...;
SELECT * FROM users;
UPDATE users SET ...;
DELETE FROM users;
```

Learn the categories of SQL:

- DDL: Data Definition Language
- DML: Data Manipulation Language
- DQL: Data Query Language
- DCL: Data Control Language
- TCL: Transaction Control Language

Example:

```sql
CREATE TABLE users (
  id bigint GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
  email text NOT NULL UNIQUE,
  name text NOT NULL,
  created_at timestamptz NOT NULL DEFAULT now()
);
```

Master this before getting fancy.

## 2. PostgreSQL Data Types

You need to know which type to use and why.

Learn:

- `smallint`
- `integer`
- `bigint`
- `numeric`
- `real`
- `double precision`
- `serial`
- `bigserial`
- Identity columns
- `text`
- `varchar`
- `char`
- `boolean`
- `date`
- `time`
- `timestamp`
- `timestamptz`
- `interval`
- `uuid`
- `json`
- `jsonb`
- Arrays
- Enums
- Ranges
- `bytea`
- `inet`
- `cidr`
- `macaddr`
- `money`, and why you usually should not use it

Important distinctions:

- Use `text` by default for strings.
- Use `timestamptz` for real moments in time.
- Use `numeric` for money and precise decimal values.
- Use `bigint` or `uuid` for IDs depending on system needs.
- Prefer `jsonb` over `json` for queryable JSON.
- Avoid `float` for money.
- Avoid storing dates as strings.

Practice:

```sql
SELECT now();
SELECT current_date;
SELECT gen_random_uuid();
SELECT '2026-07-06'::date;
SELECT '{"theme":"dark"}'::jsonb;
```

## 3. Table Design

This is the heart of SQL.

Learn:

- Primary keys
- Foreign keys
- Unique constraints
- Check constraints
- Not-null constraints
- Default values
- Generated columns
- Composite primary keys
- Composite unique constraints
- Surrogate keys
- Natural keys
- Join tables
- Lookup tables
- Audit columns
- Soft deletes
- Hard deletes
- Cascading deletes
- Restricted deletes
- Deferred constraints

Example:

```sql
CREATE TABLE organizations (
  id bigint GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
  name text NOT NULL,
  slug text NOT NULL UNIQUE,
  created_at timestamptz NOT NULL DEFAULT now()
);

CREATE TABLE users (
  id bigint GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
  email text NOT NULL UNIQUE,
  name text NOT NULL,
  created_at timestamptz NOT NULL DEFAULT now()
);

CREATE TABLE memberships (
  organization_id bigint NOT NULL REFERENCES organizations(id) ON DELETE CASCADE,
  user_id bigint NOT NULL REFERENCES users(id) ON DELETE CASCADE,
  role text NOT NULL CHECK (role IN ('owner', 'admin', 'member')),
  created_at timestamptz NOT NULL DEFAULT now(),
  PRIMARY KEY (organization_id, user_id)
);
```

You should know why every constraint exists.

## 4. Basic Querying

Learn:

- `SELECT`
- `FROM`
- `WHERE`
- `ORDER BY`
- `LIMIT`
- `OFFSET`
- Column aliases
- Table aliases
- Arithmetic expressions
- Boolean expressions
- String comparisons
- Date comparisons
- `IS NULL`
- `IS NOT NULL`
- `IN`
- `NOT IN`
- `BETWEEN`
- `LIKE`
- `ILIKE`
- Regular expressions
- `DISTINCT`
- `DISTINCT ON`

Examples:

```sql
SELECT id, email
FROM users
WHERE email ILIKE '%gmail.com'
ORDER BY created_at DESC
LIMIT 20;
```

```sql
SELECT DISTINCT ON (user_id) user_id, status, created_at
FROM subscriptions
ORDER BY user_id, created_at DESC;
```

Master the logical order of SQL:

1. `FROM`
2. `JOIN`
3. `WHERE`
4. `GROUP BY`
5. `HAVING`
6. `SELECT`
7. `DISTINCT`
8. `ORDER BY`
9. `LIMIT`

This matters because SQL is not executed in the order beginners visually read it.

## 5. Filtering Logic

Learn deeply:

- `AND`
- `OR`
- `NOT`
- Parentheses
- Truth tables
- Three-valued logic
- `NULL` behavior
- `IS DISTINCT FROM`
- `COALESCE`
- `NULLIF`

Important examples:

```sql
SELECT NULL = NULL; -- not true
SELECT NULL IS NULL; -- true
SELECT NULL IS DISTINCT FROM NULL; -- false
```

Most beginners get destroyed by `NULL`. Master it early.

## 6. Joins

This is non-negotiable.

Learn:

- Inner join
- Left join
- Right join
- Full outer join
- Cross join
- Self join
- Lateral join
- Semi join concept
- Anti join concept

Examples:

```sql
SELECT users.email, organizations.name
FROM memberships
JOIN users ON users.id = memberships.user_id
JOIN organizations ON organizations.id = memberships.organization_id;
```

```sql
SELECT users.*
FROM users
LEFT JOIN memberships ON memberships.user_id = users.id
WHERE memberships.user_id IS NULL;
```

Learn when joins multiply rows. This is one of the biggest beginner traps.

Master questions like:

- Which users have no posts?
- Which customers have never paid?
- Which products have more than 10 orders?
- Which organizations have exactly one owner?
- Which posts have tags A and B?
- Which users have their latest login per device?

## 7. Aggregation

Learn:

- `COUNT`
- `SUM`
- `AVG`
- `MIN`
- `MAX`
- `GROUP BY`
- `HAVING`
- Aggregate filtering
- Grouping multiple columns
- `array_agg`
- `json_agg`
- `string_agg`
- Ordered aggregates
- Conditional aggregation

Examples:

```sql
SELECT organization_id, count(*) AS member_count
FROM memberships
GROUP BY organization_id
HAVING count(*) > 10;
```

```sql
SELECT
  customer_id,
  sum(amount) FILTER (WHERE status = 'paid') AS paid_total,
  sum(amount) FILTER (WHERE status = 'failed') AS failed_total
FROM invoices
GROUP BY customer_id;
```

Master the difference between `WHERE` and `HAVING`.

## 8. Subqueries

Learn:

- Scalar subqueries
- Table subqueries
- Correlated subqueries
- `EXISTS`
- `NOT EXISTS`
- `IN`
- `ANY`
- `ALL`

Examples:

```sql
SELECT *
FROM users u
WHERE EXISTS (
  SELECT 1
  FROM posts p
  WHERE p.user_id = u.id
);
```

```sql
SELECT *
FROM users
WHERE id IN (
  SELECT user_id
  FROM memberships
);
```

Master `EXISTS`. It is extremely useful.

## 9. Common Table Expressions

Learn:

- `WITH`
- Multiple CTEs
- Recursive CTEs
- CTEs for readability
- CTEs for data modification
- Materialized vs non-materialized CTE behavior

Example:

```sql
WITH paid_invoices AS (
  SELECT *
  FROM invoices
  WHERE status = 'paid'
)
SELECT customer_id, sum(amount)
FROM paid_invoices
GROUP BY customer_id;
```

Recursive example:

```sql
WITH RECURSIVE category_tree AS (
  SELECT id, parent_id, name, 1 AS depth
  FROM categories
  WHERE parent_id IS NULL

  UNION ALL

  SELECT c.id, c.parent_id, c.name, ct.depth + 1
  FROM categories c
  JOIN category_tree ct ON c.parent_id = ct.id
)
SELECT *
FROM category_tree;
```

Use recursive CTEs for trees and hierarchies.

## 10. Window Functions

This is where SQL becomes powerful.

Learn:

- `OVER`
- `PARTITION BY`
- `ORDER BY` inside windows
- `row_number`
- `rank`
- `dense_rank`
- `lag`
- `lead`
- `first_value`
- `last_value`
- Running totals
- Moving averages
- Percentiles
- Window frames

Examples:

```sql
SELECT
  user_id,
  amount,
  created_at,
  row_number() OVER (
    PARTITION BY user_id
    ORDER BY created_at DESC
  ) AS invoice_rank
FROM invoices;
```

```sql
SELECT
  created_at::date AS day,
  amount,
  sum(amount) OVER (
    ORDER BY created_at::date
  ) AS running_total
FROM payments;
```

Master these problems:

- Latest row per group
- Top N per group
- Running balance
- Retention analysis
- Ranking users
- Previous/next event
- Sessionization

## 11. Insert, Update, and Delete Mastery

Learn:

- Multi-row inserts
- `INSERT ... SELECT`
- `RETURNING`
- `ON CONFLICT DO NOTHING`
- `ON CONFLICT DO UPDATE`
- Bulk updates
- Update with joins
- Delete with joins
- Safe deletes
- Transactions around writes

Examples:

```sql
INSERT INTO users (email, name)
VALUES ('a@example.com', 'A')
RETURNING id;
```

```sql
INSERT INTO users (email, name)
VALUES ('a@example.com', 'Alice')
ON CONFLICT (email)
DO UPDATE SET name = EXCLUDED.name
RETURNING *;
```

```sql
UPDATE users
SET name = 'Deleted User'
WHERE deleted_at IS NOT NULL
RETURNING id;
```

Always learn how to preview destructive queries before running them.

## 12. Constraints As Business Logic

Good SQL developers push correctness into the database.

Learn:

- Not-null constraints
- Unique constraints
- Foreign keys
- Check constraints
- Exclusion constraints
- Deferred constraints
- Partial unique indexes

Examples:

```sql
ALTER TABLE users
ADD CONSTRAINT users_email_not_empty
CHECK (length(trim(email)) > 0);
```

```sql
CREATE UNIQUE INDEX one_active_subscription_per_customer
ON subscriptions (customer_id)
WHERE status = 'active';
```

This is how you prevent impossible states.

## 13. Indexes

Indexes are mandatory.

Learn:

- B-tree indexes
- Composite indexes
- Index column order
- Unique indexes
- Partial indexes
- Expression indexes
- Covering indexes with `INCLUDE`
- GIN indexes
- GiST indexes
- BRIN indexes
- Hash indexes
- Index-only scans
- Index bloat
- Write overhead
- Selectivity
- Cardinality

Examples:

```sql
CREATE INDEX users_email_idx ON users (email);
```

```sql
CREATE INDEX posts_author_created_idx
ON posts (author_id, created_at DESC);
```

```sql
CREATE INDEX users_lower_email_idx
ON users (lower(email));
```

```sql
CREATE INDEX active_subscriptions_idx
ON subscriptions (customer_id)
WHERE status = 'active';
```

Master this rule:

An index is not magic that makes every query faster. It is a separate data structure that speeds some reads and slows writes.

## 14. Query Planning

Learn:

- `EXPLAIN`
- `EXPLAIN ANALYZE`
- Sequential scan
- Index scan
- Index-only scan
- Bitmap index scan
- Bitmap heap scan
- Nested loop
- Hash join
- Merge join
- Sort
- Aggregate
- Cost
- Actual time
- Estimated rows
- Actual rows
- Loops
- Buffers
- Planning time
- Execution time

Use:

```sql
EXPLAIN ANALYZE
SELECT *
FROM posts
WHERE author_id = 1
ORDER BY created_at DESC
LIMIT 10;
```

Then learn:

```sql
EXPLAIN (ANALYZE, BUFFERS)
SELECT ...
```

You should eventually understand why PostgreSQL chose a plan.

## 15. Transactions

Learn:

- `BEGIN`
- `COMMIT`
- `ROLLBACK`
- Savepoints
- ACID
- Atomicity
- Consistency
- Isolation
- Durability
- Transaction boundaries
- Failed transaction state

Example:

```sql
BEGIN;

UPDATE accounts
SET balance = balance - 100
WHERE id = 1;

UPDATE accounts
SET balance = balance + 100
WHERE id = 2;

COMMIT;
```

You must understand what happens if the second update fails.

## 16. Isolation and Concurrency

This separates serious people from casual users.

Learn:

- Read committed
- Repeatable read
- Serializable
- Dirty reads
- Non-repeatable reads
- Phantom reads
- Lost updates
- Write skew
- Row locks
- Table locks
- Deadlocks
- `SELECT ... FOR UPDATE`
- `FOR NO KEY UPDATE`
- `FOR SHARE`
- `SKIP LOCKED`
- `NOWAIT`
- Advisory locks

Example:

```sql
SELECT *
FROM jobs
WHERE status = 'pending'
ORDER BY created_at
FOR UPDATE SKIP LOCKED
LIMIT 1;
```

This pattern is used for job queues.

Master:

- Booking systems
- Bank transfers
- Inventory reservations
- Payment idempotency
- Distributed worker queues

## 17. Views and Materialized Views

Learn:

- Views
- Updatable views
- Materialized views
- Refreshing materialized views
- Concurrent refresh
- When views help
- When views hide complexity

Examples:

```sql
CREATE VIEW active_users AS
SELECT *
FROM users
WHERE deleted_at IS NULL;
```

```sql
CREATE MATERIALIZED VIEW daily_revenue AS
SELECT
  created_at::date AS day,
  sum(amount) AS revenue
FROM payments
WHERE status = 'paid'
GROUP BY created_at::date;
```

## 18. Functions and Procedures

Learn:

- SQL functions
- PL/pgSQL functions
- Stored procedures
- Function volatility
- `IMMUTABLE`
- `STABLE`
- `VOLATILE`
- Parameters
- Return types
- Table-returning functions
- Security definer risks

Example:

```sql
CREATE FUNCTION full_name(first_name text, last_name text)
RETURNS text
LANGUAGE sql
IMMUTABLE
AS $$
  SELECT first_name || ' ' || last_name;
$$;
```

Use functions carefully. They are powerful, but can hide too much logic.

## 19. Triggers

Learn:

- Before insert triggers
- After insert triggers
- Before update triggers
- After update triggers
- Row-level triggers
- Statement-level triggers
- Audit triggers
- Updated-at triggers
- Trigger risks

Example use cases:

- Auto-update `updated_at`
- Write audit logs
- Enforce complex invariants
- Maintain derived tables

Brutal advice: triggers are useful, but invisible behavior can become a maintenance nightmare.

## 20. JSON and JSONB

Learn:

- `json`
- `jsonb`
- JSON operators
- Querying nested fields
- Indexing JSONB
- `jsonb_build_object`
- `jsonb_agg`
- `to_jsonb`
- `jsonb_set`
- GIN indexes
- When JSONB is good
- When JSONB is schema laziness

Examples:

```sql
SELECT preferences->>'theme'
FROM users;
```

```sql
CREATE INDEX users_preferences_idx
ON users USING gin (preferences);
```

Use JSONB for flexible metadata, not for everything.

## 21. Arrays

Learn:

- Array columns
- `unnest`
- Array containment
- Array aggregation
- GIN indexes on arrays
- When arrays are acceptable
- When join tables are better

Example:

```sql
SELECT *
FROM posts
WHERE tags @> ARRAY['postgres'];
```

Most many-to-many relationships should use join tables, not arrays.

## 22. Full-Text Search

Learn:

- `tsvector`
- `tsquery`
- `to_tsvector`
- `plainto_tsquery`
- `websearch_to_tsquery`
- Ranking
- Highlighting
- GIN indexes
- `pg_trgm`
- Fuzzy search

Example:

```sql
SELECT *
FROM posts
WHERE to_tsvector('english', title || ' ' || body)
@@ websearch_to_tsquery('english', 'postgres indexes');
```

Also learn:

```sql
CREATE EXTENSION pg_trgm;
```

Useful for typo-tolerant search.

## 23. Date and Time

Learn this carefully.

Learn:

- `date`
- `time`
- `timestamp`
- `timestamptz`
- Time zones
- Intervals
- Date truncation
- Date arithmetic
- Recurring events
- Calendar boundaries
- Storing user time zones
- UTC
- Daylight saving time

Examples:

```sql
SELECT date_trunc('day', created_at)
FROM posts;
```

```sql
SELECT now() + interval '7 days';
```

Rule: use `timestamptz` for real moments in time.

## 24. Migrations

Learn:

- Schema migration files
- Forward migrations
- Rollback migrations
- Idempotent migrations
- Data migrations
- Backfills
- Locking during migrations
- Zero-downtime migrations
- Expand/contract pattern
- Avoiding table rewrites
- Adding columns safely
- Adding constraints safely
- Creating indexes concurrently

Example:

```sql
CREATE INDEX CONCURRENTLY posts_author_id_idx
ON posts (author_id);
```

Migration safety is a critical production skill.

## 25. Security and Permissions

Learn:

- Roles
- Users
- Grants
- Revokes
- Schemas
- Row-level security
- Policies
- Least privilege
- SQL injection
- Prepared statements
- Search path risks
- Security definer risks

Examples:

```sql
GRANT SELECT ON users TO readonly_user;
```

```sql
ALTER TABLE documents ENABLE ROW LEVEL SECURITY;
```

If you use Supabase, row-level security becomes especially important.

## 26. Import, Export, and Bulk Data

Learn:

- `COPY`
- CSV import/export
- `pg_dump`
- `pg_restore`
- Bulk inserts
- Staging tables
- Cleaning imported data
- Deduplication
- Upserts at scale

Example:

```sql
COPY users(email, name)
FROM '/path/users.csv'
WITH (FORMAT csv, HEADER true);
```

## 27. Analytics SQL

Learn:

- Funnels
- Cohorts
- Retention
- Revenue reports
- Rolling averages
- Percentiles
- Histograms
- Time bucketing
- Event tables
- Fact tables
- Dimension tables
- Star schemas
- Slowly changing dimensions

Example concepts:

- Daily active users
- Monthly recurring revenue
- Churn rate
- Conversion rate
- Cohort retention
- Top customers
- p95 latency from event logs

This is where SQL becomes business power.

## 28. Advanced PostgreSQL Features

Learn:

- Partitioning
- Table inheritance basics
- Extensions
- `LISTEN` / `NOTIFY`
- Advisory locks
- Generated columns
- Exclusion constraints
- Range types
- `ltree`
- `hstore`
- Foreign data wrappers
- Logical replication
- Publications/subscriptions
- Temporary tables
- Unlogged tables

Especially useful extensions:

- `pg_stat_statements`
- `pg_trgm`
- `uuid-ossp` or `pgcrypto`
- `postgis`
- `pgvector`
- `btree_gin`
- `btree_gist`

## 29. Vector Search in PostgreSQL

As of 2026, vector search matters because of AI applications.

Learn:

- Embeddings
- Vector similarity
- Cosine distance
- Inner product
- L2 distance
- `pgvector`
- HNSW indexes
- IVFFlat indexes
- Hybrid search
- Reranking
- Metadata filtering
- Chunking documents

Example:

```sql
CREATE EXTENSION vector;

CREATE TABLE documents (
  id bigint GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
  content text NOT NULL,
  embedding vector(1536)
);
```

Use `pgvector` first unless you have a real reason to use a dedicated vector database.

## 30. Testing SQL

Learn:

- Testing constraints
- Testing migrations
- Testing queries
- Seed data
- Fixtures
- Transactional tests
- Snapshotting query results
- Performance regression tests
- Testing race conditions

Practice:

- Write tests that prove duplicate emails fail.
- Write tests that prove invalid role values fail.
- Write tests that prove money transfers remain correct.
- Write tests that prove migrations work from old schema to new schema.

## 31. Debugging SQL

Learn how to debug:

- Syntax errors
- Type errors
- Constraint violations
- Deadlocks
- Slow queries
- Bad joins
- Duplicate rows
- Missing rows
- Null bugs
- Time zone bugs
- Migration failures
- Permission errors

Useful `psql` commands:

```sql
\d users
\d+ users
\dt
\dn
\du
\l
\x
```

## 32. SQL Style

Learn good habits:

- Use clear table names.
- Use consistent singular/plural style.
- Use explicit joins.
- Avoid `SELECT *` in app code.
- Name constraints.
- Name indexes clearly.
- Use lowercase SQL if your team does.
- Format long queries.
- Avoid clever SQL when readable SQL works.
- Prefer constraints over comments.
- Prefer correctness over convenience.

Example index name:

```sql
CREATE INDEX posts_author_id_created_at_idx
ON posts (author_id, created_at DESC);
```

## 33. Real Schemas To Practice

Build these from scratch:

- Blog
- Ecommerce store
- Banking ledger
- Chat app
- Issue tracker
- Calendar app
- File storage system
- Notification system
- Subscription billing
- Multi-tenant SaaS
- Job queue
- Audit log system
- Inventory management
- Analytics event pipeline
- Permission system
- Forum
- Learning platform
- Marketplace
- CRM
- Search engine backend

For each one, write:

- Schema
- Constraints
- Seed data
- 30 queries
- Indexes
- Query plans
- Migration history

## 34. Recommended Learning Order

Follow this order:

1. Basic SQL
2. PostgreSQL setup and `psql`
3. Tables and data types
4. Primary keys and foreign keys
5. Basic `SELECT`
6. Filtering
7. Joins
8. Aggregation
9. Subqueries
10. Constraints
11. Insert, update, and delete
12. Transactions
13. Indexes
14. `EXPLAIN ANALYZE`
15. CTEs
16. Window functions
17. Views and materialized views
18. JSONB
19. Full-text search
20. Migrations
21. Locks and isolation
22. Performance tuning
23. Security and permissions
24. Analytics SQL
25. Partitioning
26. Replication basics
27. Extensions
28. Vector search
29. Production operations
30. Internals

## 35. What Mastered SQL Means

You are not done when you can write `JOIN`.

You are getting strong when you can:

- Design a schema for a real product.
- Explain every constraint.
- Write complex queries without panic.
- Know when to use joins, subqueries, CTEs, or windows.
- Read `EXPLAIN ANALYZE`.
- Create the right index.
- Avoid duplicate rows from bad joins.
- Handle `NULL` correctly.
- Use transactions safely.
- Prevent race conditions.
- Migrate production tables safely.
- Write analytics queries.
- Debug slow queries.
- Know when JSONB is good or bad.
- Know when the database should enforce correctness.
- Understand what the ORM is doing under the hood.

## 36. First 30 Days

Do this exactly:

1. Install PostgreSQL locally.
2. Learn `psql`.
3. Create 20 tables manually.
4. Insert fake data manually.
5. Write 100 SQL queries.
6. Build a blog schema.
7. Build an ecommerce schema.
8. Build a team/membership/roles schema.
9. Learn joins until they are boring.
10. Learn constraints until you stop trusting app-only validation.
11. Learn `EXPLAIN`.
12. Create indexes and observe query plans.
13. Write one article: "What I learned about SQL joins."
14. Build a tiny API backed by PostgreSQL.
15. Use an ORM such as Drizzle only after you can write the SQL yourself.

Your first milestone: you can design a small schema, write useful SQL, and explain why your constraints exist.

## 37. Practice Projects

### Beginner Projects

- Personal notes database
- Todo app schema
- Blog schema
- Library management system
- Simple ecommerce store

### Intermediate Projects

- Team workspace with organizations and memberships
- Role-based permission system
- Chat application
- Subscription billing system
- Job queue using PostgreSQL
- Audit logging system

### Advanced Projects

- Banking ledger
- Inventory reservation system
- Multi-tenant SaaS database
- Analytics event pipeline
- Search system with full-text search
- Vector search document store
- Zero-downtime migration playground
- Visual `EXPLAIN ANALYZE` explorer

## 38. Reading List

Start here:

- PostgreSQL official documentation
- `psql` documentation
- "Practical SQL" by Anthony DeBarros
- "SQL Antipatterns" by Bill Karwin
- "Use The Index, Luke" by Markus Winand
- "The Art of PostgreSQL" by Dimitri Fontaine

Then read:

- "Designing Data-Intensive Applications" by Martin Kleppmann
- "Database Internals" by Alex Petrov
- "Database System Concepts" by Silberschatz, Korth, and Sudarshan
- "Readings in Database Systems" / the Red Book
- PostgreSQL source and architecture notes
- SQLite architecture documentation
- DuckDB engineering posts
- ClickHouse engineering posts

Papers for later:

- Codd's relational model paper
- System R
- ARIES
- Volcano query optimizer
- Dynamo
- Bigtable
- Spanner
- Calvin

## 39. Things To Avoid

Avoid:

- Tutorial hell
- Only learning ORM APIs
- Designing schemas without constraints
- Using `SELECT *` everywhere
- Ignoring `NULL`
- Ignoring time zones
- Adding indexes blindly
- Using JSONB for everything
- Running destructive queries without previewing them
- Treating migrations casually
- Thinking local success means production safety
- Learning distributed databases before basic SQL
- Believing benchmarks without understanding workload

## 40. Final Standard

Start with tables, constraints, joins, aggregation, indexes, and transactions. Those are the core.

Everything advanced becomes easier after that.

You are becoming dangerous, in the good way, when you can:

- Model real-world domains cleanly
- Explain query behavior
- Predict performance problems
- Design constraints that protect correctness
- Use indexes intentionally
- Handle concurrent writes safely
- Migrate schemas without breaking production
- Read ORM-generated SQL critically
- Debug database issues calmly

That is the path.

---

Yes. If you want “master level,” you are not trying to “learn databases.” You are trying to master **data modeling, SQL, query planning, storage engines, transactions, distributed systems, production operations, and developer tooling**.

Brutal truth: most developers are database users. A master is someone who can design schemas, predict query plans, explain latency, fix corrupt mental models, operate production systems, and build tools other engineers trust.

As of July 2026, the practical center of gravity is:

- **PostgreSQL** as the default serious relational database.
- **SQLite** for embedded/local/edge work.
- **DuckDB** for local analytics.
- **ClickHouse** for high-volume analytics.
- **Redis/Valkey** for caching and fast ephemeral data.
- **pgvector/Qdrant/Milvus/Pinecone** for vector search, with pgvector usually being the first choice if you already use Postgres.
- **Drizzle, Prisma, Kysely, SQLAlchemy, jOOQ, sqlc** as important database tooling layers.

If you admire the Drizzle team, understand this: they are not impressive because they know some ORM syntax. They understand **SQL, TypeScript’s type system, migrations, relational modeling, dialect differences, developer experience, and the pain of real applications**.

## The Mastery Stack

You need to master these layers, in this order:

1. **Relational thinking**
   Learn tables, rows, columns, keys, constraints, normalization, relationships, cardinality, and integrity. This is the foundation. If your schema is bad, no ORM or index will save you.

2. **SQL**
   SQL is not optional. Master `SELECT`, joins, grouping, subqueries, CTEs, window functions, constraints, transactions, indexes, views, materialized views, recursive queries, JSON columns, and full-text search.

3. **PostgreSQL**
   Pick Postgres as your main database. Learn it deeply: data types, indexes, `EXPLAIN`, transactions, MVCC, locks, vacuum, WAL, replication, backups, extensions, roles, permissions, partitioning, and performance tuning.

4. **Data modeling**
   Learn how to model users, teams, billing, posts, comments, permissions, ledgers, messages, audit logs, schedules, trees, tags, events, and multi-tenant SaaS systems.

5. **Performance**
   Learn why queries are slow. Study indexes, query plans, statistics, cardinality estimation, I/O, memory, joins, sort cost, cache behavior, and bad ORM-generated SQL.

6. **Transactions and concurrency**
   Learn ACID, isolation levels, MVCC, deadlocks, locks, optimistic concurrency, idempotency, race conditions, and correctness under failure.

7. **Operations**
   Learn backups, restores, migrations, zero-downtime deploys, observability, monitoring, connection pooling, disaster recovery, replication lag, failover, and incident debugging.

8. **Internals**
   Learn how databases store pages, B-trees, WAL, buffer pools, query optimizers, execution engines, LSM trees, column stores, and distributed consensus.

9. **Distributed systems**
   Learn replication, sharding, consensus, CAP, leader/follower, quorum reads/writes, eventual consistency, distributed transactions, Spanner/Cockroach-style systems, and why “scale” is usually a correctness problem first.

10. **Tool building**
    If you want to become like the Drizzle people, build database tools: migration systems, query builders, schema diff tools, type-safe SQL helpers, visual explain tools, seeders, test database harnesses, and schema documentation generators.

## Beginner Roadmap

### Phase 0: Programming Basics

Before databases, you need enough programming skill to build things.

Learn:

- JavaScript/TypeScript or Python.
- Basic terminal usage.
- Git.
- HTTP APIs.
- JSON.
- Basic backend structure.

Build:

- A tiny API with users and posts.
- A script that reads/writes JSON.
- A simple command-line tool.

Do not spend a year here. Get useful, then move.

### Phase 1: Learn SQL Properly

Start with PostgreSQL.

Learn:

- `CREATE TABLE`
- `INSERT`
- `SELECT`
- `UPDATE`
- `DELETE`
- `WHERE`
- `ORDER BY`
- `LIMIT`
- `JOIN`
- `GROUP BY`
- `HAVING`
- `COUNT`, `SUM`, `AVG`
- `NULL`
- primary keys
- foreign keys
- unique constraints
- check constraints

Projects:

- Build a library database: books, authors, borrowers, loans.
- Build a blog database: users, posts, comments, tags.
- Build an ecommerce database: products, carts, orders, payments.

Your goal: be able to model small real systems without guessing.

Read:

- PostgreSQL official tutorial.
- “Practical SQL” by Anthony DeBarros.
- Mode SQL tutorial.
- SQLBolt for quick practice.

### Phase 2: Learn Data Modeling

This is where beginners stay weak.

Learn:

- One-to-one relationships.
- One-to-many relationships.
- Many-to-many relationships.
- Join tables.
- Natural keys vs surrogate keys.
- UUIDs vs integers.
- Normalization: 1NF, 2NF, 3NF.
- Denormalization.
- Soft deletes.
- Audit tables.
- Temporal data.
- Multi-tenancy.
- Permissions and roles.
- Ledger-style immutable records.

Build:

- A team workspace app: users, organizations, memberships, roles.
- A billing system: customers, subscriptions, invoices, payments.
- A messaging system: conversations, participants, messages, read receipts.
- A permissions system: users, roles, permissions, resources.

Brutal rule: if you cannot design the schema for a real app, you are not good at databases yet.

Read:

- “SQL Antipatterns” by Bill Karwin.
- “Database Design for Mere Mortals” by Michael Hernandez.
- PostgreSQL docs on constraints and data types.

### Phase 3: Master PostgreSQL Basics

Now stop treating Postgres as a black box.

Learn:

- `psql`
- schemas
- roles/users
- privileges
- sequences
- generated columns
- enums
- arrays
- JSONB
- timestamps and time zones
- indexes
- views
- materialized views
- triggers
- functions
- extensions
- `COPY`
- `EXPLAIN`
- `EXPLAIN ANALYZE`

Build:

- A dashboard with aggregate queries.
- A search feature using full-text search.
- A JSONB-heavy settings system.
- A materialized view for analytics.
- A migration from bad schema to good schema.

Read:

- PostgreSQL official docs, slowly.
- “The Art of PostgreSQL” by Dimitri Fontaine.
- “Use The Index, Luke” by Markus Winand.

### Phase 4: Indexes and Query Performance

This is where you start separating yourself.

Learn:

- B-tree indexes.
- Composite indexes.
- Partial indexes.
- Unique indexes.
- Covering indexes.
- Expression indexes.
- GIN indexes.
- GiST indexes.
- BRIN indexes.
- Index selectivity.
- Sequential scan vs index scan.
- Bitmap heap scan.
- Nested loop join.
- Hash join.
- Merge join.
- Sorts.
- Statistics.
- Cardinality estimation.

Practice:

- Create a table with 10 million rows.
- Write slow queries.
- Use `EXPLAIN ANALYZE`.
- Add indexes.
- Compare before/after.
- Break performance intentionally.
- Fix it again.

You should be able to look at a query plan and say: “This is slow because the planner estimated 500 rows but got 2 million.”

That is the beginning of real skill.

### Phase 5: Transactions, Locks, and Correctness

Most developers write race conditions without knowing.

Learn:

- ACID.
- MVCC.
- Isolation levels.
- Read committed.
- Repeatable read.
- Serializable.
- Row locks.
- Table locks.
- Deadlocks.
- `SELECT ... FOR UPDATE`.
- Advisory locks.
- Optimistic concurrency.
- Idempotency keys.
- Unique constraints as correctness tools.

Build:

- A bank transfer system.
- A ticket booking system.
- An inventory reservation system.
- A job queue using Postgres.
- A payment webhook processor with idempotency.

Brutal rule: if two users click at the same time and your app breaks, your database knowledge is shallow.

Read:

- PostgreSQL docs on transactions and locking.
- Kleppmann’s “Designing Data-Intensive Applications.”
- Papers later: ARIES, Calvin, Spanner.

### Phase 6: ORMs and Query Builders

Now study Drizzle, Prisma, Kysely, SQLAlchemy, jOOQ, ActiveRecord.

Do not become an ORM-only developer. Become the person who understands what the ORM emits.

For Drizzle specifically, learn:

- Schema definitions.
- Relations.
- Migrations.
- Query builder API.
- Type inference.
- SQL escape hatches.
- Dialect differences.
- Generated SQL.
- How migrations are represented.
- How type-level constraints map to runtime SQL constraints.

Build:

- Same app with raw SQL.
- Same app with Drizzle.
- Same app with Prisma or Kysely.
- Compare generated SQL.
- Compare migration behavior.
- Compare type safety.

If you want to become like the Drizzle team, also master TypeScript:

- Generics.
- Conditional types.
- Mapped types.
- Template literal types.
- Type inference.
- Type-level API design.
- Library ergonomics.

Drizzle-level work is not “database only.” It is **database knowledge plus language design plus developer experience**.

### Phase 7: Production Operations

A real database master knows production pain.

Learn:

- Backups.
- Restores.
- Point-in-time recovery.
- WAL.
- Replication.
- Connection pooling.
- PgBouncer.
- Monitoring.
- Slow query logs.
- `pg_stat_statements`.
- Vacuum.
- Autovacuum.
- Bloat.
- Migration safety.
- Zero-downtime deploys.
- Rollbacks.
- Read replicas.
- Failover.

Practice:

- Break a local Postgres database and restore it.
- Create a replica.
- Simulate a failed migration.
- Recover from backup.
- Add `pg_stat_statements`.
- Find top slow queries.
- Tune them.

Read:

- PostgreSQL docs on backup and restore.
- PgBouncer docs.
- `pg_stat_statements` docs.
- Supabase/Neon/RDS operational docs.

### Phase 8: Database Internals

This is where “master” starts becoming realistic.

Learn:

- Pages.
- Tuples.
- Heap storage.
- B-trees.
- WAL.
- Buffer pool.
- Checkpoints.
- Vacuum.
- MVCC internals.
- Query parser.
- Query planner.
- Query executor.
- Cost model.
- Join algorithms.
- LSM trees.
- Column stores.
- Compression.
- Vector indexes: HNSW, IVF, DiskANN-style ideas.

Read:

- “Database Internals” by Alex Petrov.
- “Architecture of a Database System” paper.
- “Readings in Database Systems” / Red Book.
- PostgreSQL source code, slowly.
- SQLite architecture docs.
- DuckDB papers/blogs.
- ClickHouse docs.

Build:

- A toy key-value store.
- A toy B-tree.
- A toy SQL parser.
- A toy query executor over CSV files.
- A toy WAL.
- A toy LSM tree.
- A tiny migration engine.
- A visual `EXPLAIN` tool.

You do not need to build a production database. You need to build enough toy systems that the real ones stop feeling magical.

### Phase 9: Specialized Databases

After Postgres, learn when not to use Postgres.

Study:

- SQLite: embedded/local/edge.
- DuckDB: local analytics.
- ClickHouse: large-scale analytics.
- Redis/Valkey: cache, queues, ephemeral data.
- Elasticsearch/OpenSearch: search.
- Qdrant/Milvus/Pinecone/pgvector: vector search.
- Cassandra/Scylla: high-write distributed systems.
- FoundationDB: transactional key-value internals.
- CockroachDB/Spanner/Yugabyte: distributed SQL.
- MongoDB: document modeling, but do not worship it.

Your goal is not to memorize products. Your goal is to know the tradeoff:

“Why this storage model? Why this consistency model? Why this query model? Why this failure mode?”

### Phase 10: Become Impossible To Ignore

This part matters.

You need public proof.

Do this for 2-3 years:

- Write deep posts explaining query plans.
- Publish schema design breakdowns.
- Contribute to Drizzle, PostgreSQL docs, DuckDB, SQLite tools, or database libraries.
- Build small but excellent tools.
- Answer hard database questions online.
- Reproduce database bugs.
- Benchmark things honestly.
- Read source code and explain it.
- Make visual explainers.
- Build demos that teach one hard idea clearly.

Examples of impressive projects:

- A visual Postgres `EXPLAIN ANALYZE` explorer.
- A Drizzle migration safety checker.
- A schema diff tool.
- A Postgres index recommendation tool.
- A slow-query playground.
- A multi-tenant SaaS schema template.
- A “database mistakes in real apps” blog series.
- A toy database written from scratch.
- A benchmark suite comparing ORM query output.
- A guide to zero-downtime migrations.

Brutal truth: people do not notice “I studied databases.” They notice artifacts.

## Weekly Study Plan

For the first 6 months:

- 5 days/week: write SQL.
- 3 days/week: read Postgres docs.
- 2 days/week: build backend projects.
- 1 day/week: write a public note explaining what you learned.
- Every week: inspect at least 10 query plans.
- Every month: build one database-heavy mini-project.

For months 6-12:

- Build one serious app with Postgres.
- Add auth, billing, search, background jobs, audit logs, and analytics.
- Use Drizzle or another ORM, but inspect every important query.
- Learn migrations properly.
- Learn transaction correctness.
- Start reading internals.

For year 2:

- Study database internals seriously.
- Build toy storage/query systems.
- Contribute to open source.
- Write technical essays.
- Learn distributed systems.
- Learn analytics databases.

For year 3:

- Specialize.

Pick one:

- PostgreSQL performance engineer.
- Database tooling/ORM engineer.
- Distributed systems/database internals engineer.
- Analytics/OLAP engineer.
- Search/vector database engineer.
- Data platform engineer.

## What To Avoid

Avoid tutorial hell.

Avoid only learning ORMs.

Avoid MongoDB-first thinking as a beginner.

Avoid “I’ll learn distributed databases before SQL.”

Avoid benchmark content from vendors without skepticism.

Avoid thinking indexes are magic.

Avoid building apps without constraints.

Avoid schemas with no foreign keys because “the app handles it.”

Avoid using `created_at` and `updated_at` as a substitute for real data modeling.

Avoid pretending eventual consistency is simple.

Avoid thinking “works locally” means “correct.”

## The Reading List

Start here:

- PostgreSQL official docs.
- “Practical SQL” by Anthony DeBarros.
- “SQL Antipatterns” by Bill Karwin.
- “Use The Index, Luke” by Markus Winand.
- “The Art of PostgreSQL” by Dimitri Fontaine.

Then:

- “Designing Data-Intensive Applications” by Martin Kleppmann.
- “Database Internals” by Alex Petrov.
- “Database System Concepts” by Silberschatz, Korth, Sudarshan.
- “Readings in Database Systems” / the Red Book.
- PostgreSQL source/docs.
- SQLite architecture docs.
- DuckDB and ClickHouse engineering blogs.

Papers later:

- Codd’s relational model paper.
- ARIES.
- System R.
- Volcano query optimizer.
- Spanner.
- Dynamo.
- Calvin.
- Bigtable.
- FoundationDB papers/materials.

## Your First 30 Days

Do this exactly:

1. Install PostgreSQL locally.
2. Learn `psql`.
3. Create 20 tables manually.
4. Insert fake data manually.
5. Write 100 SQL queries.
6. Build a blog schema.
7. Build an ecommerce schema.
8. Build a team/membership/roles schema.
9. Learn joins until they are boring.
10. Learn constraints until you stop trusting app-only validation.
11. Learn `EXPLAIN`.
12. Create indexes and observe query plans.
13. Write one article: “What I learned about SQL joins.”
14. Build a tiny API backed by Postgres.
15. Use Drizzle only after you can write the SQL yourself.

Your first milestone: you can design a small schema, write useful SQL, and explain why your constraints exist.

## Final Brutal Standard

You are getting good when:

- You can model a domain cleanly.
- You can read ugly SQL and improve it.
- You can explain query plans.
- You know when an index helps and when it hurts.
- You understand transaction bugs.
- You can run migrations safely.
- You can recover from backup.
- You know when Postgres is enough.
- You know when Postgres is not enough.
- You can build tools that make other developers better.

That is the path.

Start with Postgres and SQL. Build real schemas. Break them. Fix them. Read the docs. Read internals. Build tools. Write publicly. Do that consistently for years, and you will not need to tell people you are serious. They will see it.
