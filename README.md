# DSA System Design Optimizer

An agent skill for auditing real codebases for practical data structure, algorithm, database, caching, scalability, reliability, and AI-system design improvements.

The skill is designed for working engineers first: it reads the product context, explores the codebase, classifies evidence-backed bottlenecks, and produces a measured roadmap before making changes. It also helps newer builders understand why a data structure, algorithm, or architecture pattern fits a specific product constraint without turning the audit into toy LeetCode advice.

## Install

```bash
npx skills add <owner>/dsa-system-design-optimizer --skill dsa-system-design-optimizer -a claude-code -a codex
```

After this repository is published, replace `<owner>` with the GitHub account or organization name.

## What It Does

- Finds code-level DSA opportunities: maps/sets, heaps, queues, sliding windows, prefix sums, binary search, DP, graph algorithms, batching, and query shape improvements.
- Finds system design opportunities: database indexing, caching, queues, pooling, rate limits, observability, retries, circuit breakers, scaling paths, and rollout plans.
- Reviews AI systems: RAG retrieval, reranking, ACL filtering, evals, agent loops, tool idempotency, LLM serving latency, and cost controls.
- Produces audit-first reports with impact, risk, effort, complexity estimates, trade-offs, and verification plans.

## Layout

```text
dsa-system-design-optimizer/
  SKILL.md
  agents/openai.yaml
  references/
  evals/
```

## Usage Examples

```text
Use $dsa-system-design-optimizer to audit this API for time complexity and query bottlenecks.
```

```text
Use $dsa-system-design-optimizer to review this backend for 10x traffic growth and give me a phased roadmap.
```

```text
Use $dsa-system-design-optimizer to audit this RAG pipeline and agent loop for production readiness.
```

## License

MIT
