<div align="center">

# Cybersecurity Capstone  
### From Attack Path to Validated Remediation

**2 Servers · 8 Security Findings · 2 Attack Paths · 8 Validated Fixes**

End-to-end security assessment covering offensive testing, defensive investigation, root-cause analysis, remediation, and validation.

</div>

---

## Overview

This project documents a complete security assessment of two Linux servers hosting file-sharing services, web applications, databases, and administrative interfaces.

The work did not stop at finding vulnerabilities.

Each attack path was reproduced, investigated from the defender's side, traced back to its root cause, remediated, and tested again to confirm that the original security impact could no longer be reproduced.

```text
Discover → Exploit → Investigate → Understand → Remediate → Validate
```

The final result was **eight security findings across two connected attack stories, with all eight remediations successfully validated.**

---

## Environment

| System | Main Services | Applications |
|---|---|---|
| **Server 1 — 192.168.100.250** | Apache, Samba, OpenLDAP, PostgreSQL, MariaDB | CubeCart, Horilla |
| **Server 2 — 192.168.100.107** | vsftpd, OpenSSH, Apache, MariaDB | phpMyFAQ |

### Server 1

Server 1 contained two separate attack paths originating from the same exposed SMB share.

```text
                         ┌── Legacy Authentication Files
                         │          │
                         │          ▼
                         │    Credential Recovery
                         │          │
                         │          ▼
Open SMB Share ──────────┤     CubeCart Admin
                         │          │
                         │          ▼
                         │       Stored XSS
                         │
                         └── HR Deployment Information
                                    │
                                    ▼
                             Default Admin Login
                                    │
                                    ▼
                           Unsafe Input Handling
                                    │
                                    ▼
                              Code Execution
```

The SMB exposure itself did not directly provide code execution.

Its impact came from the information it exposed and the additional weaknesses that could be reached from there.

---

### Server 2

Server 2 followed a more linear attack path.

```text
Anonymous FTP Access
        │
        ▼
Sensitive Maintenance File
        │
        ▼
Plaintext Credential
        │
        ▼
Valid phpMyFAQ Login
        │
        ▼
Insufficient Authorization
        │
        ▼
Sensitive Configuration Backup
```

This path demonstrated how an information disclosure issue can evolve into authenticated application access and eventually expose sensitive configuration data.

---

## Security Findings

| ID | Finding | Severity | Status |
|---|---|:---:|:---:|
| **KF-001** | Unauthenticated Sensitive SMB Access | High | Remediated |
| **KF-002** | Weak Legacy Password Hashing / Credential Exposure | High | Remediated |
| **KF-003** | CubeCart Stored Cross-Site Scripting | Medium | Remediated |
| **KF-004** | Horilla Default Administrative Credentials | Critical | Remediated |
| **KF-005** | Horilla Unsafe Dynamic Evaluation / RCE | Critical | Remediated |
| **KF-006** | Anonymous Sensitive FTP Access | High | Remediated |
| **KF-007** | Plaintext Application Credentials | High | Remediated |
| **KF-008** | phpMyFAQ Insufficient Backup Authorization | High | Remediated |

---

## Investigation

The offensive assessment showed how the weaknesses could be exploited.

The defensive phase focused on a different question:

> **Can the attack be proven from the evidence left behind?**

Evidence was collected before remediation and correlated across multiple sources.

### Evidence Sources

**Server 1**

- Samba configuration and service logs
- Apache and CubeCart access logs
- Network packet capture
- SMB2 file-access activity
- Filesystem metadata
- Preserved application artifacts
- Horilla source code
- Podman container metadata
- Authentication history

**Server 2**

- vsftpd configuration
- FTP transfer logs
- Apache and phpMyFAQ logs
- Filesystem metadata
- Maintenance credential artifact
- Application source code
- Configuration backup artifacts
- Authentication activity

The investigation relied on correlation rather than a single source of truth.

For example:

```text
FTP Log
   +
Filesystem Metadata
   +
Application Login
   +
Backup Request
   +
Generated Backup Artifact
   =
Reconstructed Attack Sequence
```

---

## Root Cause Analysis

The findings ultimately traced back to four recurring security problems.

### Access Control

Sensitive files were reachable through anonymous or guest-accessible services.

Examples:

- Guest-accessible SMB share
- Anonymous FTP access

### Credential Management

Credentials were exposed through several insecure practices:

- Plaintext credential storage
- Weak legacy password hashing
- Default administrative credentials
- Credential material stored in accessible files

### Application Security

Application-level weaknesses included:

- Stored Cross-Site Scripting
- Unsafe evaluation of user-controlled input
- Remote code execution

Where vendor fixes were available, patched releases were used instead of relying only on local workarounds.

### Authorization

phpMyFAQ demonstrated an important distinction:

```text
Authentication → Who are you?
Authorization  → What are you allowed to do?
```

The affected account could authenticate legitimately, but it was able to access backup functionality beyond its intended privileges.

---

## Remediation

Remediation focused on removing the actual cause of each attack path.

| Area | Remediation |
|---|---|
| SMB | Guest access disabled and authenticated access enforced |
| Legacy authentication | Exposed legacy files preserved as evidence and removed |
| CubeCart | Updated from 6.7.4 to a patched release |
| Horilla credentials | Default administrator credential replaced |
| Horilla RCE | Updated from 1.3 to 1.3.1 and unsafe evaluation removed |
| FTP | Anonymous access disabled |
| Plaintext credential | File removed and credential rotated |
| phpMyFAQ | Updated from 4.0.16 to 4.0.17 with proper backup authorization |

