# Methodology

The project combined offensive security assessment with defensive investigation and remediation validation.

The goal was not to run isolated tools or collect a list of vulnerabilities.

The goal was to answer four questions:

```text
How did the attack work?
What evidence proves it?
Why did the weakness exist?
Did the fix actually stop the original behavior?
```

## Workflow

```text
1. Review offensive findings
        ↓
2. Preserve the environment
        ↓
3. Collect evidence
        ↓
4. Analyze and correlate
        ↓
5. Identify indicators
        ↓
6. Build the timeline
        ↓
7. Perform root-cause analysis
        ↓
8. Map observed behavior to MITRE ATT&CK
        ↓
9. Apply remediation
        ↓
10. Map defensive actions to MITRE D3FEND
        ↓
11. Repeat the original attack behavior
        ↓
12. Confirm whether the original impact remains possible
```

## Offensive Phase

The offensive phase followed a black-box style workflow:

- service discovery;
- enumeration;
- information and credential discovery;
- authentication testing;
- application functionality review;
- controlled exploitation.

The project focused on demonstrating impact without expanding beyond the authorized environment.

## Defensive Phase

The defensive phase was aligned with incident-response concepts from:

- NIST SP 800-61 Rev. 3
- NIST Cybersecurity Framework 2.0

The investigation relied on original server-side evidence rather than treating the offensive report as sufficient proof.

## Evidence Correlation

Evidence sources included:

- network traffic;
- service logs;
- web/application logs;
- filesystem metadata;
- preserved artifacts;
- source code;
- configuration;
- container metadata.

A single source was rarely treated as the full story.

For example:

```text
Network Request
      +
Application Log
      +
Filesystem Artifact
      =
Stronger Evidence of What Happened
```

## Root-Cause Analysis

Findings were grouped into recurring root-cause areas:

1. Access control
2. Credential management
3. Application security
4. Authorization

This helped separate symptoms from the underlying reason an attack step succeeded.

## Remediation Strategy

Where official vendor patches existed, supported vendor releases were preferred over local one-off changes.

Configuration problems were addressed at the control that enabled the behavior.

Examples:

- guest access → access-control correction;
- exposed credential → remove exposure + rotate credential;
- default password → replace default credential;
- vulnerable software → supported software update;
- missing permission enforcement → corrected authorization behavior.

## Validation Strategy

Every remediation was tested against the original security behavior.

```text
Before:
Original attack succeeds

After:
Apply remediation

Validation:
Repeat the same relevant test

Pass condition:
Original security impact can no longer be reproduced
```

This distinction between remediation and validation was central to the project.

> A patch is not proof. Retesting is proof.
