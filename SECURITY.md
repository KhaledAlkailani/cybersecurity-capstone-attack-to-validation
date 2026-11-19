# Security and Responsible Use

This repository documents work performed in an authorized and controlled cybersecurity environment.

## Public Repository Policy

The public version is intentionally sanitized.

It does not intentionally include:

- active passwords;
- reusable credentials;
- authentication tokens;
- session cookies;
- private keys;
- sensitive database secrets;
- full application configuration backups;
- unnecessary raw exploitation payloads.

Where sensitive values are relevant to the explanation, they are described conceptually or replaced with redacted placeholders.

## Scope

All testing described in this repository was performed within the authorized project environment.

The material is published for:

- education;
- portfolio demonstration;
- defensive analysis;
- incident-response learning;
- remediation documentation.

## Reporting Repository Issues

If a secret or sensitive value is accidentally committed to this repository, it should be treated as exposed.

The correct response is:

1. remove the public exposure;
2. rotate or invalidate the affected secret;
3. review repository history and access;
4. document the incident if necessary.

Deleting a visible file alone is not sufficient when a credential has already been exposed.
