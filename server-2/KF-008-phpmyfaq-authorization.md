# KF-008 — phpMyFAQ Insufficient Backup Authorization

**Severity:** High  
**CWE:** CWE-862 — Missing Authorization  
**System:** Server 2 — `192.168.100.107`  
**Application:** phpMyFAQ 4.0.16

## Summary

The phpMyFAQ configuration-backup endpoint required authentication, but a low-privileged authenticated account could still invoke sensitive backup functionality.

The affected endpoint was:

```text
/api/setup/backup
```

This was an **authorization** issue, not an authentication bypass.

## Authentication vs Authorization

```text
Authentication → Who are you?
Authorization  → What are you allowed to do?
```

The application correctly rejected unauthenticated access.

The security failure occurred after login, when a support-level account could perform a privileged backup operation.

## Evidence Sequence

Apache/phpMyFAQ logs showed the following sequence from the investigated source:

```text
/api/setup/backup  → 401 Unauthorized
/api/v3.0/login    → 200
/api/setup/backup  → 200
```

This sequence was important because it demonstrated that authentication itself was functioning.

The low-privileged user then triggered creation of a configuration backup archive.

A corresponding backup artifact was identified under:

```text
/var/www/phpmyfaq/content/core/config/
```

The archive contained application configuration material, increasing the confidentiality impact.

## Source-Code Review

The affected backup functionality authenticated the user, but did not sufficiently enforce the specific backup permission.

The vulnerable control flow effectively performed an authentication check without a corresponding role/permission check for the privileged action.

The expected secure pattern included enforcement of the relevant backup permission before generating the archive.

## Root Cause

The root cause was insufficient role-based authorization over sensitive administrative functionality.

A valid session was treated as sufficient authorization for a privileged backup operation.

## Attack Path

```text
Plaintext Credential
      ↓
Valid Login
      ↓
Low-Privilege Session
      ↓
Backup Endpoint
      ↓
Missing / Insufficient Permission Enforcement
      ↓
Sensitive Configuration Backup
```

## Remediation

phpMyFAQ was upgraded:

```text
4.0.16 → 4.0.17
```

The patched release corrected authorization enforcement for the backup functionality.

The backup feature itself was retained because it is a legitimate administrative function. The correct remediation was permission enforcement, not removal of the feature.

## Validation

The same low-privileged account was tested after remediation.

The account could still authenticate successfully.

However, the backup operation was denied.

**Result:** PASS

This was a strong validation result because it demonstrated that:

- authentication still worked;
- the account still existed;
- only the unauthorized privileged action was blocked.

## Framework Mapping

- **MITRE ATT&CK:** T1078 — Valid Accounts
- **MITRE ATT&CK:** T1005 — Data from Local System
- **MITRE D3FEND:** D3-SU — Software Update

## Takeaway

> A successful login should not imply permission to perform every application action.

This finding demonstrated why authentication and authorization must be tested independently.
