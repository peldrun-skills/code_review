---
name: correctness_analysis
description: Identify logic bugs, edge cases, and incorrect control flow with cited evidence.
---

# Correctness Analysis

You are reviewing code for **correctness**: does it do what it claims
to do, for every input it will see in production?

## Checklist

1. **Off-by-one errors** — loop bounds, array indices, pagination
   windows, inclusive vs exclusive ranges.
2. **Null / undefined / None handling** — is every optional value
   checked before use?
3. **Edge cases** — empty inputs, single-element collections, zero-length
   strings, negative numbers, integer overflow.
4. **Race conditions** — concurrent writes, check-then-act on shared
   state, missing locks.
5. **Resource leaks** — files, sockets, DB connections, locks —
   released on EVERY path including exceptions?
6. **Type confusion** — implicit conversions that lose precision or
   change semantics (e.g. `"1" + 1`).
7. **Wrong operator** — `=` vs `==`, `&` vs `&&`, `is` vs `==` on
   non-singletons, `+` on mixed types.
8. **Integer vs float** — division that should be integer, or vice
   versa.

## Method

For each suspected bug:

1. Read the surrounding function end-to-end.
2. Trace the concrete input that would trigger the bug.
3. Point at the exact line that produces the wrong result.
4. If you cannot construct a concrete failure case, downgrade to
   "warning" or drop the finding entirely.

## Output Format

    ## [Severity] <Short title>
    **Location**: `path/to/file.py:42`
    **Evidence**:

```
<quoted code>
```

    **Impact**: <what breaks, under what conditions>
    **Fix**:

```
<minimal patch>
```

## Rules

- Report only bugs you can **demonstrate** with a concrete input.
- One bug per block. Do not bundle multiple issues.
- If the fix is non-trivial (requires refactoring), say so and
  explain the tradeoff rather than presenting a large diff.
- Prefer the smallest patch that fixes the root cause, not the symptom.
