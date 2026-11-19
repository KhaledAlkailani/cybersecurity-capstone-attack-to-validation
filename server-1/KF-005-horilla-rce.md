# KF-005 — Horilla Unsafe Dynamic Evaluation / RCE

**Severity:** Critical  
**CWE:** CWE-95 — Improper Neutralization of Directives in Dynamically Evaluated Code  
**System:** Server 1 — `192.168.100.250`  
**Application:** Horilla 1.3

## Summary

An authenticated Horilla function processed the `is_active` parameter using Python's `eval()`.

The parameter was expected to represent a Boolean value, but attacker-controlled expressions were interpreted as Python code.

The vulnerable implementation in `project/views.py` followed this pattern:

```python
is_active = eval(request.GET.get("is_active"))
```

## Why This Was Dangerous

`eval()` does not simply parse a Boolean value.

It evaluates Python expressions.

Passing user-controlled request data directly into `eval()` therefore created an authenticated code-execution condition.

## Validation Progression

The vulnerability was demonstrated progressively rather than jumping directly to the highest-impact test.

### 1. Expression Evaluation

A harmless Boolean expression was supplied through `is_active`.

The application behavior confirmed that the expression was being evaluated.

This established unsafe dynamic evaluation, but expression evaluation by itself was not treated as proof of operating-system command execution.

### 2. Controlled Command Execution

A controlled operating-system action was then used to create a proof artifact inside the Horilla environment.

That filesystem artifact provided stronger evidence than an HTTP status code alone.

### 3. Reverse Shell

The same vulnerable behavior was ultimately used to demonstrate a reverse shell inside the Horilla application container.

The resulting shell operated as root **inside the container**.

The project did not demonstrate or claim:

- container escape;
- root access to the underlying host.

## Defensive Evidence

The finding was supported by multiple sources:

- Horilla source-code review;
- network traffic to TCP/8000;
- requests manipulating the `is_active` parameter;
- the controlled filesystem proof artifact;
- the demonstrated reverse shell;
- Podman container inspection.

Useful investigation commands included:

```bash
grep -Rni "eval(request.GET.get" /app/project/
grep -n -A 15 -B 10 "def project_bulk_archive" /app/project/views.py
podman inspect horilla-hr-server-1
```

## Root Cause

The root cause was unsafe dynamic evaluation of untrusted request data.

A value that should have been parsed as a strict Boolean was instead sent directly to Python's expression evaluator.

## Remediation

Horilla was upgraded:

```text
1.3 → 1.3.1
```

The vendor-patched implementation removed the unsafe `eval()` behavior and constrained the parameter to the expected values.

A supported upstream patch was preferred over maintaining a local one-off modification.

## Validation

Unexpected values were submitted again to the same functionality.

The patched application rejected invalid input with application-level validation similar to:

```text
Invalid value for 'is_active'. Use 'true' or 'false'.
```

**Result:** PASS — the original code-execution path could no longer be reproduced.

An HTTP `200` response alone was not treated as exploit success. The security outcome depended on whether the supplied value was evaluated and whether command execution could occur.

## Framework Mapping

- **MITRE ATT&CK:** T1190 — Exploit Public-Facing Application
- **MITRE ATT&CK:** T1059 — Command and Scripting Interpreter
- **MITRE D3FEND:** D3-SU — Software Update

## Takeaway

> Expression evaluation is not the same thing as proving RCE, and HTTP success is not the same thing as exploit success.

The strongest proof came from combining source-code analysis, network evidence, and host-side execution artifacts.
