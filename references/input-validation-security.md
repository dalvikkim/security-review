---
scope:
  domain: ["input-validation"]
priority: 95
---

# Input Validation

## Secure defaults

- Prefer allowlist-based validation.
- Validate type, length, and format.

## Common attacks

- SQL injection
- Command injection
- Path traversal

## Do

- Use centralized validation logic.
- Prefer library-based parsing (e.g. parameterized queries, schema validation).

## Don't

- Rely on a single regex for all validation.
- Trust client-side validation alone.
