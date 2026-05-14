# Audit Templates

Use these templates for consistent output. Keep findings concise and evidence-backed.

## DSA Finding

```markdown
1. [Impact/Risk/Effort] Short title
   Location: `path/to/file.ext:function`
   Evidence: what code pattern or runtime signal points to the issue
   Current cost: O(?) time, O(?) space, plus IO if relevant
   Proposed change: data structure or algorithm
   Expected cost: O(?) time, O(?) space
   Trade-offs: memory, readability, ordering, invalidation, rollout risk
   Verification: test/benchmark/metric to prove it
```

## System Design Finding

```markdown
1. [Impact/Risk/Effort] Short title
   Component: service/module/database/job
   Evidence: code/docs/config signal
   Pattern: named system design pattern
   Proposed change: concrete design step
   Why now: current or projected scale reason
   Trade-offs: consistency, ops burden, cost, failure modes
   Rollout: low-risk sequence and rollback point
   Measure: metric that should improve
```

## Scoring

Impact:
- High: affects hot user path, reliability, correctness, cloud cost, or a real scale target.
- Medium: affects important internal path or projected near-term growth.
- Low: local cleanup, cold path, or speculative improvement.

Risk:
- High: data migration, cross-service behavior, security, payments, auth, irreversible side effects.
- Medium: shared module or behavior change with tests.
- Low: isolated function, additive index, config, or well-covered code.

Effort:
- Hours: local code/index/config with clear tests.
- Days: moderate refactor, cache with invalidation, new background job.
- Weeks: architecture migration, new data store, service split.

## Roadmap Shape

Group final recommendations by:

- Quick wins: hours to days, low risk, measurable.
- Structural improvements: days to weeks, needs design and rollout.
- Strategic bets: weeks/months, only if scale or product direction justifies it.

## Implementation Closeout

When implementing a chosen change, close with:

```markdown
Changed:
- Files and behavior changed

Verified:
- Tests, benchmarks, or checks run

Measured/Expected:
- Before/after numbers if available, otherwise expected complexity change

Residual risk:
- Anything not fully proven
```
