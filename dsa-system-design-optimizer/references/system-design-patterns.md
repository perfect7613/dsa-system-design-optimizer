# System Design Patterns

Use this file when classifying architecture, database, infrastructure, reliability, and AI-system findings. Recommend the lowest-complexity pattern that meets the product's current and near-future requirements.

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
| App servers hold session state | Stateless app tier | Move state to Redis/DB; put app behind load balancer |
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
| Relational data, transactions | Postgres/MySQL | Start here for most products |
| Missing filter/join index | Indexing | Add measured, query-backed indexes |
| High connection overhead | Connection pooling | PgBouncer or ORM pool tuning |
| N+1 query | Batch/eager load | Query related records once |
| Offset pagination slow | Keyset pagination | Use stable cursor over indexed column |
| Read and analytics compete | OLTP/OLAP split or CQRS | Add read model/warehouse when measured |
| Time-series growth | Partitioning/time-series DB | Partition before sharding |
| Semantic/vector search | pgvector first, managed vector DB later | Use metadata filters before ANN search |
| Single DB write ceiling reached | Sharding | Last resort after indexes, pooling, cache, replicas, partitioning |

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
