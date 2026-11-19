# Root Cause Analysis

The project identified eight findings, but the underlying causes were concentrated in four recurring areas.

Treating each finding in isolation would have missed the pattern that made the attack chains possible.

---

## 1. Access Control

### Related Findings

- KF-001 — Unauthenticated Sensitive SMB Access
- KF-006 — Anonymous Sensitive FTP Access

### Root Cause

Services exposed information to users who had not been properly authenticated or authorized to access that content.

The issue was not simply the existence of SMB or FTP.

The issue was the access policy applied to sensitive content.

### Pattern

```text
Network Service
      ↓
Overly Permissive Access
      ↓
Sensitive Information Exposure
```

---

## 2. Credential Management

### Related Findings

- KF-002 — Weak Legacy Password Hashing
- KF-004 — Default Horilla Administrative Credentials
- KF-007 — Plaintext Application Credentials

### Root Cause

Credentials were handled in ways that made them easier to discover, recover, or reuse.

The project observed three different forms:

```text
Weak Password Storage
Default Credential
Plaintext Credential
```

Each created a different route to valid application access.

### Pattern

```text
Credential Weakness
      ↓
Credential Obtained or Reused
      ↓
Valid Account Access
```

---

## 3. Application Security

### Related Findings

- KF-003 — CubeCart Stored XSS
- KF-005 — Horilla Unsafe Dynamic Evaluation / RCE

### Root Cause

User-controlled input reached sensitive application behavior without sufficient safe handling.

For CubeCart, untrusted content could later execute in the browser.

For Horilla, request data reached Python's `eval()`, allowing expressions to be interpreted as code.

### Pattern

```text
Untrusted Input
      ↓
Insufficient Input / Output Handling
      ↓
Unsafe Application Behavior
```

---

## 4. Authorization

### Related Finding

- KF-008 — phpMyFAQ Insufficient Backup Authorization

### Root Cause

The application verified that the user had authenticated, but did not sufficiently verify whether the user's role was permitted to perform the backup action.

### Pattern

```text
Valid Session
      ↓
Sensitive Function
      ↓
Missing / Insufficient Permission Enforcement
```

---

# Cross-Finding Pattern

Across both servers, the most important pattern was not one individual vulnerability.

It was the ability to chain weaknesses together.

```text
Exposure
   ↓
Information
   ↓
Credential Opportunity
   ↓
Authenticated Access
   ↓
Application / Authorization Weakness
   ↓
Higher Impact
```

This is why the project focused on attack paths rather than presenting the eight findings as unrelated items.

---

# Root Cause vs Finding

```text
Finding    → What security problem was observed?
Evidence   → What proves it happened?
Root Cause → Why was the problem possible?
Remediation→ What changes the security state?
Validation → What proves the fix actually worked?
```

That distinction was used throughout the defensive investigation.
