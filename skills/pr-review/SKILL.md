---
name: pr-review
description: Reviews a pull request, branch, or uncommitted diff for correctness, missing tests, and risk, and returns findings ranked must-fix / should-fix / nit with file and line references. Use when asked to review a PR, a branch, "my changes", or a diff before merging.
metadata:
  category: review
---

# PR review

Find the problems that matter. Don't rewrite the author's code unless they ask.

## Steps

1. Read the PR description or issue first, then `git diff --stat`, then every changed file. For a large diff, also read callers and tests around the changed functions, not only the hunks.
2. Check correctness: logic errors, empty/null paths, swallowed errors, races, wrong async handling, edge cases (empty, one, many, time zones).
3. Check tests: would a test fail if this change were reverted? Are new branches covered? Are the tests asserting behavior, or just calling code?
4. Check risk: public API or schema changes, migrations, auth, config defaults, new dependencies, logs that might contain secrets.
5. Check fit with this repo's existing patterns (`AGENTS.md`, lint config). Don't flag style the formatter already enforces.
6. Run what you can (tests, typecheck, lint for the touched area) and report the actual result.

## Output

```markdown
**Verdict:** approve | approve with nits | request changes
**Summary:** 2-3 sentences on what the change does and the risk.
**Checks run:** `pnpm test -- billing` pass · `tsc` pass

### Must fix
1. `src/billing/charge.ts:88` What's wrong, why it matters, and a concrete fix.
### Should fix
### Nits (optional)
### Questions for the author
```

## Rules

- Every finding has a location and a reason. "Consider refactoring" with no reason is not a finding.
- Label guesses as guesses, and say how to confirm them.
- If the diff is too large to review properly (roughly 1,000+ lines of non-generated code), say so and suggest how to split it.

## Done when

The verdict, the ranked findings, and the checks you actually ran are in the reply.
