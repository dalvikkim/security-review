---
scope:
  domain: ["secrets-management"]
priority: 100
---

# Secrets Management

## What Counts as a Secret

- API keys / tokens
- Passwords / credentials
- OAuth client secrets
- Private keys (SSH, JWT signing)
- Cloud access keys
- Database connection strings with credentials

## Secure Defaults

- Never store secrets in source code or config files
- Inject secrets at runtime only
- Apply least privilege; plan expiration and rotation

## Storage Guidelines

- Environment variables for local/temporary use only
- Prefer secret managers (Vault, AWS Secrets Manager, Azure Key Vault, GCP Secret Manager)
- Kubernetes Secrets only with encryption-at-rest verified

## Do

- Abstract secret access in code
- Mask secrets in CI/CD logs
- Fail fast on missing required secrets

## Don't

- Commit `.env`, `.pem`, `.key` to Git
- Put secrets in URLs or query strings
- Log or include secrets in error messages
- Share one secret across multiple systems

## High-Risk Patterns

- Hardcoded `password = "..."` in source
- Empty password assignments
- Secrets in Docker image layers
- Secrets visible in build logs

## Verification Checklist

- [ ] No hardcoded secrets in source
- [ ] No secrets in logs or error messages
- [ ] Secret access minimized and documented
- [ ] Rotation strategy defined
