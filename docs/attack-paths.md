# Attack Paths

This document summarizes the two attack stories reconstructed during the assessment.

The focus is the relationship between findings rather than treating each vulnerability as an isolated issue.

---

## Server 1 — Two Paths From One Exposure

**Target:** `192.168.100.250`  
**Attack activity date:** 07 October 2026

The common starting point was an SMB share that allowed unauthenticated guest access.

### Path A — SMB to CubeCart

```text
Open SMB Share
      ↓
Legacy Authentication Files
      ↓
MD5 Password Material
      ↓
Offline Credential Recovery
      ↓
CubeCart Admin Login
      ↓
Stored Cross-Site Scripting
```

The SMB weakness did not directly provide CubeCart access.

It exposed legacy authentication material. That material was weak enough to support offline password recovery, and the recovered credential was successfully reused against the CubeCart administrative interface.

Once administrative access was obtained, the product description functionality was tested and Stored XSS was demonstrated.

---

### Path B — SMB to Horilla

```text
Open SMB Share
      ↓
HR Deployment Information
      ↓
Default Administrative Credential
      ↓
Horilla Admin Login
      ↓
Unsafe is_active Handling
      ↓
Python eval()
      ↓
Controlled Command Execution
      ↓
Reverse Shell Inside Container
```

The share exposed information about the Horilla deployment, including the application location and the fact that the original administrative password had not been changed.

After authentication, testing identified unsafe handling of the `is_active` parameter. The vulnerable implementation evaluated user-controlled input rather than treating it strictly as a Boolean value.

Expression evaluation was confirmed first, then controlled command execution, followed by a reverse shell inside the Horilla application container.

The demonstrated execution context was root **inside the container**. The project did not claim host-level root compromise or a container escape.

---

## Server 2 — FTP to Sensitive Configuration

**Target:** `192.168.100.107`  
**Attack activity date:** 02 October 2026

```text
Anonymous FTP Access
      ↓
Internal Operational Files
      ↓
maintenance-account.txt
      ↓
Plaintext Valid Credential
      ↓
phpMyFAQ Authentication
      ↓
Authenticated Low-Privilege Session
      ↓
/api/setup/backup
      ↓
Insufficient Authorization
      ↓
Configuration Backup Created
      ↓
Sensitive Archive Retrieved
```

Anonymous FTP access exposed internal content that should not have been publicly reachable.

One exposed file contained a valid application credential in plaintext. The credential was successfully used against phpMyFAQ.

The backup endpoint itself was not unauthenticated. An unauthenticated request returned `401 Unauthorized`.

The issue appeared **after authentication**: the low-privileged account could invoke sensitive backup functionality without the required authorization.

This made the final finding an authorization problem rather than an authentication bypass.

---

## Shared Pattern

Both servers followed the same broader security pattern:

```text
Information Exposure
      ↓
Credential Opportunity
      ↓
Authenticated Access
      ↓
Additional Application Weakness
      ↓
Higher Impact
```

The project therefore focused not only on individual findings, but on how weaknesses can be chained into a larger compromise path.
