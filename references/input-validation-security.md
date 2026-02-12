---
scope:
  domain: ["input-validation"]
priority: 95
---

# Input Validation

## Secure Defaults

- Prefer allowlist-based validation
- Validate type, length, and format

## Common Attacks

- SQL injection
- Command injection
- Path traversal
- XSS

## Do

- Use centralized validation logic
- Prefer library-based parsing (parameterized queries, schema validation)
- Validate at trust boundaries

## Don't

- Rely on a single regex for all validation
- Trust client-side validation alone
- Pass raw input to sensitive operations

## High-Risk Patterns

- String concatenation in queries
- User input in file paths without normalization
- Unvalidated redirect URLs

## Verification Checklist

- [ ] Input validation present at all entry points
- [ ] No direct use of untrusted input in sensitive operations
- [ ] Allowlist validation preferred over blocklist
