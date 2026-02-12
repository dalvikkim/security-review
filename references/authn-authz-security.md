---
scope:
  domain: ["authn-authz"]
priority: 100
---

# Authentication & Authorization

## Core principles

- Treat authentication and authorization separately.
- Enforce authorization on the server; never trust client-only checks.
- Do not trust client-supplied identity (e.g. user ID in request params).

## Do

- Use role- or policy-based access control.
- Set token expiration.
- Centralize permission checks (e.g. middleware).

## Don't

- Rely on front-end-only permission checks.
- Trust user ID from request parameters.

## High-risk patterns

- Trusting flags such as `isAdmin=true` from the client.
- Basing authorization on URL or path alone.
