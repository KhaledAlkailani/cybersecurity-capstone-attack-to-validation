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

## Quick Navigation

| Area | Link |
|---|---|
| Project overview | [docs/project-overview.md](docs/project-overview.md) |
| Attack paths | [docs/attack-paths.md](docs/attack-paths.md) |
| Findings summary | [docs/findings-summary.md](docs/findings-summary.md) |
| Timeline | [docs/timeline.md](docs/timeline.md) |
| Methodology | [docs/methodology.md](docs/methodology.md) |
| Evidence guide | [docs/evidence-guide.md](docs/evidence-guide.md) |
| Root cause analysis | [docs/root-cause-analysis.md](docs/root-cause-analysis.md) |
| Remediation & validation | [docs/remediation-and-validation.md](docs/remediation-and-validation.md) |
| Lessons learned | [docs/lessons-learned.md](docs/lessons-learned.md) |
| Tools & commands | [docs/tools-and-commands.md](docs/tools-and-commands.md) |
| Scope & limitations | [docs/scope-and-limitations.md](docs/scope-and-limitations.md) |
| Future improvements | [docs/future-improvements.md](docs/future-improvements.md) |
| Full repository map | [docs/repository-map.md](docs/repository-map.md) |

---

## Environment

| System | Main Services | Applications |
|---|---|---|
| **Server 1 — 192.168.100.250** | Apache, Samba, OpenLDAP, PostgreSQL, MariaDB | CubeCart, Horilla |
| **Server 2 — 192.168.100.107** | vsftpd, OpenSSH, Apache, MariaDB | phpMyFAQ |

---

## Attack Paths

### Server 1

One exposed SMB share created two separate paths.

```text
                         ┌── Legacy Authentication Files
                         │          ↓
                         │    Credential Recovery
                         │          ↓
Open SMB Share ──────────┤     CubeCart Admin
                         │          ↓
                         │       Stored XSS
                         │
                         └── HR Deployment Information
                                    ↓
                             Default Admin Login
                                    ↓
                             Horilla Function
                                    ↓
                              Unsafe eval()
                                    ↓
                           Code Execution / RCE
```

The SMB share did **not** directly provide RCE. Its impact came from the information it exposed and the weaknesses that information made reachable.

[View Server 1 write-ups →](server-1/README.md)

### Server 2

```text
Anonymous FTP
      ↓
Sensitive Maintenance File
      ↓
Plaintext Credential
      ↓
Valid phpMyFAQ Login
      ↓
Insufficient Authorization
      ↓
Sensitive Configuration Backup
```

[View Server 2 write-ups →](server-2/README.md)

---

## Security Findings

| ID | Finding | Severity | Status |
|---|---|:---:|:---:|
| [KF-001](server-1/KF-001-smb-access.md) | Unauthenticated Sensitive SMB Access | High | Remediated |
| [KF-002](server-1/KF-002-legacy-md5.md) | Weak Legacy Password Hashing / Credential Exposure | High | Remediated |
| [KF-003](server-1/KF-003-cubecart-stored-xss.md) | CubeCart Stored XSS | Medium | Remediated |
| [KF-004](server-1/KF-004-horilla-default-credentials.md) | Horilla Default Administrative Credentials | Critical | Remediated |
| [KF-005](server-1/KF-005-horilla-rce.md) | Horilla Unsafe Dynamic Evaluation / RCE | Critical | Remediated |
| [KF-006](server-2/KF-006-anonymous-ftp.md) | Anonymous Sensitive FTP Access | High | Remediated |
| [KF-007](server-2/KF-007-plaintext-credentials.md) | Plaintext Application Credentials | High | Remediated |
| [KF-008](server-2/KF-008-phpmyfaq-authorization.md) | phpMyFAQ Insufficient Backup Authorization | High | Remediated |

---

## Investigation

The offensive assessment showed how the weaknesses could be exploited.

The defensive phase focused on a different question:

> **Can the attack be proven from the evidence left behind?**

Evidence was correlated across:

- Samba and FTP service logs
- Apache and application logs
- Server 1 packet capture
- filesystem metadata
- preserved artifacts
- source-code review
- container metadata
- post-remediation testing

