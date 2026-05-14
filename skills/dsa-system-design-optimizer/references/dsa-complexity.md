# Complexity and Hidden Costs

Use this file to sanity-check complexity claims across any implementation language. Focus on access patterns and cost models, not language-specific syntax.

## Complexity Checklist

- Define `n`, `m`, `k`, `V`, `E`, or other size variables explicitly.
- Estimate realistic current and 12-month maximum input sizes.
- Separate one-time build cost from per-query cost.
- Include IO cost when database, network, filesystem, or model calls happen inside loops.
- Include space complexity and cache/index invalidation cost.
- Compare the optimized version against the simplest correct baseline.

## Common Hidden Costs

| Smell | Why it hurts | Pattern to consider |
|---|---|---|
| Linear lookup inside loop | O(n^2) | Precomputed membership index |
| Removing the first item repeatedly | O(n^2) in contiguous arrays/lists | Queue, deque, ring buffer, or head index |
| Sort inside loop | O(n^2 log n) or worse | Sort once, heap, balanced tree, or incremental order maintenance |
| Repeated string/buffer growth | Repeated copying | Accumulate chunks, then join/flush once |
| Slice/copy in recursion | Hidden O(n) per call | Pass indexes, views, cursors, or immutable shared state |
| Repeated encode/decode | CPU and allocation cost | Parse once, stream, or keep a typed/intermediate representation |
| Lazy related-data access in loop | N+1 queries/calls | Batch, prefetch, join, or create a read model |
| Unbounded cache | Memory leak | Bounded LRU/LFU/TTL |

## Complexity Output Format

Use this style in findings:

```text
Current: O(users * roles) time, O(1) extra space, plus one data fetch per user.
Proposed: O(users + roles) time, O(roles) extra space, one batched query.
Why it matters: this endpoint runs per page load and user count is expected to reach 100k.
```

If the current complexity depends on implementation details, say so:

```text
Likely current cost: O(n^2) because membership uses a linear list scan inside the outer loop. Confirm by checking input cardinality in production logs or adding a microbenchmark.
```
