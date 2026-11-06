# Database Career Path

Becoming a “database guy” at the Ben Dicken / Andy Pavlo / Hellerstein level is **not** “get really good at SQL.” It’s closer to becoming a systems engineer whose specialty is data systems: storage, concurrency, recovery, query engines, and distributed consistency — with SQL as the *interface*, not the subject.

Below is the honest map: from “I know SELECT” to “I can reason about (and build/change) a DBMS.”

---

## The honest framing

**SQL fluency** = you can express queries and tune them with EXPLAIN.  
**Database mastery** = you understand *why* the engine does what it does, can predict failure modes under load, can design storage/transaction/query layers, and can read research + source code of real systems.

Rough timeline if you’re serious (already mid-OSTEP helps a lot):
- **Solid practitioner / strong backend DB person:** 1–2 years of deliberate work  
- **Internals-capable (can contribute to Postgres / build a teaching engine / deep blog-level understanding):** 2–4+ years  
- **“One of those guys” (public expertise, research-grade intuition, career identity):** usually a decade of compounding systems + DB work, not a 21-day sprint

21 days can give you a **foundation + direction**. It cannot make you Ben Dicken. Treat 21 days as bootstrapping the multi-year path.

---

## Layer 0 — Prerequisites (you’re already partly here)

Master these or you’ll fake understanding forever:

1. **OS internals (OSTEP is perfect)**
   - Virtual memory, page cache, mmap vs read/write
   - Processes/threads, scheduling, context switches
   - File systems, fsync, durability semantics
   - Locks, atomics, memory ordering (at least the practical bits)
2. **Computer architecture**
   - Cache hierarchy, false sharing, NUMA
   - Disk vs SSD vs NVMe characteristics (latency, IOPS, wear, fsync cost)
   - SIMD basics (modern query engines care)
3. **Data structures & algorithms**
   - B+trees, LSM-trees, hash indexes, skip lists, bloom filters
   - Sorting, hashing, external merge sort
   - Graph basics (for query plans / dependency graphs)
4. **Networking (for distributed DBs later)**
   - TCP, latency vs bandwidth, partial failure
5. **Math you’ll actually need**
   - Probability/stats (selectivity, cardinality estimation)
   - Discrete math / relational algebra basics
   - Some queue theory intuition for consistency proofs (later)

---

## Layer 1 — The relational model (theory that still matters)

People skip this and stay forever “SQL tip” people.

1. Relational model (Codd)
2. Relational algebra & calculus
3. Integrity constraints, keys, FDs
4. Normalization (1NF–BCNF, when to denormalize)
5. Null semantics (three-valued logic) — cruel and important
6. Views, updatable views, materialized views (conceptually)
7. SQL as a declarative language over algebra

**Books/resources:**
- *Database System Concepts* (Silberschatz) — survey
- *Database Management Systems* (Ramakrishnan & Gehrke) — classic
- Date’s writings if you want pedantic correctness

---

## Layer 2 — Storage engines (this is where “DB guys” diverge)

This is the core. If you skip this, you’re not a database person.

### 2.1 Files & pages
- Page layout (slotted pages)
- Record formats (fixed/variable, null bitmaps)
- Free space management
- Buffer pool / buffer manager
  - replacement (LRU, CLOCK, 2Q)
  - pin/unpin, dirty pages
  - interaction with OS page cache (double buffering problem)

### 2.2 Indexes
- B+trees in brutal detail (splits, merges, latch coupling)
- Hash indexes (static/dynamic)
- Covering indexes, INCLUDE columns
- Partial / expression indexes
- GiST/GIN/SP-GiST/BRIN (Postgres world)
- Index-only scans, visibility map

### 2.3 Table organization
- Heap vs clustered index (Postgres vs MySQL/InnoDB difference is career-defining knowledge)
- TOAST / out-of-line storage
- Column stores vs row stores
- Hybrid (HTAP) ideas

### 2.4 LSM vs B-tree world
- Memtable, WAL, SSTables
- Compaction strategies (leveled, tiered, universal)
- Read amplification / write amplification / space amplification
- RocksDB / LevelDB / Cassandra / Scylla mental model

