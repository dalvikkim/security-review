---
scope:
  stack: ["container", "ci-cd"]
  domain: ["container-build", "supply-chain", "secrets-management"]
priority: 95
applies_to:
  - "Dockerfile"
  - "container image build pipelines"
---

# Docker / Container Security

## Review targets

- Dockerfile: FROM, RUN, COPY/ADD, USER, ENTRYPOINT/CMD
- Base image choice and pinning
- Secrets and build args
- File permissions and ownership
- Non-root runtime, minimal packages
- Supply-chain (integrity, provenance)

## Critical (must fix)

**1. Running as root**  
Rule: Final image must run as non-root.  
Detect: No USER or USER root in final stage.  
Fix: Add dedicated user/group and set USER.

**2. Secrets in image layers**  
Rule: Never embed secrets in Dockerfile layers.  
Detect: ENV/ARG/RUN with tokens/passwords/keys; .env copied.  
Fix: Use BuildKit secrets, runtime env, or secret stores.

**3. Unpinned base / latest**  
Rule: Pin to digest or fixed version.  
Detect: FROM image:latest or no tag.  
Fix: Use image@sha256:<digest> or explicit version.

## High severity

**4. Unsafe package installs**  
Rule: Minimize packages; remove package manager caches.  
Detect: apt-get install without cleanup; build tools in final stage.  
Fix: Multi-stage build; rm -rf /var/lib/apt/lists/* after install.

**5. ADD instead of COPY**  
Rule: Prefer COPY unless ADD behavior is required.  
Fix: Use COPY for local files.

**6. curl | bash**  
Rule: Never pipe remote scripts to shell without verification.  
Detect: curl ... | sh, wget ... | bash.  
Fix: Download, verify checksum/signature, pin version.

## Medium severity

**7. File permissions**  
Rule: Correct ownership and least privilege.  
Fix: COPY --chown=user:group; minimal chmod.

**8. Privileged assumptions**  
Rule: Do not assume privileged runtime.  
Fix: Document required capabilities; prefer non-privileged design.

## Verification checklist

- [ ] Final stage runs as non-root (USER set)
- [ ] No secrets in ARG/ENV/RUN/COPY
- [ ] Base image pinned (digest or version)
- [ ] No unverified curl|bash
- [ ] Multi-stage build (build deps not in runtime)
- [ ] Package caches cleaned
- [ ] Permissions/ownership minimized
