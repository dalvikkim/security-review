---
scope:
  stack: ["container", "ci-cd"]
  domain: ["container-build", "supply-chain", "secrets-management"]
priority: 95
applies_to:
  - "Dockerfile"
  - "container image build pipelines"
---

# Docker / Dockerfile Security Best Practices

## What to Review (Targets)

- Dockerfile instructions: `FROM`, `RUN`, `COPY/ADD`, `USER`, `ENTRYPOINT/CMD`
- Base image choice and pinning
- Secrets and build args
- File permissions and ownership
- Runtime hardening (non-root, minimal packages)
- Supply-chain controls (integrity, provenance)

---

## Critical Findings (Must Fix)

### 1. Running as root (missing USER)
**Rule:** Final image must run as a non-root user.  
**Detect:** No `USER` instruction (or `USER root`) in the final stage.  
**Fix:** Create a dedicated user/group and set `USER <uid|name>`.

### 2. Secrets baked into image layers
**Rule:** Never embed secrets in Dockerfile layers.  
**Detect:** `ENV`, `ARG`, `RUN` containing tokens/passwords/keys; `.env` copied into image.  
**Fix:** Use build-time secrets (BuildKit secrets), runtime env injection, secret stores.

### 3. Unpinned base images / latest tag
**Rule:** Pin base images to immutable digests or at least fixed versions.  
**Detect:** `FROM image:latest` or no tag.  
**Fix:** Use `image@sha256:<digest>` or explicit version tags.

---

## High Severity Findings

### 4. Unsafe package installs & cache bloat
**Rule:** Minimize packages, avoid leaving package manager caches.  
**Detect:** `apt-get install` without cleanup; unnecessary build tools in runtime stage.  
**Fix:** `apt-get update && apt-get install ... && rm -rf /var/lib/apt/lists/*` + multi-stage builds.

### 5. Using ADD unnecessarily
**Rule:** Prefer `COPY` over `ADD` unless you need its special behaviors.  
**Detect:** `ADD` used for local files.  
**Fix:** Replace with `COPY`.

### 6. curl | bash / remote script execution
**Rule:** Never pipe remote scripts to shell without verification.  
**Detect:** `curl ... | sh` / `wget ... | bash`.  
**Fix:** Download with checksum/signature verification, pin versions, verify publisher.

---

## Medium Severity Findings

### 7. Weak file permissions / ownership
**Rule:** Ensure correct ownership and least privilege for app files.  
**Detect:** `COPY` without `--chown` when non-root user is used; world-writable paths.  
**Fix:** `COPY --chown=user:group ...` and `chmod` minimal.

### 8. Excessive capabilities / privileged runtime assumptions
**Rule:** Dockerfile should not assume privileged runtime.  
**Detect:** instructions that hint at privileged needs (e.g., manipulating kernel/network) without docs.  
**Fix:** Document required capabilities; prefer non-privileged design.

---

## Verification Checklist (Dockerfile)

- [ ] Final stage runs as non-root (`USER` set)
- [ ] No secrets in `ARG/ENV/RUN/COPY`
- [ ] Base image pinned (digest or fixed version)
- [ ] No `curl|bash` without verification
- [ ] Multi-stage build used (build deps not in runtime)
- [ ] Package manager caches cleaned
- [ ] File permissions/ownership minimized