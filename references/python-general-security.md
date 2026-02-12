---
scope:
  language: ["python"]
priority: 80
---

# Python Security

## Core Risks

- Injection: SQL/NoSQL, command, template
- Unsafe deserialization (pickle, yaml.load)
- SSRF and unchecked outbound requests
- Secrets in logs/env/config
- Supply-chain (typosquatting, vulnerable versions)

## Secure Defaults

- Use input validation/schema (pydantic) for trust boundaries
- Use parameterized queries or ORM for DB
- Minimize OS commands; use argument list (no shell)
- Avoid pickle and unsafe yaml.load

## Do

- Use bound parameters for SQL (?, %s, named params)
- Set timeout, allowlist, redirect limits for HTTP clients
- Normalize file paths; validate extension and content
- Pin versions, use lockfile, run dependency scans

## Don't

- Use `eval`/`exec` on user input
- Use `subprocess` with `shell=True` and user input
- Use `pickle.loads` on untrusted data
- Log secrets or include in exceptions

## High-Risk Patterns

- `requests.get(user_url)` without validation
- `os.system("cmd " + user)`
- `subprocess.run(user_string, shell=True)`
- Debug mode left enabled in production

## Verification Checklist

- [ ] No untrusted input in DB/OS/templates
- [ ] No pickle or unsafe YAML loading
- [ ] HTTP clients have timeout/allowlist
- [ ] No secrets in code/logs
- [ ] Dependencies pinned and scanned
