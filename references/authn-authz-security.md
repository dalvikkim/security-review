---
scope:
  domain: ["authn-authz"]
priority: 100
---

# Authentication & Authorization

## Core Principles

- Treat authentication and authorization separately
- Enforce authorization on the server; never trust client-only checks
- Do not trust client-supplied identity (e.g., user ID in request params)

## Do

- Use role- or policy-based access control
- Set token expiration
- Centralize permission checks (e.g., middleware)

## Don't

- Rely on front-end-only permission checks
- Trust user ID from request parameters
- Accept client-supplied roles or permissions

## High-Risk Patterns

- Trusting flags such as `isAdmin=true` from client
- Basing authorization on URL or path alone
- Missing authorization checks on sensitive endpoints

## Verification Checklist

- [ ] All sensitive endpoints require authentication
- [ ] Authorization enforced server-side
- [ ] No client-supplied identity trusted
