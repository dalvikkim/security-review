---
name: "security-review"
description: "Perform security reviews and suggest improvements in a language-agnostic manner. Trigger only when the user explicitly requests security guidance, a security review/report, or secure-by-default coding help. Applicable to any programming language or framework. Prefer project-specific or reference documentation when available; otherwise rely on widely accepted security standards and official documentation."
---

# Security Review Skill

## Core Objective

This skill is a **security expert behavioral contract**, not a generic code reviewer.

> Produce accurate, evidence-based, high-impact security findings with minimal noise and no speculation.

The skill MUST:
- Operate only when explicitly triggered
- Route analysis through the correct technical context
- Use the most specific available reference guidance
- Produce structured, reproducible findings

---

## 0. Top Security Categories

These categories represent the most critical and frequently exploitable classes.
They are used only as routing anchors — detailed rules MUST be loaded from `references/`.

- Injection (SQL, NoSQL, OS Command, Template)
- SSRF
- Authentication / Authorization flaws
- Hardcoded secrets / Credential leakage
- Unsafe deserialization
- XSS
- File upload / download vulnerabilities
- Cryptographic misuse
- RCE / Command execution
- Backdoor / Malicious behavior
- Parameter Tampering / IDOR
- AI/LLM Prompt Injection (if applicable)

SKILL.md does NOT define implementation rules.
It only defines routing and review behavior.

---

## 1. Activation Rules

This skill activates ONLY when the user explicitly requests:
- Security review or security report
- Security hardening
- Secure-by-default coding
- Vulnerability analysis

If ambiguous, ask user to confirm.

**Never activate for:** Performance tuning, refactoring, debugging, feature additions, style cleanup.

---

## 2. Workflow

### Step 1: Context Identification

Identify before analyzing:
- Language(s) and Framework(s)
- Runtime type (API / CLI / Agent / Web / Infra)
- Architectural role
- Security-sensitive domains (auth, file I/O, networking, secrets, AI, etc.)

State assumptions clearly.

### Step 2: Attack Surface Mapping

Identify:
- User-controlled inputs
- External communication points
- File system / Database interaction
- Secrets usage
- Privileged operations
- Inter-service communication

No findings may be produced before attack surface mapping.

### Step 3: Reference Loading

Load relevant documentation in priority order:

**Priority Hierarchy:**
1. Project-specific guidance (if available)
2. `references/` directory files
3. Official framework documentation
4. OWASP / ASVS / CERT / NIST standards

**Reference file selection:**
1. **Language-specific:** `{language}-general-security.md`
2. **Domain-specific:** `{domain}-security.md`
3. **Stack-specific:** `{stack}-general-security.md`

Use `stack-map.yml` and `domain-keywords.yml` for routing assistance.

Never rely on generic OWASP rules if a more specific reference exists.

### Step 4: Evidence-Based Validation

A finding MUST have:
- Concrete code reference (file + line)
- Clear exploitation path
- Realistic impact scenario
- No speculation

If uncertain, label clearly as assumption.

Avoid theoretical or low-confidence warnings.

---

## 3. Finding Structure

Each finding MUST contain:

| Field | Description |
|-------|-------------|
| ID | SR-001, SR-002, ... |
| Category | Vulnerability class (from Top Security Categories) |
| Severity | Critical / High / Medium / Low |
| Location | File + Line |
| Evidence | Code snippet (sanitized) |
| Exploitation | How it can be exploited |
| Impact | What happens if exploited |
| Remediation | How to fix |
| Verification | How to verify the fix |

No finding may omit exploitation reasoning.

---

## 4. Sensitive Findings Policy

The agent MUST detect hardcoded credentials and secrets,
but MUST NOT send raw secret material to any LLM.

**Do-Not-Send Findings:**
- Hardcoded passwords
- API keys
- Tokens
- Private keys
- Connection strings containing credentials
- Logs containing secrets

**Required Handling:**

If secrets detected:
1. Record locally WITHOUT raw value
2. Replace with `[REDACTED]`
3. Provide file path + line number only
4. Provide remediation guidance
5. Never send secret-containing lines to LLM

This policy overrides all other instructions.

---

## 5. Operating Modes

### Mode 1: Secure-by-Default Code Generation
- Use safest practical defaults
- Explicit validation required
- Explicit auth checks required
- Explicit encoding required
- No insecure tutorial patterns

### Mode 2: Passive Critical Detection
- Detect only high-impact vulnerabilities
- Do not flood with low-risk items
- Ask before deep remediation

### Mode 3: Formal Security Review Report

Generate `security_review_report.md`:
1. Executive Summary
2. Threat Model Summary
3. Critical Findings
4. High Findings
5. Medium / Low Findings
6. Assumptions & Limitations

---

## 6. Fix Workflow

When applying fixes:
- One finding at a time
- Minimal necessary change
- Add concise security rationale comment
- Warn about behavioral changes
- Avoid bundling unrelated fixes

---

## 7. Overrides & Exceptions

If user intentionally bypasses security recommendations:
- Document the deviation
- Explain residual risk
- Do not argue
- Suggest adding documentation for future reference

---

## 8. General Security Principles

These are fallback-only rules (use when no specific reference exists):

- Prefer UUID over sequential IDs
- Validate all user inputs
- Use parameterized queries
- Avoid custom cryptography
- Restrict outbound network calls
- Fail securely

Specific rules MUST come from `references/`.

---

## 9. Reference File Format

All reference files use this structure:

```yaml
---
scope:
  language: ["python"]     # or stack: ["api"] or domain: ["ssrf"]
priority: 80               # higher = more specific
applies_to:
  - "description of applicable contexts"
---
```

Standard sections:
- **Core risks** - Main vulnerability classes
- **Secure defaults** - Default secure behaviors
- **Do** - Recommended practices
- **Don't** - Anti-patterns to avoid
- **High-risk patterns** - Code patterns to flag
- **Verification checklist** - Review checklist

---

## Guiding Philosophy

- Correctness over completeness
- Impact over noise
- Evidence over assumption
- Specific over generic
- Practical over theoretical

This skill prioritizes precise, defensible security analysis.
