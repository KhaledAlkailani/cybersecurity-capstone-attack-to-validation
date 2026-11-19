# Lessons Learned

This project changed how I think about vulnerability assessment and incident investigation.

The most useful lessons were not individual commands or tools. They were the security principles that appeared repeatedly across both servers.

---

## Open Ports Are Not Vulnerabilities by Themselves

A listening service is part of the attack surface.

The security problem comes from what that service exposes, how it is configured, and what an attacker can do with it.

---

## Information Disclosure Can Be the Start of the Real Attack

Neither exposed file-sharing service directly provided the final impact.

The exposed information enabled the next steps.

On Server 1, SMB content revealed authentication material and deployment information.

On Server 2, FTP content exposed a valid application credential.

> Information can be just as valuable to an attacker as direct code execution when it reveals the next path.

---

## Small Weaknesses Become Dangerous When They Connect

The project was not eight isolated security issues.

```text
Exposure
→ Information
→ Credential
→ Access
→ Additional Weakness
→ Higher Impact
```

This was the main pattern across both servers.

---

## Authentication Is Not Authorization

The phpMyFAQ case made this distinction clear.

The backup endpoint rejected unauthenticated requests.

The problem was that an authenticated low-privileged user could still perform a privileged backup operation.

```text
Authentication → Who are you?
Authorization  → What are you allowed to do?
```

---

## HTTP 200 Does Not Automatically Mean Exploit Success

A successful HTTP response describes request handling.

It does not always prove the security impact.

For Horilla, post-remediation testing still produced an HTTP response while application-level validation rejected the unsafe value.

---

## Container Root Is Not Automatically Host Root

The Horilla reverse shell executed as root inside the application container.

That is serious impact, but it is not the same as proving root access to the underlying host.

The project did not claim a container escape that was not demonstrated.

---

## Evidence Should Be Correlated

A single log rarely tells the whole story.

The investigation became stronger when multiple sources supported the same sequence:

- Service logs
- Apache logs
- Packet capture
- Filesystem metadata
- Application artifacts
- Source-code review

---

## Preserve Before You Fix

Remediation changes the system.

Files may disappear, timestamps may change, logs may rotate, and vulnerable behavior may no longer be reproducible.

Relevant evidence was therefore preserved before fixes were applied.

---

## Removing an Exposed Secret Is Not Enough

Once a credential has been exposed, deleting the file that contained it does not make the credential safe again.

The Server 2 remediation required both:

```text
Remove the exposed file
+
Rotate the credential
```

---

## Prefer Supported Vendor Fixes When Available

Official patched releases were preferred over ad-hoc local modifications where vendor fixes existed.

This made remediation easier to maintain, document, and validate.

---

## Remediation Is Not Complete Until It Is Tested

This became the most important lesson in the project.

```text
Find
→ Understand
→ Fix
→ Retest
```

> Finding the problem is only the first step. Proving the fix worked is what completes the job.
