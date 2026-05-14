---
name: dsa-system-design-optimizer
description: Audits real codebases for data structure, algorithm, database, caching, scalability, reliability, and AI-system design improvements. Use when users ask to reduce time or space complexity, find hot paths, choose DSA patterns, optimize queries, improve architecture, scale a product, review infra, design RAG/agent systems, or create a measured performance roadmap.
---

# DSA and System Design Optimizer

Use this skill to find practical engineering optimizations in a real codebase. Optimize for product impact, maintainability, and measured evidence, not interview cleverness.

Default mode is audit first. Do not edit code until the user chooses a candidate or explicitly asks for implementation.

Serve experienced engineers with concise evidence and trade-offs first. When the user seems newer or asks for learning help, add a short plain-English explanation of the relevant data structure, algorithm, or system pattern after the recommendation.

## Operating Rules

- Ground every recommendation in product behavior, expected scale, and code evidence.
- Prefer simple, boring improvements before complex algorithms or infrastructure.
- Estimate both current and proposed complexity. If complexity is uncertain, say what evidence is missing.
- Treat memory, readability, rollout risk, and tests as first-class constraints.
- If the host supports subagents, delegate independent read-only audits in parallel. If not, inspect locally.
- For destructive, irreversible, or high-blast-radius changes, produce a design/spec first.
- Do not shame simple code. A clear O(n^2) solution may be correct for bounded cold paths.

## First Pass

1. Read project context: `README*`, `CONTEXT.md`, `docs/`, ADRs, architecture diagrams, package manifests, and deployment files.
2. Identify product shape: user workflows, hot paths, data volume, traffic, read/write ratio, latency targets, uptime needs, and team/ops maturity.
3. If context is missing, ask only for the minimum missing scale facts:
   - Product in one sentence
   - Current and target scale
   - The workflow that must stay fast or reliable

## Mode Selection

Use DSA audit when the request mentions Big O, slow functions, memory, loops, sorting, search, graph/tree logic, deduplication, ranking, batching, or hot endpoint internals.

Use system design audit when the request mentions architecture, scaling, databases, caching, queues, reliability, infra, services, RAG, LLM serving, agents, multi-user growth, latency, or availability.

Use both when a slow user workflow likely has architectural and code-level causes.

## DSA Audit Workflow

1. Explore read-only:
   - Hot paths: endpoints, jobs, render paths, CLI commands, query handlers.
   - Complexity smells: nested loops, repeated scans, repeated sorts, recursion without memoization, queue operations with shifting arrays, N+1 queries, repeated DB/API calls.
   - Data structure smells: lists used for membership, ad hoc deduplication, rebuilt indexes, missing maps/sets, repeated range sums, brute-force graph traversals.
2. Classify candidates using [references/dsa-patterns.md](references/dsa-patterns.md) and [references/dsa-complexity.md](references/dsa-complexity.md).
3. Present findings using [references/audit-templates.md](references/audit-templates.md).
4. Before implementation, run the grilling loop in [references/grilling.md](references/grilling.md).
5. Implement only after selection. Add or update tests first when practical, then measure or benchmark with realistic input sizes.

## System Design Audit Workflow

1. Explore read-only:
   - Entry points, service/module boundaries, data stores, queues, caches, infra, deployment topology, observability, background jobs, third-party dependencies.
   - Scale smells: sync chains on user requests, DB bottlenecks, missing indexes, no pooling, no cache invalidation plan, shared mutable state, single points of failure, no rate limits, weak observability.
   - AI-system smells: RAG without evals, no ACL filtering before retrieval, slow retrieval, missing reranking, unbounded agent loops, non-idempotent tools, weak prompt-injection defenses.
2. Classify candidates using [references/system-design-patterns.md](references/system-design-patterns.md).
3. Present roadmap using [references/audit-templates.md](references/audit-templates.md).
4. Run the grilling loop in [references/grilling.md](references/grilling.md) before proposing migrations or infra changes.
5. For code-level improvements, implement and verify. For architecture changes, write a spec/ADR with rollout, rollback, metrics, and trade-offs.

## Parallel Audit Prompts

When subagents are available, use short, read-only prompts like these:

- Hot path auditor: "Find likely hot paths and complexity smells. Report file/function, evidence, current complexity guess, and risk. Do not edit."
- Data structure auditor: "Find places where arrays/lists, maps, sets, heaps, queues, indexes, or graph structures are missing or misused. Do not edit."
- Data layer auditor: "Find ORM/query inefficiencies, N+1 patterns, missing indexes, connection pooling issues, and cache candidates. Do not edit."
- Reliability auditor: "Find sync dependency chains, retries/timeouts/circuit breaker gaps, rate-limit gaps, single points of failure, and observability gaps. Do not edit."
- AI systems auditor: "If the product uses LLMs/RAG/agents, inspect retrieval, evals, tool safety, memory, budgets, and latency risks. Do not edit."

Merge reports, remove duplicates, and rank by user impact.

## Evidence Standard

Every recommendation should include:

- File/component evidence
- Current behavior and estimated cost
- Proposed data structure, algorithm, or system pattern
- Expected complexity or reliability improvement
- Trade-offs and failure modes
- Verification plan: tests, benchmark, load test, metric, or rollout signal

If evidence is weak, label it as a hypothesis and explain how to validate it.

## Reference Guide

- DSA pattern selection: [references/dsa-patterns.md](references/dsa-patterns.md)
- Complexity and language gotchas: [references/dsa-complexity.md](references/dsa-complexity.md)
- System design patterns: [references/system-design-patterns.md](references/system-design-patterns.md)
- Grilling questions: [references/grilling.md](references/grilling.md)
- Report templates: [references/audit-templates.md](references/audit-templates.md)
