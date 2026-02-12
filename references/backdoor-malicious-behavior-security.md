---
scope:
  domain: ["backdoor", "malicious-behavior"]
priority: 90
applies_to:
  - "any application handling untrusted input"
  - "services with OS or network access"
  - "agents, plugins, CI/CD scripts"
---

# Backdoor / Malicious Behavior Security

## Overview

Backdoor or malicious behavior means code that enables unauthorized access, hidden control, data exfiltration, or remote execution. This document identifies suspicious patterns by **observable structure and behavior**, not by intent. Applies to any code that touches OS or network.

## Entry points

- Hidden HTTP params/headers/cookies, magic-value conditionals
- Code paths disguised as debug/admin
- Env or config-based hidden flags
- LLM agent/tool input, CI/CD scripts

## Secure defaults

- No hidden features; admin/debug paths require explicit auth/authz.
- Document any conditional sensitive behavior.
- Minimize and trace network/OS access.

## High-risk behaviors

- Bypassing auth/authz when a specific value is present
- Undocumented admin endpoints
- Conditional OS command execution
- Hidden outbound communication
- Obfuscation or dynamic loading to hide behavior

## Defenses

- **Auth:** Require explicit auth for all admin/maintenance features; use role/policy-based access; protect "debug-only" in production.
- **Transparency:** Remove hidden conditions (magic strings, flags); document activation in config/docs; comment security-sensitive branches.
- **Execution/network:** Follow RCE policy for command execution; allowlist outbound calls; log/audit unusual traffic.

## Do

- Separate admin/ops features clearly.
- Include security-sensitive branches in code review.
- Test activation paths for conditional features.
- Add clear comments and rationale for suspicious-looking code.

## Don't

- Use `if (password == "magic")`-style bypass.
- Expose undocumented admin endpoints.
- Enable features via hidden header/param only.
- Communicate with undisclosed external servers.
- Leave "convenience" debug code in production.

## Severity

- **Critical:** Auth/authz bypass; hidden remote command execution; hidden exfiltration.
- **High:** Unexplained OS command execution; undocumented admin features; conditional egress.
- **Medium:** Obfuscation/dynamic loading; leftover debug code.

## Verification checklist

- [ ] No undocumented admin/debug features
- [ ] No auth/authz bypass conditions
- [ ] No conditional OS command execution without justification
- [ ] Outbound communication clearly defined
- [ ] No obfuscation or dynamic code execution without justification
