# DSA System Design Optimizer

[![skills.sh](https://skills.sh/b/perfect7613/dsa-system-design-optimizer)](https://skills.sh/perfect7613/dsa-system-design-optimizer)

An agent skill for auditing real codebases for practical data structure, algorithm, database, caching, scalability, reliability, and AI-system design improvements.

The skill is designed for working engineers first: it reads the product context, explores the codebase, classifies evidence-backed bottlenecks, and produces a measured roadmap before making changes. It can also explain why a data structure, algorithm, or architecture pattern fits a specific product constraint while keeping recommendations grounded in production trade-offs.

## Install

Universal install with the `skills` CLI:

```bash
npx skills add perfect7613/dsa-system-design-optimizer
```

This lets the CLI detect your current agent and install the skill into the right local skill directory.

Preview available skills without installing:

```bash
npx skills add perfect7613/dsa-system-design-optimizer --list
```

Install only this skill by name:

```bash
npx skills add perfect7613/dsa-system-design-optimizer --skill dsa-system-design-optimizer
```

Install into every supported agent directory detected by the CLI:

```bash
npx skills add perfect7613/dsa-system-design-optimizer --skill dsa-system-design-optimizer --agent '*'
```

Opt out of anonymous install telemetry:

```bash
DISABLE_TELEMETRY=1 npx skills add perfect7613/dsa-system-design-optimizer
```

The package follows the shared Agent Skills `SKILL.md` convention and uses the standard `skills/dsa-system-design-optimizer/` repository layout, so any agent or editor supported by `npx skills` can install it from this repository.

## What It Does

- Finds code-level DSA opportunities: maps/sets, heaps, queues, sliding windows, prefix sums, binary search, DP, graph algorithms, batching, and query shape improvements.
- Finds system design opportunities: database indexing, caching, queues, pooling, rate limits, observability, retries, circuit breakers, scaling paths, and rollout plans.
- Reviews AI systems: RAG retrieval, reranking, ACL filtering, evals, agent loops, tool idempotency, LLM serving latency, and cost controls.
- Produces audit-first reports with impact, risk, effort, complexity estimates, trade-offs, and verification plans.
- Stays language- and vendor-neutral unless the target codebase or user request calls for specific technologies.

## Layout

```text
skills/
  dsa-system-design-optimizer/
    SKILL.md
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