---

## Validation

A configuration change or software update was not considered enough on its own.

The original attack behavior was repeated after remediation.

```text
Original Attack
      │
      ▼
Remediation
      │
      ▼
Same Test Repeated
      │
      ▼
Original Impact No Longer Reproduced
```

### Results

| Validation Test | Result |
|---|---|
| Anonymous SMB access | **Blocked** |
| Legacy credential exposure | **Blocked** |
| CubeCart Stored XSS | **No execution** |
| Previous Horilla default credential | **Rejected** |
| Horilla unsafe input / RCE | **Rejected** |
| Anonymous FTP login | **Blocked** |
| Previously exposed credential | **Rejected** |
| Unauthorized phpMyFAQ backup | **Denied** |

<div align="center">

## 8 / 8 Remediations Validated

**A patch is not proof. Retesting is proof.**

</div>

---

## MITRE Mapping

The project used MITRE ATT&CK to describe observed adversary behavior and MITRE D3FEND to describe defensive actions.

### ATT&CK

| Technique | ID |
|---|---|
| Network Service Discovery | T1046 |
| File and Directory Discovery | T1083 |
| Credentials in Files | T1552.001 |
| Valid Accounts | T1078 |
| Default Accounts | T1078.001 |
| Exploit Public-Facing Application | T1190 |
| Command and Scripting Interpreter | T1059 |
| Data from Local System | T1005 |

Only behavior supported by collected evidence was mapped.

### D3FEND

Defensive actions included:

- User Account Permissions
- File Eviction
- Change Default Password
- Credential Rotation
- Software Update

A simple way to describe the distinction:

> **ATT&CK describes what the attacker did.**  
> **D3FEND describes what we did about it.**

---

## Methodology

The defensive investigation followed a structured workflow aligned with NIST incident-response concepts.

```text
Review Offensive Findings
        │
        ▼
Preserve the Environment
        │
        ▼
Collect Evidence
        │
        ▼
Analyze & Correlate
        │
        ▼
Build the Timeline
        │
        ▼
Root Cause Analysis
        │
        ▼
MITRE Mapping
        │
        ▼
Remediation
        │
        ▼
Post-Remediation Validation
```

The focus throughout the project was evidence-driven investigation rather than relying only on the original offensive findings.

---

## Project Structure

```text
.
├── README.md
│
├── docs/
│   ├── methodology.md
│   ├── attack-paths.md
│   ├── root-cause-analysis.md
│   ├── remediation.md
│   └── validation.md
│
├── server-1/
│   ├── KF-001-smb-access.md
│   ├── KF-002-legacy-md5.md
│   ├── KF-003-cubecart-stored-xss.md
│   ├── KF-004-horilla-default-credentials.md
│   └── KF-005-horilla-rce.md
│
├── server-2/
│   ├── KF-006-anonymous-ftp.md
│   ├── KF-007-plaintext-credentials.md
│   └── KF-008-phpmyfaq-authorization.md
│
├── evidence/
│   ├── screenshots/
│   ├── sanitized-logs/
│   └── timelines/
│
├── mitre/
│   ├── attack-mapping.md
│   └── d3fend-mapping.md
│
├── diagrams/
│   ├── environment-architecture.png
│   ├── server1-attack-path.png
│   └── server2-attack-path.png
│
├── presentation/
│   └── capstone-presentation.pdf
│
└── report/
    └── cybersecurity-capstone-report-sanitized.pdf
```

---

## What I Worked On

This project gave me practical experience across several areas of cybersecurity:

`Penetration Testing` · `Incident Investigation` · `DFIR` · `Log Analysis` · `Packet Analysis` · `Linux` · `Web Security` · `Root Cause Analysis` · `Security Hardening` · `Remediation Validation`

The part I found most valuable was moving between the offensive and defensive perspectives.

Finding a vulnerability answers one question.

Understanding how it was used, what evidence it left behind, why it existed, and whether the remediation actually worked answers several more.

---

## Future Work

The current project focused on assessment, investigation, remediation, and validation.

A natural next phase would be to extend the environment into continuous monitoring and detection engineering, including:

- Centralized log collection
- SIEM integration
- Custom detection rules
- Alert correlation
- Additional host telemetry
- Detection validation
- SOAR playbooks after response procedures are defined and tested

This would extend the project from retrospective investigation into continuous detection and response.

---

## Responsible Use

This project was completed in an authorized and controlled cybersecurity environment.

Public material is sanitized and does not intentionally include:

- Active passwords
- Session cookies
- Authentication tokens
- Private keys
- Reusable credentials
- Sensitive configuration secrets

The technical material is included to document the investigation, findings, remediation, and validation process.

---

## Final Note

The biggest lesson from this project was not that one critical vulnerability can compromise a system.

It was that **small weaknesses become much more dangerous when they connect.**

```text
Find the problem
      ↓
Understand what happened
      ↓
Understand why it happened
      ↓
Fix the root cause
      ↓
Test it again
```

> **Finding the problem is only the first step.  
> Proving the fix worked is what completes the job.**

---

<div align="center">

### Khaled AlKailani

Cybersecurity Specialist · Security Operations · DFIR · Security Engineering

</div>
