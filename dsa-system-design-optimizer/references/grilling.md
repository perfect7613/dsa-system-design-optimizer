# Grilling Questions

Ask questions one at a time. Provide your recommended default when the codebase gives enough evidence. Do not make the user answer questions that can be answered by reading code or docs.

## DSA Decision Questions

1. What is the real maximum `n`, and is this path hot or cold?
2. What latency or memory budget does this path need to meet?
3. Does the input have useful properties: sorted, bounded, immutable, repeated, sparse, streaming?
4. Are queries repeated enough to justify precomputation, an index, memoization, or a cache?
5. Does the proposed data structure change external behavior: ordering, serialization, equality, duplicates?
6. What edge cases matter: empty input, duplicates, negative numbers, overflow, cycles, disconnected graph, stale cache?
7. What tests or benchmarks will prove correctness and performance?
8. Is the complexity gain worth the readability and maintenance cost?

## System Design Questions

1. What is current traffic and 12-month target traffic?
2. What is the read/write ratio?
3. What consistency does the workflow need: strong, read-your-writes, bounded stale, or eventual?
4. What is the failure mode we most need to prevent: data loss, downtime, breach, cost runaway, wrong answer, slow answer?
5. What does the team know how to operate at 3am?
6. Is this a quick fix, a migration, or a new design?
7. How will we roll out and roll back?
8. Which metric proves the change worked: p95/p99 latency, error rate, DB CPU, queue depth, cache hit rate, retrieval quality, cost?

## AI System Questions

1. What should the system optimize for: factuality, latency, cost, privacy, recall, or controllability?
2. What data is allowed to enter the prompt?
3. How are retrieval quality and answer quality evaluated?
4. What happens when retrieval returns nothing, conflicting sources, stale sources, or unauthorized sources?
5. Which tools can cause side effects, and are they idempotent?
6. What budget stops runaway loops or runaway token spend?

## Keep Simple Rule

If the user cannot provide scale numbers and the codebase has no evidence of pain, recommend instrumentation or a low-risk measurement step before major redesign.