### 2.5 Compression & encoding
- Dictionary, RLE, bit-packing, delta, frame-of-reference
- Why columnar engines crush analytics

**Build project (non-negotiable if you want mastery):**  
A tiny storage engine: pages + buffer pool + B+tree + simple heap file.

---

## Layer 3 — Transactions, concurrency, recovery (the hard core)

This is what separates seniors from “I know isolation levels from a blog.”

### 3.1 Transaction model
- ACID precisely (especially D and I)
- Schedules, conflict serializability
- View serializability (know it exists)
- Recoverability, cascading aborts

### 3.2 Concurrency control
- Two-phase locking (2PL), strict 2PL
- Deadlocks (detection vs prevention)
- Timestamp ordering
- Optimistic concurrency control (OCC)
- Multiversion concurrency control (MVCC) — **study this deeply**
- Snapshot isolation vs serializability
- Write skew, phantoms, read anomalies — with examples you can reproduce
- Predicate locking / next-key locking (InnoDB)
- Latches vs locks

### 3.3 Isolation in the real world
- ANSI isolation levels are incomplete/wrong in places — know the critique
- Postgres: Read Committed, Repeatable Read (SI), Serializable (SSI)
- MySQL InnoDB RR behavior vs Postgres RR
- “What does SELECT FOR UPDATE actually do here?”

### 3.4 Logging & recovery (ARIES family)
- Steal/no-steal, force/no-force
- WAL protocol
- ARIES: analysis, redo, undo
- Checkpoints
- Fuzzy checkpoints
- Crash recovery testing mentality

### 3.5 Durability reality
- fsync, group commit
- torn pages / full-page writes
- replication vs local durability
- “committed” does not always mean what app devs think

**Build project:**  
Transactions on your toy engine: WAL + LOCK/MVCC lite + crash recovery that actually works.

---

## Layer 4 — Query processing & optimization

This is the other half of “DB guy.”

### 4.1 Parsing & rewriting
- SQL parse → AST → logical plan
- Constant folding, predicate pushdown
- View merging, subquery unnesting
- CTE materialization pitfalls

### 4.2 Execution
- Volcano/iterator model
- Vectorized execution
- Compilation / JIT (LLVM era, Firebolt/SingleStore/modern analytical engines)
- Operators: scan, filter, project, nested loop, hash join, merge join, aggregate, sort, window functions
- Parallel query execution (gather/repartition)
- Spill to disk

### 4.3 Cost-based optimization
- Cardinality estimation (the eternal villain)
- Histograms, MCVs, hyperloglog sketches
- Join order optimization (DP, genetic, heuristics)
- Physical property enforcement (sort/order)
- Correlated subqueries
- Why optimizers are wrong and how experts debug them

### 4.4 EXPLAIN mastery (system-specific)
- Reading plans fluently in Postgres *and* one other engine
- Buffer hits, rows estimated vs actual
- Work_mem, hash batches, sorts
- When stats lie

**Build project:**  
SQL subset → logical plan → heuristic/CBO-ish physical plan → execute on your storage engine.

---

## Layer 5 — Vacuum, MVCC garbage, physical maintenance

Especially if you go Postgres-shaped:

1. Tuple visibility (xmin/xmax, snapshots)
2. Vacuum / autovacuum
3. Freezing, xid wraparound (this has taken production down)
4. Bloat, HOT updates
5. Analyze / statistics lifecycle
6. Replication slots + disk blowups
7. Checkpoints, background writer, WAL pressure

MySQL/InnoDB equivalent pain: undo logs, purge, history list length, change buffer, etc.

You are not a DB person until you’ve been burned by one of these in production (or simulated it carefully).

---

## Layer 6 — Distributed databases (where careers often go now)

After single-node mastery:

1. Failures as the normal case
2. Replication
   - leader/follower
   - sync vs async
   - quorum
   - logical vs physical replication
3. Consistency models
   - linearizability, sequential, causal, eventual
   - CAP (as intuition, not religion)
   - PACELC
4. Consensus
   - Paxos / Raft (implement Raft once)
5. Distributed transactions
   - 2PC, 3PC (why 3PC isn’t salvation)
   - Percolator-style
   - Calvin
   - deterministic databases
