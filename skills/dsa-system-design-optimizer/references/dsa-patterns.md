# DSA Pattern Selection

Use this file when classifying code-level optimization opportunities. Prefer language-native standard libraries over custom data structures unless the codebase already has a trusted implementation.

## Recognition Order

1. Constraints: input size, latency budget, call frequency, memory budget.
2. Shape: sequence, map, tree, graph, stream, range, interval, text, matrix.
3. Operation: lookup, count, rank, search, merge, traverse, update, query range, schedule, optimize.
4. Data properties: sorted, bounded, repeated queries, immutable, streaming, dense, sparse.
5. Pattern: choose the simplest pattern that meets the budget.

## Constraint Table

| Input scale | Usually acceptable | Usually suspicious |
|---|---|---|
| n <= 20 | Backtracking, exhaustive search | Factorial without pruning on hot path |
| n <= 100 | O(n^3) if cold | O(2^n) unless intentional |
| n <= 1,000 | O(n^2) if simple | Nested DB/API calls |
| n <= 100,000 | O(n log n) | O(n^2), repeated sort |
| n <= 1,000,000 | O(n) or O(n log n) | Copy-heavy algorithms, unbounded memory |
| Streaming/unbounded | O(1), O(k), or bounded state | Storing all events by default |

## Pattern Matrix

| Signal | Candidate pattern | Typical result |
|---|---|---|
| Membership checks inside loops | Hash set/map | O(n^2) to O(n) |
| Frequency, grouping, deduplication | Hash map, counter, multimap | O(n^2) to O(n) |
| Pair/triplet over sorted data | Two pointers | O(n^2) to O(n) or O(n^2) for 3-sum |
| Contiguous subarray/string, rolling state | Sliding window | O(n^2) to O(n) |
| Repeated range sum/count | Prefix sum, Fenwick tree, segment tree | O(n) query to O(1) or O(log n) |
| Repeated min/max/top-k | Heap/priority queue | O(n log n) repeated sort to O(n log k) |
| Next greater/smaller, dominated items | Monotonic stack/deque | O(n^2) to O(n) |
| Sorted search or boundary | Binary search | O(n) to O(log n) |
| Minimum/maximum feasible answer | Binary search on answer | O(range * check) to O(log range * check) |
| Overlapping subproblems | Dynamic programming/memoization | Exponential to polynomial |
| Enumerating combinations/permutations | Backtracking with pruning | Correct exhaustive search |
| Unweighted shortest path/levels | BFS | O(V + E) |
| Weighted positive shortest path | Dijkstra | O((V + E) log V) |
| Dependency order | Topological sort | O(V + E) |
| Repeated connectivity merges | Union-find | Near O(1) amortized |
| Prefix lookup/autocomplete | Trie or indexed search | O(length) lookup |
| Interval overlap | Sweep line, interval tree, sorted events | O(n^2) to O(n log n) |

## Codebase Smells

- Linear membership: `contains`, `includes`, `in`, `indexOf`, or manual scans inside loops.
- Queue by shifting/removing first element from an array/list.
- Sorting inside a loop when only min/max/top-k is needed.
- Building the same lookup table repeatedly instead of once per request/job.
- Recursive calls with identical states and no memo/cache.
- Re-querying related rows one at a time.
- Range aggregates recomputed from raw data per query.
- Graph reachability recomputed after each edge merge.

## Real Product Filters

Do not recommend a more complex algorithm unless at least one is true:

- The code is on a hot path.
- The input can realistically grow enough to matter.
- The current implementation causes user-visible latency, cost, memory pressure, or operational risk.
- The proposed change also improves clarity, correctness, or testability.

Prefer "keep simple" when the path is cold, inputs are bounded, and the optimization adds fragile code.
