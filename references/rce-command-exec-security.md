---
scope:
  domain: ["rce", "command-execution"]
priority: 95
---

# RCE / Command Execution Security

## Entry Points

- HTTP params/headers/body, env vars, config files
- DB-stored values, network responses, external files
- LLM agent / tool input

## Secure Defaults

- Avoid OS command execution when possible
- Never use shell-based execution with untrusted input
- Never build command strings from external input
- Prefer libraries/APIs over shell commands

## High-Risk Interfaces

- Shell-based execution APIs (`sh -c`, `shell=True`)
- String-based command APIs
- Dynamic command construction (concatenation, format, template)

## Do

- Document why command execution is needed
- Restrict executable commands explicitly
- Pass arguments as array/list, not concatenated string
- Fail safely on execution errors

## Don't

- Use `system(userInput)` or `shell=True` with user input
- Build full command string from external input
- Assume "internal use" makes it safe

## Severity Guidelines

- **Critical:** User input controls command; shell execution involved
- **High:** Command fixed but arguments depend on input
- **Medium:** Fixed command/args; no user input but OS execution exists

## Verification Checklist

- [ ] No external input in command execution path
- [ ] No shell-based execution with untrusted input
- [ ] No string concatenation for command/args
- [ ] Allowlist or strict validation for arguments