6. Sharding / partitioning
   - hash/range/composite
   - resharding, rebalancing
   - secondary indexes in a shard world (global vs local)
7. Distributed query execution
   - data locality, shuffle, stage materialization
8. CRDTs / conflict resolution (for some systems)
9. Clock problems
   - NTP, TrueTime, HLC, uncertainty windows

**Systems to study (pick depth, not tourism):**
- Postgres logical replication / Patroni / Citus
- MySQL Group Replication / Vitess
- CockroachDB / Yugabyte
- Spanner papers + model
- Kafka is not a DB but understanding logs is mandatory
- Cassandra/Dynamo papers for the AP side
- Snowflake / BigQuery / DuckDB / ClickHouse for analytical designs

---

## Layer 7 — Specialized data systems (breadth of a modern DB person)

You don’t need all equally, but you need literacy:

1. **OLTP vs OLAP vs HTAP**
2. **Columnar analytical engines** (DuckDB, ClickHouse, Snowflake architecture)
3. **NewSQL / distributed SQL**
4. **Key-value / embedded** (RocksDB, FoundationDB)
5. **Time-series** (compression, out-of-order ingest)
6. **Search / inverted indexes** (Lucene concepts; overlap with GIN)
7. **Graph DB concepts** (at least query patterns + storage tradeoffs)
8. **Vector indexes** (HNSW, IVF) — currently career-relevant
9. **Stream processing vs DB** boundary (Kafka, Flink, materialize-style)

---

## Layer 8 — Operations & real production competence

“Blog internals” without ops is incomplete.

1. Capacity planning & workload characterization
2. Schema design for access patterns (not textbook normalization cosplay)
3. Indexing strategy as engineering, not folklore
4. Lock contention / hotspot diagnosis
5. Slow query archaeology
6. Backup/restore (PITR), backup testing
7. Major version upgrades
8. Connection pooling (PgBouncer transaction vs session)
9. ORMs as query crime scenes
10. Multi-tenant patterns
11. Security: roles, RLS, encryption at rest/in transit, audit
12. Observability: `pg_stat_*`, wait events, metrics that matter
13. Chaos: disk full, failover, replication lag, split brain

---

## Layer 9 — Research literacy (this is the Ben Dicken / Pavlo separator)

Read papers until architecture diagrams feel like native language.

### Canon (non-exhaustive but real)
- System R (history)
- ARIES
- Granularity of locks
- R-trees / GiST lineage
- Dynamo
- Bigtable
- Spanner
- Calvin
- Boulder/LSM literature
- The Case for Deterministic DBs
- Everything about cardinality estimation failures
- MonetDB/X100 / vectorization papers
- Shore-MT / OLTP through the looking glass (H-store path)
- Postgres-specific: HOT, SSI (Cahill), etc.

**Courses worth treating as curriculum:**
- CMU 15-445/645 (Pavlo) — **do the projects**
- CMU 15-721 (Advanced DB)
- Berkeley DB courses / Hellerstein material
- Andy’s YouTube lectures are genuinely world-class

**Books that matter for mastery:**
- *Architecture of a Database System* (Hellerstein, Stonebraker, Hamilton) — short, essential
- *Transactional Information Systems* (Weikum/Vossen) — hardcore txns
- *Designing Data-Intensive Applications* (Kleppmann) — distributed intuition
- Postgres source/docs + *PostgreSQL 14 Internals* (Rogov) if Postgres-path
- *Readings in Database Systems* (Red Book)

---

## Layer 10 — Pick a “home” system and go frighteningly deep

Tourists know 10 databases. Experts own one, and can transfer.

**Recommended primary for career + learning depth:** **PostgreSQL**

Why: open source, readable enough, enormous ecosystem, teaches MVCC/WAL/optimizer pain for real.

What “deep” means for Postgres:
- Heap tuple headers & visibility
- Buffer manager, procarray, snapshots
- WAL records & redo
- Executor nodes
- Planner paths / cost model
- Catalogs
- Extensions / index AMs / table AMs
- Contribute a patch or write tooling that proves you understand it

