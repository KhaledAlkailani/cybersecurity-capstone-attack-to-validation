# Future Improvements

The completed project focused on:

```text
Assessment
→ Investigation
→ Root Cause
→ Remediation
→ Validation
```

A natural next phase would extend the environment into continuous monitoring and detection engineering.

## 1. Centralized Log Collection

Forward relevant telemetry from both servers to a dedicated monitoring platform.

Potential sources:

- Apache
- Samba
- vsftpd
- authentication logs
- application logs
- container logs
- operating-system security events

The purpose would be to make evidence searchable from one place without removing the original host logs as a source of truth.

## 2. SIEM Integration

A dedicated SIEM could provide:

- centralized search;
- normalization;
- cross-host correlation;
- alerting;
- retention;
- investigation dashboards.

This was intentionally treated as a separate monitoring phase rather than adding a SIEM only for appearance.

## 3. Detection Engineering

Once telemetry is centralized, detections could be developed around behaviors demonstrated in the project.

Examples of detection themes:

- unusual anonymous or guest access;
- access to sensitive legacy paths;
- repeated privileged application actions;
- unexpected administrative authentication;
- unusual web requests to sensitive endpoints;
- suspicious process behavior inside application containers.

The emphasis should be on detecting meaningful behaviors, not simply producing a large number of alerts.

## 4. Host Telemetry

Additional endpoint telemetry could provide more visibility into:

- process execution;
- child processes;
- file creation;
- authentication events;
- network connections from application processes.

This would strengthen host-side correlation for events such as the Horilla code-execution path.

## 5. Time Synchronization

The two servers used different timezones during the investigation.

Future monitoring should use consistent time synchronization and clearly defined timestamp handling to simplify cross-system correlation.

## 6. Alert Validation

Detection rules should be tested against known attack behavior.

The same philosophy used for remediation should apply:

```text
Write Detection
      ↓
Replay / Reproduce Behavior
      ↓
Confirm Alert
      ↓
Tune False Positives
```

## 7. SOAR — Only After the Process Is Defined

SOAR can become useful after:

- telemetry is reliable;
- detections are stable;
- response procedures are documented;
- automation boundaries are understood.

Automating an undefined process would add complexity without improving response quality.

## Goal of the Next Phase

```text
Current Project:
Understand what happened after the attack

Next Phase:
Detect the same behavior while it is happening
```
