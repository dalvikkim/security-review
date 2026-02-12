---
scope:
  domain: ["secrets-management"]
priority: 100
applies_to:
  - "applications handling credentials, tokens, API keys"
  - "CI/CD pipelines and automation"
  - "cloud and on-prem services"
---

# Secrets Management Security

## Overview

Secrets are a single point of failure: exposure can compromise the whole system. This document defines language-agnostic principles for handling secrets.

**Do-Not-Send and redaction:** See SKILL.md for the policy on never sending secret material (or findings containing secrets) to the LLM.

## What counts as a secret

- API keys / tokens
- Passwords / credentials
- OAuth client secrets
- Private keys (SSH, JWT signing)
- Cloud access keys
- Database connection strings that include credentials

## Secure defaults

- Never store secrets in source code, config files, or images.
- Inject secrets at runtime only.
- Apply least privilege; plan expiration and rotation.

## Storage

- Env vars for local/temporary use only.
- Prefer a secret manager (Vault, AWS Secrets Manager, Azure Key Vault, GCP Secret Manager).
- Kubernetes Secrets only with encryption-at-rest verified.

## Access control

- Minimize who can read secrets.
- Separate secrets per service.
- Prefer read-only access where possible.

## Do

- Abstract secret access in code.
- Mask secrets in CI/CD logs.
- On secret-use failure, return clear but minimal error messages (no secret values).

## Don't

- Commit `.env`, `.pem`, `.key` to Git.
- Put secrets in URLs or query strings.
- Log or include secrets in error messages.
- Share one secret across multiple systems.

## Empty password / blank credential

Treat as a finding when a key such as `password`, `passwd`, `pwd`, `pass`, `passphrase`, `secret`, `clientSecret`, `apiSecret`, or `token` is set to empty string or whitespace only.

**Impact:** Empty passwords can disable authentication or propagate "no password set" to production.

**Fix:** Require secrets at startup (fail-fast) or use null/undefined and handle explicitly; in production, require injection from a secret store or env.

## Verification checklist

- [ ] No hardcoded secrets in source
- [ ] No secrets in logs or error messages
- [ ] Secret access minimized and documented
- [ ] Rotation strategy considered

## Notes

- Base64 is not encryption.
- Consider both storage and transmission when encrypting secrets.
