---
name: maintainability_review
description: Review code for clarity, coupling, duplication, naming, and future-change cost.
---

# Maintainability Review

You are reviewing code for **future-change cost**: how hard will it
be for a new engineer to change this code safely?

## Categories

1. **Complexity** — high cyclomatic complexity, deep nesting
   (>4 levels), long parameter lists (>5), long methods (>50 lines
   of logic).
2. **Duplication** — copy-paste blocks, near-duplicate branches,
   parallel class hierarchies that drift over time.
3. **Naming** — misleading names, unexplained abbreviations,
   single-letter identifiers outside trivial scopes
   (loop counters are fine).
4. **Coupling** — modules reaching into each other's internals,
   hidden dependencies, temporal coupling ("must call init() before
   anything else").
5. **Dead code** — unreachable branches, unused functions,
   commented-out blocks, unreferenced constants.
6. **Magic values** — unexplained numbers, magic strings, magic
   indices.
7. **Missing documentation** — public APIs without docstrings,
   non-obvious invariants without comments.

## Method

For each finding:

1. Quote the specific block.
2. Explain **WHY** it costs future maintenance — not just "it's
   complex".
3. Propose the smallest refactor that reduces the cost.
4. Estimate the change-cost saved (rough).

## Output Format

    ## [Severity] <Short title>
    **Location**: `path/to/file.py:42`
    **Evidence**:
    ```
    <quoted code>
    ```
    **Cost**: <why this hurts future maintenance>
    **Suggested refactor**:
    ```
    <minimal patch>
    ```

## Rules

- Prefer concrete suggestions over abstract advice
  ("extract method" is better than "refactor").
- Do **NOT** report stylistic preferences as maintainability
  issues (tabs vs spaces, brace style, etc.).
- If "ugly" code is well-tested, self-contained, and rarely
  changed, it may not be a maintainability problem — judge by
  **change-cost**, not by taste.
- Consider test coverage when assessing cost: code with good
  tests can be safely refactored, so its cost is lower.
```

---
