---
scope:
  language: ["vuejs"]
priority: 75
applies_to:
  - "web frontends built with Vue"
  - "SPAs handling auth/session"
---

# Vue.js (Frontend) Security

## Core risks

- XSS (especially v-html)
- Token/session storage mistakes (e.g. localStorage)
- CORS/CSRF (cannot be solved by front-end alone)
- Secrets in bundle (e.g. build-time env)
- Dependency and build pipeline compromise

## Secure defaults

- Do not insert raw HTML by default; use v-html only for sanitized or trusted content.
- Server enforces auth; front-end supports UX only.
- No secrets in front-end bundle.
- Keep default template escaping for user input.

## Do

- If v-html needed: sanitize on server or use trusted source only.
- Prefer httpOnly cookie-based session (design with backend).
- Treat client route guards as UX, not security.
- Implement CSP with backend/proxy.

## Don't

- Bind user or external HTML to v-html.
- Put API key/secret in .env and include in front-end build.
- Assume long-lived tokens in localStorage are acceptable.
- Log or display PII in errors.

## High-risk patterns

- <div v-html="userProvidedHtml">
- Keys visible in bundle (e.g. VITE_* misuse)
- Assuming "front-end blocks it" is enough (missing server-side auth/validation)

## Verification checklist

- [ ] v-html usage has sanitization or trusted-source justification
- [ ] No secrets in front-end bundle
- [ ] Auth/authz enforced on server
- [ ] Dependency scan and update process in place
