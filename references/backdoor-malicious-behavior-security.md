---
scope:
  domain: ["backdoor", "malicious-behavior"]
priority: 90
---

# Backdoor / Malicious Behavior Detection

## Overview

Identifies suspicious patterns by **observable structure and behavior**, not intent.

## Entry Points

- Hidden HTTP params/headers/cookies, magic-value conditionals
- Code paths disguised as debug/admin
- Env or config-based hidden flags
- LLM agent/tool input

## Secure Defaults

- No hidden features; admin/debug paths require explicit auth
- Document any conditional sensitive behavior
- Minimize and trace network/OS access

## High-Risk Behaviors

- Auth/authz bypass when specific value present
- Undocumented admin endpoints
- Conditional OS command execution
- Hidden outbound communication
- Obfuscation or dynamic loading

## Do

- Separate admin/ops features clearly
- Include security-sensitive branches in code review
- Add clear comments for suspicious-looking code

## Don't

- Use `if (password == "magic")` style bypass
- Expose undocumented admin endpoints
- Enable features via hidden header/param only
- Communicate with undisclosed external servers

## Severity Guidelines

- **Critical:** Auth bypass; hidden RCE; hidden exfiltration
- **High:** Unexplained OS execution; undocumented admin features
- **Medium:** Obfuscation/dynamic loading; leftover debug code

## Verification Checklist

- [ ] No undocumented admin/debug features
- [ ] No auth/authz bypass conditions
- [ ] No conditional OS command execution without justification
- [ ] Outbound communication clearly defined
