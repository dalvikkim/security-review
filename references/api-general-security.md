---
scope:
  stack: ["api"]
priority: 90
---

# API Security

## Secure Defaults

- Require authentication for all APIs by default
- Exempt only explicitly designated public endpoints
- Validate input with JSON schema or DTOs

## Do

- Apply rate limiting
- Minimize auth failure message details
- Keep OpenAPI docs in sync with implementation

## Don't

- Expose write APIs without authentication
- Expose internal stack details in error messages
- Use request body directly in DB/OS calls without validation

## High-Risk Patterns

- Missing authentication middleware
- Verbose error responses with stack traces
- Direct database queries with request parameters

## Verification Checklist

- [ ] Unauthenticated endpoints identified and justified
- [ ] Input type/range validation in place
- [ ] Response data minimized (no over-exposure)
