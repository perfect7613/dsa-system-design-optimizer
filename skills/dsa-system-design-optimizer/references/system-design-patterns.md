# System Design Patterns

Use this file when classifying architecture, database, infrastructure, reliability, and AI-system findings. Recommend the lowest-complexity pattern that meets the product's current and near-future requirements.

Stay vendor-neutral. Name concrete products only when the codebase already uses them, the user asks for product selection, or a comparison is explicitly needed.

## Scale Discovery

Before proposing architecture changes, identify:

- Current and target RPS/concurrency
- Data size and growth rate
- Read/write ratio
- Latency SLO, availability SLO, and critical failure modes
- Consistency requirement: strong, read-your-writes, bounded stale, eventual
- Team size and operational experience

## Backend and Scalability Patterns

| Signal | Pattern | Default guidance |
|---|---|---|
| App servers hold session state | Stateless app tier | Move state to a shared session store; put app instances behind traffic distribution |
| Slow user request waits for noncritical work | Async queue/workers | Queue emails, media, analytics, webhooks, reports |
| DB read pressure | Cache-aside, read replica | Cache hot reads first, add replica when read load persists |
| Same expensive value recomputed | L1/L2 cache | Define invalidation before adding cache |
| Thundering herd on cache miss | Stampede protection | Single-flight lock, jittered TTL, stale-while-revalidate |
| Single server or single AZ | Redundancy | At least two instances/AZs for user-critical systems |
| Traffic spikes | Autoscaling | Use target tracking and readiness probes; prewarm for sharp spikes |
| Static/global assets slow | CDN | Immutable cache-busted assets; cautious dynamic caching |
| Abuse or accidental overload | Rate limiting | Token bucket or sliding window by user/API key/IP |

## Database and Storage Patterns

| Signal | Pattern | Default guidance |
|---|---|---|
| Relational data, transactions | Relational database | Start here for most products |
| Missing filter/join index | Indexing | Add measured, query-backed indexes |
| High connection overhead | Connection pooling | Tune client/server pooling and connection lifetimes |
| N+1 query | Batch/eager load | Query related records once |
| Offset pagination slow | Keyset pagination | Use stable cursor over indexed column |
| Read and analytics compete | OLTP/OLAP split or CQRS | Add read model/warehouse when measured |
| Time-series growth | Partitioning/time-series DB | Partition before sharding |
| Semantic/vector search | Vector index/store | Use metadata filters before ANN search |
| Single DB write ceiling reached | Sharding | Last resort after indexes, pooling, cache, replicas, partitioning |

## Database Internals Audit

Use this when the bottleneck is not just "add an index" but how data is stored, read, written, replicated, locked, and recovered.

### Workload Shape

| Question | Why it matters |
|---|---|
| Is the path point lookup, range scan, join-heavy, aggregate-heavy, write-heavy, or mixed? | The right index, storage layout, and cache strategy depends on access shape. |
| Is the workload transactional or analytical? | Transactional paths optimize low-latency reads/writes; analytical paths optimize scans, aggregations, and compression. |
| Are reads p50-fast but p99-slow? | Tail latency often points to lock waits, cache misses, bad plans, noisy neighbors, replication lag, or queueing. |
| Is data naturally tenant-, time-, region-, or entity-scoped? | Good locality reduces cross-partition queries and makes failover/backfill safer. |

### Index and Query Planning Checks

| Signal | Pattern | Default guidance |
|---|---|---|
| Slow filter/sort/join on large relation | Access-path audit | Check selectivity, cardinality estimates, predicate order, join order, and whether the plan scans too much data. |
| Query returns few rows but reads many | Covering/composite index candidate | Align index order with equality filters, range filters, sort, and projection. |
| Index speeds reads but writes slow down | Index write-cost trade-off | Keep indexes tied to measured query patterns; remove unused or duplicate indexes. |
| Partitioned data has inconsistent performance | Local/global index decision | Local indexes simplify partition operations; global indexes help cross-partition lookup but complicate writes and maintenance. |
| Joins dominate runtime | Join strategy audit | Consider whether nested-loop, hash, or merge-style execution fits relation sizes, ordering, memory, and indexes. |
| Cardinality estimates are wrong | Statistics/data distribution audit | Refresh or improve stats, account for skew, and validate with production-like distributions. |

### Storage Engine and IO Checks

| Signal | Pattern | Default guidance |
|---|---|---|
| Read-heavy range access | Tree-structured index/layout | Favor locality for ordered reads and predictable range scans. |
| Write-heavy ingest with later compaction | Log-structured layout | Watch read amplification, compaction pressure, write amplification, and space amplification. |
| Writes must survive crashes | Write-ahead logging / commit log | Verify fsync/durability settings, recovery behavior, and acknowledged-write semantics. |
| Hot data repeatedly read from disk | Buffer/cache working-set audit | Compare working set to memory, cache hit rate, eviction behavior, and scan pollution. |
| Bulk load or backfill is slow | Bulk ingestion path | Prefer ordered batches, disabled/rebuilt secondary structures where safe, and throttled backfills. |
| Existence checks cause expensive reads | Probabilistic prefilter | Bloom-style filters can avoid unnecessary negative lookups when false positives are acceptable. |

