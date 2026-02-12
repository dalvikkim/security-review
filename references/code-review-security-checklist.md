# Code Review – Security Checklist

## Authentication / Authorization

- [ ] All sensitive APIs require authentication
- [ ] No missing authorization checks

## Input handling

- [ ] Input validation present
- [ ] No direct use of untrusted input in sensitive operations

## Secrets

- [ ] No hardcoded secrets
- [ ] No secrets in logs

## Files

- [ ] Uploaded files validated
- [ ] Path manipulation prevented