The investigation deliberately distinguished between:

```text
Finding    → The security problem
Evidence   → What proves what happened
Root Cause → Why the problem existed
Fix        → What changed
Validation → What proves the fix worked
```

[View the evidence model →](docs/evidence-guide.md)

---

## Root Causes

The eight findings ultimately traced back to four recurring areas:

| Area | Examples |
|---|---|
| **Access Control** | SMB guest access, anonymous FTP |
| **Credential Management** | MD5 legacy auth, default credentials, plaintext credentials |
| **Application Security** | Stored XSS, unsafe dynamic evaluation |
| **Authorization** | Low-privileged phpMyFAQ backup access |

[View full root-cause analysis →](docs/root-cause-analysis.md)

---

## Validation

A configuration change or software update was not considered sufficient proof on its own.

The original attack behavior was repeated after remediation.

```text
Original Attack
      ↓
Remediation
      ↓
Same Test Repeated
      ↓
Original Impact No Longer Reproduced
```

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

[View remediation and validation details →](docs/remediation-and-validation.md)

---

## MITRE Mapping

The project used two MITRE frameworks for different purposes.

```text
ATT&CK  → What the attacker did
D3FEND  → What the defender did about it
```

### ATT&CK Techniques

- T1046 — Network Service Discovery
- T1083 — File and Directory Discovery
- T1552.001 — Credentials In Files
- T1078 — Valid Accounts
- T1078.001 — Default Accounts
- T1190 — Exploit Public-Facing Application
- T1059 — Command and Scripting Interpreter
- T1005 — Data from Local System

[ATT&CK mapping →](mitre/attack-mapping.md)  
[D3FEND mapping →](mitre/d3fend-mapping.md)

---

## Project Structure

```text
.
├── README.md
├── SECURITY.md
├── .gitignore
│
├── docs/
│   ├── repository-map.md
│   ├── project-overview.md
│   ├── attack-paths.md
│   ├── findings-summary.md
│   ├── timeline.md
│   ├── methodology.md
│   ├── evidence-guide.md
│   ├── root-cause-analysis.md
│   ├── remediation-and-validation.md
│   ├── tools-and-commands.md
│   ├── lessons-learned.md
│   ├── scope-and-limitations.md
│   └── future-improvements.md
│
├── server-1/
│   ├── README.md
│   ├── KF-001-smb-access.md
│   ├── KF-002-legacy-md5.md
│   ├── KF-003-cubecart-stored-xss.md
│   ├── KF-004-horilla-default-credentials.md
│   └── KF-005-horilla-rce.md
│
├── server-2/
│   ├── README.md
│   ├── KF-006-anonymous-ftp.md
│   ├── KF-007-plaintext-credentials.md
│   └── KF-008-phpmyfaq-authorization.md
│
├── evidence/
│   └── README.md
│
└── mitre/
    ├── attack-mapping.md
    └── d3fend-mapping.md
```

---

## What This Project Demonstrates

`Penetration Testing` · `Incident Investigation` · `DFIR` · `Log Analysis` · `Packet Analysis` · `Linux` · `Web Security` · `Root Cause Analysis` · `Security Hardening` · `Remediation Validation`

The most valuable part of the project was moving between offensive and defensive perspectives.

Finding a vulnerability answers one question.

Understanding how it was used, what evidence it left behind, why it existed, and whether the remediation actually worked answers several more.

---

## Future Work

The next logical phase is continuous monitoring and detection engineering:

- centralized log collection;
- SIEM integration;
- detection engineering;
- additional host telemetry;
- alert validation;
- SOAR only after response procedures are mature.

[View future improvements →](docs/future-improvements.md)

---

## Responsible Use

This project was completed in an authorized and controlled environment.

The public repository is intentionally sanitized and does not publish active credentials, session material, private keys, sensitive configuration archives, or unnecessary reusable exploitation payloads.

[Read the security and sanitization policy →](SECURITY.md)

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

> **Finding the problem is only the first step. Proving the fix worked is what completes the job.**

---

<div align="center">

### Khaled AlKailani

Cybersecurity Specialist · Security Operations · DFIR · Security Engineering

</div>
