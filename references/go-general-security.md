---
scope:
  language: ["go"]
priority: 80
applies_to:
  - "api services"
  - "microservices"
  - "cli tools"
---

# Go General Security

## Core risks

- Missing input validation → injection, SSRF, path traversal
- Template/HTML mistakes (XSS when bypassing escaping)
- Command injection via os/exec
- File/path handling mistakes
- Dependency and supply-chain

## Secure defaults

- Use structs and validation for input.
- Use parameterized queries for DB.
- **Outbound:** Timeout required; limit redirects; consider allowlist.
- Use os/exec with argument list only; avoid shell.
- Prefer html/template (auto-escape); do not use text/template for HTML.

## Do

- Use context.WithTimeout for outbound requests.
- Use filepath.Clean and restrict to under root for file paths.
- Minimize internal details in error messages (separate logging from client response).
- Inject secrets via env/secret store; mask in logs.

## Don't

- Use http.Get(userURL) without validation (SSRF).
- Use exec.Command("sh", "-c", userInput) (shell injection).
- Generate HTML with text/template.
- Log tokens or PII in debug output.

## High-risk patterns

- Default client following redirects with access to internal network
- Download feature without path/filename validation
- Returning raw errors to client (internal structure exposure)

## Verification checklist

- [ ] Input validation (length/range/format) present
- [ ] Outbound requests have timeout/allowlist/redirect limits
- [ ] os/exec used without shell invocation
- [ ] HTML via html/template without escaping bypass
- [ ] go.mod and dependency scan process in place