Secondary system (pick one):
- **InnoDB** (locks/undo different worldview)
- **RocksDB** (embeddable LSM mastery)
- **DuckDB** (modern analytical execution)
- **Cockroach/Yugabyte** (distributed SQL)

---

## Layer 11 — Skills that make you *that guy* publicly/professionally

1. Reproduce weird behaviors with minimal SQL scripts
2. Read `EXPLAIN (ANALYZE, BUFFERS)` like a flight recorder
3. Read C/C++ DB source without fear
4. Write benchmarks that aren’t lies (watch for caching, fsync, concurrency)
5. Explain tradeoffs without slogan thinking (“just use Mongo”, “Postgres can’t scale”)
6. Teach: blogs, talks, notes — Ben Dicken–type people write constantly
7. Build: toy DB, extension, storage engine experiment, or serious open source work

---

## A staged mastery path (practical order)

### Stage A — Foundations (weeks–months)
- Finish OSTEP
- CMU 15-445 lectures + notes
- Implement B+tree + buffer pool
- Kleppmann selected chapters (replication, transactions)

### Stage B — Single-node engine brain (months)
- MVCC + WAL + ARIES-style recovery study
- Build toy txn DB
- Deep Postgres usage: isolation, vacuum, bloat, plans
- Rogov / Postgres internals reading

### Stage C — Optimizer & execution (months)
- Join algorithms + costing
- Break and fix bad plans in real schemas
- Study vectorization / columnar at least once end-to-end

### Stage D — Distributed (months–year)
- Raft implementation
- Replication + partitioning project
- Spanner/Dynamo/Calvin + one distributed SQL system deeply

### Stage E — Professional identity (ongoing)
- Own DB performance/reliability in real systems
- Open source contributions
- Write public technical notes with original experiments
- Optional: grad-level course / research internship / DB company role

---

## Career reality (cruel version)

1. **“Database person” is not one job.** It splits into:
   - DBRE / data platform / SRE for datastores
   - Backend engineer who is the DB expert
   - Query performance specialist
   - DBMS developer / storage engine engineer
   - OLAP engine engineer
   - Research / PhD track  
   These need overlapping but different depths.

2. **SQL alone will not get you there.** Thousands of people know SQL. Few can explain SSI vs SI with bugs, or why a checkpoint stall tanks p99.

3. **You must build and break things.** Reading without implementation plateaus hard.

4. **Production scars matter.** Wraparound, replication lag, bad stats, lock convoys, disk saturation — this is part of mastery.

5. **Math + systems + taste.** The best people combine paper literacy with “what will hurt at 3am.”

6. **21 days:** use them to finish a brutal foundations sprint (below), not to “master databases.”

---

## A ruthless 21-day bootstrap (direction, not mastery)

**Days 1–3:** Relational algebra + physical storage pages/buffer pool  
**Days 4–7:** B+tree implementation + experiments  
**Days 8–11:** WAL + crash recovery readings + tiny durable key-value  
**Days 12–15:** Locks/MVCC/isolation anomalies (hand-reproduce in Postgres)  
**Days 16–18:** Query execution + join algorithms + EXPLAIN deep dive  
**Days 19–21:** Read *Architecture of a Database System* + start CMU 15-445 project path; write a note: “What I thought databases were vs what they are”

If after 21 days you’re addicted, continue the multi-year path. If not, you still become a far better engineer than “knows SQL.”

---

## What to measure (so you know you’re becoming the real thing)

You’re leveling up when you can:
- Implement and explain a B+tree + buffer pool cleanly
- Explain Postgres visibility rules and name real anomalies under each isolation level
- Predict when an index will not help
- Diagnose a bad plan from estimates vs actuals
- Explain WAL, checkpoints, and what fsync buys you
- Compare B-tree vs LSM tradeoffs quantitatively
- Explain how a distributed write becomes “safe”
- Read a DB paper and map it to code/architecture in a real system
- Change a small piece of a real DBMS (or toy clone) and reason about correctness

---

## Bottom line

To become “a database guy,” master this stack in order of leverage:

