# Project Overview

## Scope

This repository documents an authorized cybersecurity capstone covering two Linux servers and eight security findings.

The project followed the full path from offensive assessment to defensive investigation, root-cause analysis, remediation, and post-remediation validation.

The objective was not to stop at vulnerability discovery. Each attack path was examined from both the attacker and defender perspectives, then retested after remediation.

---

## Environment

| System | IP Address | Main Services | Applications |
|---|---|---|---|
| Server 1 | 192.168.100.250 | Apache, Samba, OpenLDAP, PostgreSQL, MariaDB | CubeCart, Horilla |
| Server 2 | 192.168.100.107 | vsftpd, OpenSSH, Apache, MariaDB | phpMyFAQ |

The investigated source address associated with the attack activity was:

`192.168.100.113`

### Server 1

Server 1 hosted the file-sharing and application services involved in two separate attack paths.

The shared starting point was an SMB share that allowed unauthenticated guest access. The exposed content included legacy authentication files and internal deployment information.

Those disclosures led to two branches:

```text
Open SMB Share
├── Legacy Auth Files → Credential Recovery → CubeCart Admin → Stored XSS
└── HR Deployment Info → Default Admin Login → Horilla → Code Execution
```

Horilla was hosted in a Podman container and exposed through TCP port 8000.

### Server 2

Server 2 hosted an FTP service and the phpMyFAQ application.

The attack path was more linear:

```text
Anonymous FTP
→ Sensitive Maintenance File
→ Plaintext Credential
→ Valid phpMyFAQ Login
→ Insufficient Authorization
→ Sensitive Configuration Backup
```

---

## Findings

| ID | Finding | Severity |
|---|---|---|
| KF-001 | Unauthenticated Sensitive SMB Access | High |
| KF-002 | Weak Legacy Password Hashing / Credential Exposure | High |
| KF-003 | CubeCart Stored Cross-Site Scripting | Medium |
| KF-004 | Horilla Default Administrative Credentials | Critical |
| KF-005 | Horilla Unsafe Dynamic Evaluation / RCE | Critical |
| KF-006 | Anonymous Sensitive FTP Access | High |
| KF-007 | Plaintext Application Credentials | High |
| KF-008 | phpMyFAQ Insufficient Backup Authorization | High |

---

## Investigation Model

The defensive phase focused on four questions:

1. What happened?
2. What evidence supports it?
3. Why did the attack work?
4. Did the remediation actually stop the original behavior?

Evidence sources included service configuration, Samba and FTP logs, Apache and application logs, packet capture from Server 1, filesystem metadata, preserved application artifacts, source-code review, container metadata, and post-remediation retesting.

---

## Outcome

All eight findings were remediated and retested.

```text
8 Findings
8 Remediations
8 Validation Results: PASS
```

> Small weaknesses become much more dangerous when they connect.
