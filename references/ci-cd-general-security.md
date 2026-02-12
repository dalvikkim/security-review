---
scope:
  stack: ["ci-cd"]
priority: 70
---

# CI/CD General Security

## Secure defaults

- Consider build provenance and artifact integrity (signing, attestation).
- Gate on image scanning (vulnerabilities and secrets).
- Use least-privilege, short-lived tokens.
- Mask secrets in build logs.

## Checklist

- [ ] SBOM generation and storage
- [ ] Deployment blocked on failed image scan
- [ ] Signing/verification policy in place