1. **OS + storage hardware reality**  
2. **Storage engines (pages, indexes, buffer pool, LSM/B-tree)**  
3. **Transactions + MVCC + WAL/recovery**  
4. **Query execution + optimization**  
5. **One system to terrifying depth (Postgres recommended)**  
6. **Distributed systems for databases**  
7. **Production operations + benchmarking honesty**  
8. **Papers + teaching + building in public**

If you want, I can turn this into a **21-day detailed daily syllabus** (with exact lectures, papers, and project milestones), or a **Postgres-specialist track** vs **DBMS-builder track** vs **DBRE/production track** — those three careers share a trunk but diverge hard after Layer 5.

---

# Best resources

Here’s a curated “best of” list — not every resource on the internet. Ordered so you can start now without drowning.

---

## Tier S — Do these first (highest leverage)

If you only did these for a year, you’d already be ahead of most “SQL people.”

| Resource | Type | Why |
|---|---|---|
| [CMU 15-445/645](https://15445.courses.cs.cmu.edu/fall2025/) (Pavlo) + YouTube lectures | Course | Best modern intro to *DBMS internals*. Projects (BusTub) are the real education. |
| [CMU 15-721 Advanced Database Systems](https://15721.courses.cs.cmu.edu/) | Course | Next level: modern OLTP/OLAP/HTAP, papers, systems. |
| [*Designing Data-Intensive Applications*](https://dataintensive.net/) — Martin Kleppmann | Book | Best distributed-data intuition book ever written for engineers. |
| [*Architecture of a Database System*](https://people.eecs.berkeley.edu/~kubitron/courses/cs262a-S16/handouts/papers/fntdb07-architecture.pdf) — Hellerstein, Stonebraker, Hamilton | Long survey | Mental map of how a real DBMS is structured. Read early, reread later. |
| [*PostgreSQL 14 Internals*](https://postgrespro.com/community/books/internals) — Egor Rogov (free PDF) | Book | Best single book for becoming a Postgres-native deep person. |
| Finish **OSTEP** (you’re already on it) | Book | Non-negotiable OS foundation for storage/concurrency/fsync. |

**Rule:** Watch 15-445 + do the projects. Reading alone will lie to you about your skill.

---

## Tier A — Core textbooks (theory + algorithms)

| Resource | Use for |
|---|---|
| *Database System Concepts* — Silberschatz, Korth, Sudarshan | Broad survey textbook (15-445 often uses this) |
| *Database Management Systems* — Ramakrishnan & Gehrke | Classic; excellent on storage, indexing, query processing |
| [*Readings in Database Systems* (Red Book)](http://www.redbook.io/) | Curated classic + modern papers with commentary |
| *Transactional Information Systems* — Weikum & Vossen | Hardcore transactions/recovery. Dense. Worth it later. |
| *Database Internals* — Alex Petrov | Very readable storage + distributed storage overview |

---

## Tier A — SQL / query / indexing (practitioner depth, not “LeetCode SQL”)

| Resource | Why |
|---|---|
| [Use The Index, Luke](https://use-the-index-luke.com/) — Markus Winand | Best free resource on indexes + SQL performance thinking |
| Official [PostgreSQL docs](https://www.postgresql.org/docs/current/) especially chapters on concurrency, WAL, performance tips, `EXPLAIN` | Primary source; treat as a textbook |
| [The Internals of PostgreSQL](https://www.interdb.jp/pg/) — Hironobu Suzuki | Free illustrated Postgres internals |
| [Postgres EXPLAIN visualizer](https://explain.dalibo.com/) / `EXPLAIN (ANALYZE, BUFFERS)` practice | Daily skill |
| *SQL Performance Explained* — Markus Winand | Paid/book form of his index teaching |

Skip endless “50 SQL interview questions” sites. They don’t make database people.

---

## Courses & lecture series (beyond CMU)

| Course | Notes |
|---|---|
| CMU 15-445 YouTube (full playlist) | Watch even if not enrolled |
| CMU 15-721 lectures + paper list | Your research-literacy gym |
| [Berkeley CS186](https://cs186berkeley.net/) materials | Strong undergrad DB course |
| MIT 6.830 / 6.814 (older offerings online) | Classic; still useful historically |
| [Andy Pavlo’s talks / CMU-DB YouTube](https://www.youtube.com/@CMUDB) | Ongoing systems taste |

---

## Papers you should actually read (canon)

Don’t collect PDFs. Read slowly and take notes.

### Foundations / architecture
- **Architecture of a Database System** (Hellerstein et al.) — already above
- **System R** papers (history of relational engines)
- **OLTP Through the Looking Glass** (Harizopoulos / Abadi / Madden / Stonebraker)

### Storage
- **The Log-Structured Merge-Tree** (O’Neil et al.)
- **LSM-tree / RocksDB** design docs + Facebook/Meta RocksDB talks
- **Column-stores vs row-stores** (Stonebraker/Abadi lineage; C-Store, MonetDB/X100)

### Transactions & recovery
- **ARIES: A Transaction Recovery Method…** (Mohan et al.)
- **Serializable Snapshot Isolation** (Cahill / Fekete / Röhm) — critical for Postgres SSI
- Classic papers on **2PL**, **OCC**, **MVCC**

### Distributed
- **Dynamo** (DeCandia et al.)
- **Bigtable** (Chang et al.)
- **Spanner** (Corbett et al.)
- **Calvin** (Thomson et al.)
- **Raft** (Ongaro & Ousterhout) — then implement it
- **The Network is Reliable** / Jepsen analyses (for humility)

### Query engines
- **Volcano** iterator model (Graefe)
- **MonetDB/X100** / vectorized execution papers
- Cardinality estimation failure literature (Leis et al. are gold)

Start from the **Red Book** selections so you’re not guessing what matters.

---

## Postgres specialization track (recommended “home system”)

| Resource | Role |
|---|---|
| Rogov — *PostgreSQL 14 Internals* | Core book |
| [interdb.jp Postgres internals](https://www.interdb.jp/pg/) | Visual companion |
| [Postgres source code](https://github.com/postgres/postgres) + [src/backend README trail](https://github.com/postgres/postgres) | Real mastery path |
| Bruce Momjian talks/slides ([momjian.us](https://momjian.us/)) | Excellent teaching of Postgres guts |
| [Postgres Weekly](https://postgresweekly.com/) | Stay current |
| [PGConf / PGCon talks on YouTube](https://www.youtube.com/@PostgresqlConference) | Practitioner + internals |
| `pg_stat_statements`, `auto_explain`, wait events docs | Production eyes |
| [Patroni](https://patroni.readthedocs.io/) docs | HA reality |
| [PgBouncer](https://www.pgbouncer.org/) docs + Ben Dicken’s writeups | Connection reality |

---

## People / blogs to follow (this is how “those guys” stay sharp)

### Must-read style models
| Person / source | Why |
|---|---|
| [Ben Dicken on PlanetScale](https://planetscale.com/blog/author/ben) | Exactly the modern public-DB-educator style you mentioned |
| Martin Kleppmann (book + talks + papers) | Distributed data clarity |
| Andy Pavlo / CMU-DB | Research + teaching + industry pulse |
| Bruce Momjian | Postgres internals teaching |
| Markus Winand | Indexing/SQL performance truth |
| [Jepsen](https://jepsen.io/) — Kyle Kingsbury | Consistency claims vs reality |
| [Jack Vanlightly](https://jack-vanlightly.com/) | Messaging/log/distributed systems depth |
| [Murat Demirbas](https://muratbuffalo.blogspot.com/) | Distributed systems paper commentary |
| Brandur / [brandur.org](https://brandur.org/) | Excellent Postgres/application correctness writing |
| [Cybertec Postgres blog](https://www.cybertec-postgresql.com/en/blog/) | High-signal Postgres ops/internals |
| [Postgres.fm](https://postgres.fm/) podcast | Ongoing Postgres culture |
| Jeremy Cole’s InnoDB posts (older but legendary) | If you study MySQL side |
| [ClickHouse](https://clickhouse.com/blog) / [DuckDB](https://duckdb.org/news/) blogs | Modern analytical engine thinking |
| CockroachDB / TiDB / Yugabyte engineering blogs | Distributed SQL design tradeoffs |
| [AWS Database Blog](https://aws.amazon.com/blogs/database/) (selectively) | Ops patterns; mixed quality, still useful |

---

## Hands-on projects (resources that force mastery)

| Project / resource | What you learn |
|---|---|
| **BusTub** (CMU 15-445 projects) | Storage, indexes, concurrency, query exec |
| Build a tiny DB from scratch (many follow CMU assignments or “build your own DB” series) | Reality check |
| Implement **Raft** ([Secret Lives of Data Raft viz](https://thesecretlivesofdata.com/raft/) + Ongaro thesis) | Consensus intuition |
| Run Postgres locally + break isolation levels yourself | MVCC scars |
| Cause bloat / wraparound risk in a lab | Ops fear → understanding |
| Read/compile a small part of Postgres or DuckDB | Source courage |
| DuckDB as a second engine | Columnar/vectorized worldview |
| RocksDB examples + tuning guide | LSM worldview |

---

## Distributed systems companions (needed for modern DB careers)

| Resource | Why |
|---|---|
| Kleppmann DDIA (again) | Core |
| [MIT 6.824](https://pdos.csail.mit.edu/6.824/) Distributed Systems | Best practical distributed course |
| Ongaro Raft paper + thesis | Consensus |
| *Database Internals* (Petrov) Part II | Distributed storage |
| Jepsen reports on DBs you care about | Adversarial testing literacy |

---

## Analytical / modern engine track (don’t ignore if you want 2020s relevance)

| Resource | Why |
|---|---|
| DuckDB documentation + papers | Best “learn modern execution” open system |
| ClickHouse docs/architecture articles | Real-world large-scale OLAP |
| Snowflake architecture paper / SIGMOD keynotes | Cloud warehouse design |
| CMU 15-721 paper list (column stores, vectorization, compilation) | Academic backbone |
| *Query Engines* talks by Viktor Leis / Thomas Neumann | Optimizer + execution excellence |

---

## Production / DBRE track resources

| Resource | Why |
|---|---|
| PostgreSQL docs: backup/PITR, replication, vacuum | Primary |
| *PostgreSQL High Availability Cookbook* / similar ops books | Patterns (verify against current versions) |
| Percona blogs (Postgres + MySQL) | Battle scars |
| [PostgreSQL Wiki](https://wiki.postgresql.org/) performance pages | Tuning folklore + truth |
| `pgbench`, `sysbench`, honest benchmarking essays (incl. Ben Dicken’s) | Learn not to lie to yourself |
| SRE books + on-call experience | Databases die in production, not PDFs |

---

## A simple “what to consume in what order”

### Months 1–3
1. Finish OSTEP  
2. CMU 15-445 lectures + notes  
3. Start BusTub projects  
4. Read Hellerstein architecture survey  
5. Use The Index Luke + Postgres `EXPLAIN` drills  

### Months 3–6
1. Rogov *Postgres Internals*  
2. Kleppmann DDIA  
3. ARIES + MVCC/SSI papers  
4. Reproduce isolation anomalies in Postgres  

### Months 6–12
1. CMU 15-721  
2. Raft + MIT 6.824 selected labs  
3. Dynamo/Bigtable/Spanner/Calvin  
4. DuckDB or RocksDB deep dive  
5. Write public notes / benchmarks (Ben Dicken path)

---

## What to *avoid* early (honest)

- Random “Complete SQL Mastery” Udemy-style courses as your main path  
- Only ORM tutorials  
- Vendor marketing as architecture education  
- Memorizing isolation level charts without reproducing anomalies  
- Trying to learn 12 databases at once  

---

## Minimum kit if money/time is tight (all mostly free)

1. CMU 15-445 site + YouTube  
2. OSTEP  
3. Hellerstein architecture PDF  
4. Kleppmann DDIA (book worth buying)  
5. Rogov Postgres Internals PDF  
6. Use The Index Luke  
7. Red Book (redbook.io)  
8. Postgres docs + interdb.jp  
9. PlanetScale / Ben Dicken posts as style + modern practice  

That’s enough to start becoming dangerous.

---

If you want next, I can make a **single reading/watching checklist for your next 21 days** (day-by-day, with exact links and “what done looks like”), or split resources into **Postgres specialist vs DBMS builder vs DBRE** tracks.