### Transactions, Isolation, and Concurrency

| Signal | Pattern | Default guidance |
|---|---|---|
| Occasional duplicate, missing, or stale behavior | Isolation audit | Identify whether the workflow needs atomicity, read-your-writes, monotonic reads, or stronger serial behavior. |
| Deadlocks or lock waits | Lock-order and transaction-scope audit | Keep transactions short, access rows in consistent order, and move noncritical work outside the transaction. |
| Retry creates duplicate side effects | Idempotency boundary | Add idempotency keys or conflict-safe writes around retried transactions. |
| Read path sees stale data after write | Consistency contract | Route critical reads to fresh data or make staleness visible and bounded. |

### Replication, Partitioning, and Consensus

| Signal | Pattern | Default guidance |
|---|---|---|
| Replica reads are stale | Replication lag budget | Define which workflows tolerate lag and which need read-your-writes. |
| Failover is manual or untested | Failover drill | Test leader failure, replica promotion, client reconnect behavior, and data-loss window. |
| Multi-region writes conflict | Conflict-resolution design | Choose single-writer, quorum, versioning, merge rules, or explicit compensation based on invariants. |
| Shard/partition hotspots | Shard-key audit | Prefer high-cardinality, evenly distributed keys that preserve common query locality. |
| Cross-shard queries dominate | Query-locality redesign | Revisit partition key, denormalized read models, or async aggregation. |
| Strong coordination is required | Consensus boundary | Use consensus only for values that truly need linearizable agreement; keep the consensus surface small. |

### Benchmarking and Measurement

| Signal | Pattern | Default guidance |
|---|---|---|
| Average latency looks fine but users complain | Tail-latency benchmark | Track p50, p95, p99, max, and timeout/error rate separately. |
| Benchmark does not match production | Representative workload | Use realistic cardinality, skew, data size, concurrency, cache warmth, and transaction mix. |
| Change improves one metric but hurts another | RUM-style trade-off | Evaluate read cost, update/write cost, and memory/storage overhead together. |
| Migration risk is unclear | Reversible rollout | Backfill in chunks, dual-read/dual-write only with verification, and define rollback before rollout. |

## Reliability Patterns

| Signal | Pattern | Default guidance |
|---|---|---|
| Outbound calls can hang | Timeouts | Every network call gets a timeout |
| Transient downstream failures | Retry with backoff and jitter | Retry only idempotent operations |
| Persistent downstream failure cascades | Circuit breaker | Fail fast with fallback or clear error |
| One dependency consumes all workers | Bulkhead | Separate pools per dependency |
| Async failures disappear | Dead-letter queue | Preserve failed messages for replay/inspection |
| Duplicate messages cause double effects | Idempotency key | Required for payments, emails, imports, webhooks |
| Hard to debug incidents | Observability | Metrics, structured logs, traces, correlation IDs |
| Backup confidence unknown | Restore drill | A backup is not proven until restored |

## Architecture Style Decision

| Context | Prefer |
|---|---|
| Small team, early product | Modular monolith |
| One module has distinct scale needs | Extract one service or isolate module |
| Many teams need independent deploys | Microservices with owned data |
| Cross-service workflow needs consistency | Saga plus idempotent steps |
| Reads and writes need different models | CQRS with explicit lag handling |
| Migration from monolith | Strangler pattern |

## AI-System Patterns

| Signal | Pattern | Default guidance |
|---|---|---|
| RAG retrieves irrelevant chunks | Hybrid search plus reranking | Combine vector and keyword; measure recall |
| RAG leaks private content | ACL filtering before retrieval | LLM must never see unauthorized chunks |
| RAG hallucinates despite context | Grounding/eval loop | Evaluate faithfulness, context precision/recall |
| Retrieval slow at scale | ANN index, metadata prefilter, cache | Measure p95 retrieval separately |
| Documents update often | Incremental indexing | Track source version and stale chunks |
| Agent loops too long | Iteration and budget guards | Hard caps on steps, tokens, spend |
| Agent tools duplicate side effects | Idempotent tools and approval gates | Human confirmation for irreversible actions |
| Prompt injection risk | Tool/data trust boundaries | Treat retrieved/user text as untrusted |
| LLM serving expensive/slow | Routing, batching, caching | Route by task complexity; monitor TTFT and token latency |

## Recommendation Ladder

Use this escalation order unless evidence says otherwise:

1. Measure and observe.
2. Fix code/query/index issues.
3. Add pooling, batching, and simple caching.
4. Add async work queues and backpressure.
5. Add replicas, CDN, rate limits, and autoscaling.
6. Split read/write models or services where justified.
7. Shard, multi-region, service mesh, or major rewrites only with clear scale and ops justification.
