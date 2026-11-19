# Scope and Limitations

This document records the boundaries used when interpreting the project results.

## Scope

The assessment covered two Linux servers:

- Server 1 — `192.168.100.250`
- Server 2 — `192.168.100.107`

The work included:

- network and service discovery;
- SMB and FTP enumeration;
- web-application testing;
- credential-exposure analysis;
- controlled exploitation;
- defensive evidence collection;
- timeline reconstruction;
- root-cause analysis;
- remediation;
- post-remediation validation;
- MITRE ATT&CK and D3FEND mapping.

## Authorized Environment

All testing was performed within the authorized project environment.

The public repository is intended as technical documentation and a portfolio artifact.

## Evidence Limitations

### Server 1

A packet capture was available and used alongside server-side logs and artifacts.

### Server 2

No usable packet capture was available for the investigated period.

The Server 2 timeline therefore relied on:

- vsftpd logs;
- Apache/phpMyFAQ logs;
- filesystem metadata;
- application source;
- generated artifacts.

This reduces one source of corroboration, but does not invalidate the server-side evidence.

## Timezone Differences

The systems did not use the same local timezone.

- Server 1 — Asia/Amman (UTC+03)
- Server 2 — America/New_York (UTC-04)

Timeline analysis therefore considered the recorded timezone for each system.

## Impact Boundaries

### Horilla RCE

The demonstrated reverse shell executed as root **inside the Horilla container**.

The project does not claim:

- root access to the underlying host;
- container escape;
- compromise beyond the demonstrated environment.

### CubeCart Stored XSS

The project demonstrated persistent browser-side script execution.

It does not claim untested downstream impact such as:

- session-cookie theft;
- privilege escalation;
- compromise of unrelated users.

### phpMyFAQ

The project demonstrated that an authenticated low-privileged account could trigger sensitive backup functionality.

It does not describe the issue as an authentication bypass because unauthenticated requests were correctly rejected.

## Public-Repository Sanitization

The public repository intentionally excludes:

- active passwords;
- session tokens;
- reusable credentials;
- full sensitive configuration archives;
- private keys;
- unnecessary exploitation payloads.

## Interpretation Principle

The project only claims impact supported by the available evidence.

> Do not turn a possible next step into a claimed result unless it was actually demonstrated.
