---
scope:
  domain: ["rce", "command-execution"]
priority: 95
applies_to:
  - "services executing OS or shell commands"
  - "web applications, APIs, agents, CLIs"
  - "CI/CD scripts and automation"
---

# RCE / Command Execution Security

## Overview

RCE means arbitrary OS commands can be executed (e.g. under attacker influence), leading to full compromise. Applies to any code that runs OS or shell commands.

## Entry points

- HTTP params/headers/body, env vars, config files
- DB-stored values, network responses, external files
- LLM agent / tool input

## Secure defaults

- Avoid OS command execution when possible.
- Never use shell-based execution (e.g. `sh -c`, `shell=True`) with untrusted input.
- Never build command strings from external input.
- Prefer libraries/APIs over shell commands.

## High-risk interfaces

- Shell-based execution APIs
- String-based command APIs
- Dynamic command construction (concatenation, format, template)
- Execution functions that take user-controlled arguments

## Defenses

- **Design:** Remove or minimize command execution; if required, use fixed command + allowlisted arguments only; pass arguments as array/list, not concatenated string.
- **Input:** Never pass user input directly as command arguments; normalize paths and apply allowlist; block flag-like input.
- **Execution:** Use exec APIs that do not invoke shell; minimize privileges; limit time/count.

## Do

- Document why command execution is needed.
- Restrict executable commands explicitly in code.
- Treat external input as data, not as command or arguments.
- Fail safely on execution errors.

## Don't

- Use `system(userInput)`, `exec("sh -c " + input)`, or `shell=True` with user input.
- Build full command string from external input.
- Assume "internal use" makes it safe.

## Severity

- **Critical:** User input controls command or critical arguments; shell execution involved.
- **High:** Command fixed but arguments depend on input; allowlist/validation incomplete.
- **Medium:** Command and arguments fixed; no user input but OS command execution exists.

## Verification checklist

- [ ] No external input in command execution path
- [ ] No shell-based execution with untrusted input
- [ ] No string concatenation for command/args
- [ ] Allowlist or strict validation for arguments
- [ ] Alternative to OS command considered
