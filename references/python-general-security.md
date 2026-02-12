---
scope:
  language: ["python"]
priority: 80
applies_to:
  - "web backends"
  - "automation/agents"
  - "data pipelines"
---

# Python General Security

## Core risks

- Injection: SQL/NoSQL, command, template
- Unsafe deserialization (pickle)
- SSRF and unchecked outbound requests
- Secrets in logs/env/config
- Supply-chain (typosquatting, vulnerable versions)

## Secure defaults

- Use input validation/schema (e.g. pydantic) to define trust boundaries.
- Use parameterized queries or ORM for DB.
- Minimize OS commands; if needed, allowlist + argument list (no shell).
- Avoid pickle and yaml.load(unsafe); mask tokens/passwords in logging.

## Do

- **SQL:** Use bound parameters (?, %s, named params).
- **Outbound:** Allowlist, timeout, redirect limits, response size limits.
- **Files:** Separate upload/download paths; normalize paths; validate extension and content.
- **Packages:** Pin versions, lockfile, updates, SBOM/Dependabot or similar scans.

## Don't

- Use eval/exec on user input.
- Use subprocess with shell=True and user input.
- Use pickle.loads on untrusted data.
- Log or expose secrets in exceptions.

## High-risk patterns

- requests.get(user_url) (SSRF)
- os.system("cmd " + user) or subprocess.run(user_string, shell=True)
- Raw HTML in templates (bypassing framework escaping)
- Debug mode left enabled

## Verification checklist

- [ ] No direct use of untrusted input in DB/OS/templates
- [ ] No pickle or unsafe YAML loading
- [ ] HTTP clients have timeout/allowlist/redirect limits
- [ ] No secrets in code/logs/repo
- [ ] Dependencies pinned and scan/update process in place
