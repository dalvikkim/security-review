---
scope:
  language: ["go"]
priority: 80
---

# Go Security

## Core Risks

- Missing input validation (injection, SSRF, path traversal)
- Template/HTML mistakes (XSS)
- Command injection via os/exec
- File/path handling mistakes
- Dependency and supply-chain

## Secure Defaults

- Use structs and validation for input
- Use parameterized queries for DB
- Use os/exec with argument list only (no shell)
- Prefer html/template (auto-escape) over text/template

## Do

- Use `context.WithTimeout` for outbound requests
- Use `filepath.Clean` and restrict to root directory
- Separate logging from client error responses
- Inject secrets via env/secret store

## Don't

- Use `http.Get(userURL)` without validation
- Use `exec.Command("sh", "-c", userInput)`
- Generate HTML with text/template
- Log tokens or PII

## High-Risk Patterns

- Default client following redirects to internal network
- Download without path/filename validation
- Returning raw errors to client

## Verification Checklist

- [ ] Input validation present
- [ ] Outbound requests have timeout/allowlist
- [ ] os/exec used without shell
- [ ] HTML via html/template only
- [ ] go.mod and dependency scan in place
