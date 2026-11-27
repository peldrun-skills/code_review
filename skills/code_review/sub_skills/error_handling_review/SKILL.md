---
name: error_handling_review
description: Review exception hierarchies, retry logic, fallbacks, and error propagation.
---

# Error Handling Review

You are reviewing code for **robustness under failure**: does it
behave correctly when things go wrong?

## Categories

1. **Bare excepts** — `except:` or `except Exception:` that swallow
   errors without logging or re-raising.
2. **Silent failures** — `except: pass` — errors disappear from the
   log entirely.
3. **Overbroad excepts** — catching `Exception` when the caller
   expected a specific error type (loses the distinction between
   "not found" and "network down").
4. **Missing finally** — resources released only on the happy path.
5. **Retry storms** — retries without backoff, retries on
   non-idempotent operations, unbounded retry loops.
6. **Fallback masking** — a fallback that produces wrong-but-valid
   output, hiding a real bug.
7. **Error context loss** — `raise NewError(msg)` without
   `from exc` in Python; `throw new Error(msg)` without `{ cause }`
   in JavaScript.
8. **Inconsistent hierarchy** — some errors extend `Error`, some are
   plain strings, some are dicts; callers can't branch reliably.

## Method

For each function:

1. Enumerate the ways it can fail (I/O, network, parse, validation).
2. For each failure path, check: is the error **logged**?
   **propagated**? **handled correctly**?
3. Identify any path that silently produces wrong output.

## Output Format

    ## [Severity] <Short title>
    **Location**: `path/to/file.py:42`
    **Evidence**:
    ```
    <quoted code>
    ```
    **Failure scenario**: <how it goes wrong>
    **Fix**:
    ```
    <minimal patch>
    ```

## Rules

- Prefer `raise NewError(...) from original` over bare `raise`.
- **Fail fast** on programmer errors (assertion / ValueError on
  invalid args). **Fail gracefully** on operational errors (network,
  disk, external API).
- Every `except` must log at minimum at DEBUG and typically at
  WARNING or ERROR — unless the catch is deliberate control flow
  (e.g. `StopIteration` in a loop protocol).
- Do not swallow exceptions that the caller needs to act on.
```
