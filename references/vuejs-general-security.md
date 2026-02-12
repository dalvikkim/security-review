---
scope:
  language: ["vue", "vuejs"]
priority: 75
---

# Vue.js Security

## Core Risks

- XSS (especially v-html)
- Token/session storage mistakes (localStorage)
- CORS/CSRF (requires backend coordination)
- Secrets in bundle (build-time env)
- Dependency and build pipeline compromise

## Secure Defaults

- Do not use v-html with untrusted content
- Server enforces auth; front-end supports UX only
- No secrets in front-end bundle
- Keep default template escaping for user input

## Do

- Sanitize on server before using v-html
- Prefer httpOnly cookie-based sessions
- Treat client route guards as UX, not security
- Implement CSP with backend/proxy

## Don't

- Bind user HTML to v-html
- Put API keys in .env included in build
- Store long-lived tokens in localStorage
- Log PII in errors

## High-Risk Patterns

- `<div v-html="userProvidedHtml">`
- Keys visible in bundle (VITE_* misuse)
- Front-end-only access control

## Verification Checklist

- [ ] v-html usage justified and sanitized
- [ ] No secrets in front-end bundle
- [ ] Auth/authz enforced on server
- [ ] Dependency scan in place
