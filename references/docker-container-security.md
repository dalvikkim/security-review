---
scope:
  stack: ["container"]
  domain: ["container-build"]
priority: 95
---

# Docker / Container Security

## Review Targets

- Dockerfile: FROM, RUN, COPY/ADD, USER, ENTRYPOINT/CMD
- Base image choice and pinning
- Secrets and build args
- File permissions and ownership

## Critical Issues

**Running as root**
- Rule: Final image must run as non-root
- Detect: No USER or `USER root` in final stage
- Fix: Add dedicated user/group and set USER

**Secrets in image layers**
- Rule: Never embed secrets in Dockerfile layers
- Detect: ENV/ARG/RUN with tokens/passwords/keys
- Fix: Use BuildKit secrets, runtime env, or secret stores

**Unpinned base image**
- Rule: Pin to digest or fixed version
- Detect: `FROM image:latest` or no tag
- Fix: Use `image@sha256:<digest>` or explicit version

## High Severity Issues

**Unsafe package installs**
- Minimize packages; remove package manager caches
- Use multi-stage builds; clean apt lists after install

**ADD instead of COPY**
- Prefer COPY unless ADD behavior required

**curl | bash**
- Never pipe remote scripts to shell without verification
- Download, verify checksum/signature, pin version

## Medium Severity Issues

**File permissions**
- Use COPY --chown=user:group
- Apply minimal chmod

## Verification Checklist

- [ ] Final stage runs as non-root
- [ ] No secrets in ARG/ENV/RUN/COPY
- [ ] Base image pinned (digest or version)
- [ ] No unverified curl|bash
- [ ] Multi-stage build used
- [ ] Package caches cleaned
