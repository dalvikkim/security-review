---
scope:
  stack: ["ci-cd"]
priority: 70
---

# CI/CD Security

## Secure Defaults

- Use least-privilege, short-lived tokens
- Mask secrets in build logs
- Gate deployment on security scans

## Do

- Generate and store SBOM
- Sign and verify artifacts
- Use pinned action/image versions

## Don't

- Store secrets in pipeline config files
- Skip security scans for speed
- Use unpinned dependencies in build

## High-Risk Patterns

- Secrets in plain text in workflow files
- `curl | bash` without verification
- Disabled security gates

## Verification Checklist

- [ ] SBOM generation and storage
- [ ] Deployment blocked on failed security scan
- [ ] Signing/verification policy in place
- [ ] Secrets properly masked in logs
