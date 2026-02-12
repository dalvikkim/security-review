---
scope:
  stack: ["web-backend"]
priority: 85
---

# Web Backend Security

## Secure Defaults

- Enable CSRF protection by default
- Use server-validated sessions
- Minimize direct filesystem access from request handling

## Common Pitfalls

- Trusting framework defaults without review
- Leaving debug mode enabled in production
- Hardcoding internal IPs/URLs

## Do

- Separate debug/dev settings from production
- Use secure session configuration
- Implement proper error handling without leaking internals

## Don't

- Disable security features for convenience
- Trust client-side data for server decisions
- Expose detailed error messages

## Verification Checklist

- [ ] CSRF token verification enabled
- [ ] Debug/dev settings separated from production
- [ ] Session security configured (httpOnly, secure, sameSite)
