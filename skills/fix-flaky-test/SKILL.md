---
name: fix-flaky-test
description: Diagnoses a test that passes and fails intermittently by reproducing the flake with repeated runs, finding the nondeterminism, and fixing the cause instead of adding retries. Use when a test is flaky, fails only in CI, or passes locally but not in the pipeline.
metadata:
  category: testing
---

# Fix a flaky test

A flake is a bug in the test or the code. Find the nondeterminism and remove it.

## Steps

1. Reproduce. Run the single test many times and record the failure count, for example:
   `for i in $(seq 1 50); do <test command> || echo FAIL $i; done`
   If it never fails alone, run it with its neighbors, in random order, in parallel, or under CPU load. CI is usually slower and more parallel than a laptop.
2. Read one failing run in full: which assertion, what values, any timing in the logs.
3. Check the usual causes, in this order:
   - Async / timing: missing awaits, sleeps, polling with no deadline
   - Shared state: globals, module caches, leftover DB rows or files, env vars
   - Order: passes only before or after another test
   - Time: `now()`, midnight or month-end, time zones, tight timeouts
   - Randomness: unseeded RNG, hash/set iteration order, queries with no `ORDER BY`
   - External or resource limits: real network, ports in use, shared temp dirs
4. Form one hypothesis and test it (freeze the clock, seed the RNG, force the order) until the failure is 100% or 0%.
5. Fix the cause, not the symptom. Then re-run the same loop and report before/after counts.

## Not a fix

- Retries, longer sleeps, or bigger timeouts with no tie to the cause
- Skipping or deleting the test (ask first if a temporary quarantine is truly needed)

## Output

```markdown
**Test:** `tests/test_export.py::test_csv_order`
**Before:** 7/50 failures   **After:** 0/50
**Cause:** query had no ORDER BY; row order changed under parallel load.
**Fix:** added .order_by("id") in the export query (code bug, not only the test).
```

## Stop and ask when

You can't reproduce it after isolation, order, parallelism, and load. Report what you tried and what logging to add in CI.
