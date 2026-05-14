# Complexity and Language Gotchas

Use this file to sanity-check complexity claims across languages. Complexity can hide in library calls, allocation, serialization, ORM access, and network boundaries.

## Complexity Checklist

- Define `n`, `m`, `k`, `V`, `E`, or other size variables explicitly.
- Estimate realistic current and 12-month maximum input sizes.
- Separate one-time build cost from per-query cost.
- Include IO cost when DB/API calls happen inside loops.
- Include space complexity and cache/index invalidation cost.
- Compare the optimized version against the simplest correct baseline.

## Common Hidden Costs

| Smell | Why it hurts | Better default |
|---|---|---|
| Linear lookup inside loop | O(n^2) | Build set/map once |
| Shift/remove first element repeatedly | O(n^2) in many arrays/lists | Deque/ring buffer/queue |
| Sort inside loop | O(n^2 log n) or worse | Sort once, heap, or balanced tree |
| String concat in loop | Repeated copying | Builder/list join/buffer |
| Slice/copy in recursion | Hidden O(n) per call | Pass indexes/views |
| Repeated JSON encode/decode | CPU and allocation cost | Parse once, stream, typed structure |
| ORM lazy relation in loop | N+1 queries | Batch query, eager load, join |
| Unbounded cache | Memory leak | Bounded LRU/LFU/TTL |

## Language Notes

| Language | Watch for | Prefer |
|---|---|---|
| Python | `x in list`, `list.pop(0)`, slicing in loops, string concat | `set`, `dict`, `collections.deque`, `''.join`, `heapq`, `bisect` |
| JavaScript/TypeScript | `array.includes` in loops, `shift`, object key pitfalls, repeated spreads | `Set`, `Map`, index pointers, typed arrays, priority queue library |
| Java | `List.contains`, `ArrayList.remove(0)`, boxing overhead, comparator sorts in loops | `HashSet`, `HashMap`, `ArrayDeque`, `PriorityQueue`, primitive collections when needed |
| Go | Repeated slice scans, front removal with reslice retaining backing array, string concat | `map[T]bool`, head index queue, `strings.Builder`, heap interface |
| Rust | Unnecessary clones, repeated allocation, `Vec::remove(0)` | `HashMap`, `HashSet`, `VecDeque`, iterators with references |
| SQL/ORM | N+1 queries, missing indexes, offset pagination at scale | joins, eager loading, composite indexes, keyset pagination |

## Complexity Output Format

Use this style in findings:

```text
Current: O(users * roles) time, O(1) extra space, plus one DB query per user.
Proposed: O(users + roles) time, O(roles) extra space, one batched query.
Why it matters: this endpoint runs per page load and user count is expected to reach 100k.
```

If the current complexity depends on implementation details, say so:

```text
Likely current cost: O(n^2) because membership uses a linear list scan inside the outer loop. Confirm by checking input cardinality in production logs or adding a microbenchmark.
```
