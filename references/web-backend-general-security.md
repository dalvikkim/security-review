---
scope:
  stack: ["web-backend"]
priority: 85
---

# Web Backend General Security

## Secure defaults

- Enable CSRF protection by default.
- Use server-validated sessions.
- Minimize direct filesystem access from request handling.

## Common pitfalls

- Trusting framework defaults without review.
- Leaving debug mode enabled.
- Hardcoding internal IPs/URLs.

## Verification checklist

- [ ] CSRF token verification
- [ ] Debug/dev settings separated from production
- [ ] Session hijacking mitigations considered
