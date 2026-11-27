---
name: security_audit
description: Identify security vulnerabilities — injection, secrets, auth bypass, unsafe deserialization.
---

# Security Audit

You are reviewing code for **security**: can an attacker make this
code do something the developer did not intend?

## Threat Categories

1. **Injection** — SQL, command, LDAP, XPath, template, header injection all.
2. **Secrets in code** — API keys, passwords, tokens, private keys,
   credentials, connection strings.
3. **Authentication / authorization bypass** — missing checks,
   incorrect checks (e.g. `if user.is_admin == "true"`), TOCTOU.
4. **Insecure deserialization** — `pickle.loads`, `yaml.load`
   (without SafeLoader), `eval`, `exec`, `Function(...)`.
5. **Path traversal** — `..` or absolute paths in user-controlled
   file paths.
6. **Unsafe cryptography** — MD5 / SHA-1 for security purposes, ECB
   mode, reused IVs, hardcoded salts, insufficient key length.
7. **SSRF** — user-controlled URLs fetched without host validation.
8. **Insecure randomness** — `random` / `Math.random()` for security
   tokens instead of `secrets` / `crypto.randomBytes`.

## Method

For each candidate:

1. **Is the input attacker-controlled?** (user, network, another
   service)
2. **Does the code use it in a sink without proper sanitization?**
3. \*\*Can you construct a concrete payload that demonstrates the
   issue?\*\*

If (1) or (2) is "no", drop the finding. If (3) is "no", downgrade
to informational.

## Output Format

    ## [Severity] <Category>: <Short title>
    **Location**: `path/to/file.py:42`
    **Evidence**:
<quoted code> `**Attack**: <concrete payload or scenario> **Impact**: <what an attacker gains> **Fix**:` <minimal patch> \`\`\`
Rules
No theoretical findings. Demonstrate the attack or drop it.

Never suggest hardcoding a secret in source. Recommend environment
variables or a secrets manager instead.

For vulnerable dependencies, cite the CVE identifier.

Prefer fixing at the sink (parameterized query) over the source
(input validation) — defense in depth means both, but the sink is
the critical one.
