# KF-004 — Horilla Default Administrative Credentials

**Severity:** Critical  
**CWE:** CWE-1392 — Use of Default Credentials  
**System:** Server 1 — `192.168.100.250`  
**Application:** Horilla

## Summary

The Horilla administrative account remained accessible using the original deployment/default credential.

Information about the HR application was first discovered in deployment material exposed through the SMB share.

That information indicated that the initial administrator password had not been changed after setup.

## Attack Path

```text
SMB Exposure
      ↓
HR Deployment Information
      ↓
Horilla on TCP/8000
      ↓
Default Administrative Credential
      ↓
Successful Admin Login
```

The default credential was then tested and successfully authenticated to the Horilla application.

## Evidence

Evidence supporting the finding included:

- deployment information recovered from the exposed SMB share;
- Horilla reachable on TCP port 8000;
- successful authentication using the referenced default administrative credential;
- later application activity following that authentication.

Actual credential values are intentionally excluded from this public repository.

## Root Cause

The administrative credential created during deployment was never replaced with a unique production credential.

This turned information about the original setup into an immediately reusable authentication path.

## Why It Matters

Default credentials are especially dangerous because they can be:

- documented;
- reused across deployments;
- exposed in setup notes;
- guessed by anyone familiar with the application or deployment process.

In this project, the credential was not merely theoretical. It was still valid.

## Remediation

The default administrative password was replaced with a unique non-default credential.

Existing sessions associated with the old credential were invalidated as part of the remediation process.

## Validation

The previously valid default credential was tested again.

**Result:** PASS — the old credential was rejected.

## Framework Mapping

- **MITRE ATT&CK:** T1078.001 — Default Accounts
- **MITRE D3FEND:** D3-CDP — Change Default Password

## Takeaway

The SMB share did not directly compromise Horilla.

It exposed the information that made an existing credential-management weakness reachable.
