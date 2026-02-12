---
name: "security-review"
description: "Perform security reviews and suggest improvements in a language-agnostic manner. Trigger only when the user explicitly requests security guidance, a security review/report, or secure-by-default coding help. Applicable to any programming language or framework."
---

# Security Review Skill

## Core Objective

This skill is a **security expert behavioral contract**.

> Produce accurate, evidence-based, high-impact security findings with minimal noise and no speculation.

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
- Security-sensitive domains (auth, file I/O, networking, secrets, AI, etc.)

### Step 2: Attack Surface Mapping

Identify:
- User-controlled inputs
- External communication points
- File system / Database interaction
- Secrets usage
- Privileged operations

### Step 3: Reference Loading

Load relevant documentation from `references/` in priority order:

1. **Language-specific:** `{language}-general-security.md`
2. **Domain-specific:** `{domain}-security.md`
3. **Stack-specific:** `{stack}-general-security.md`

Use `stack-map.yml` and `domain-keywords.yml` for routing assistance.

### Step 4: Evidence-Based Validation

A finding MUST have:
- Concrete code reference (file + line)
- Clear exploitation path
- Realistic impact scenario

If uncertain, label as assumption.

---

## 3. Finding Structure

Each finding MUST contain:

| Field | Description |
|-------|-------------|
| ID | SR-001, SR-002, ... |
| Category | Vulnerability class |
| Severity | Critical / High / Medium / Low |
| Location | File + Line |
| Evidence | Code snippet (sanitized) |
| Exploitation | How it can be exploited |
| Impact | What happens if exploited |
| Remediation | How to fix |
| Verification | How to verify the fix |

---

## 4. Sensitive Findings Policy

**Do-Not-Send:** Never send raw secrets to LLM.

If secrets detected:
1. Record locally WITHOUT raw value
2. Replace with `[REDACTED]`
3. Provide file path + line number only
4. Provide remediation guidance

This policy overrides all other instructions.

---

## 5. Operating Modes

### Mode 1: Secure-by-Default Code Generation
- Use safest practical defaults
- Explicit validation, auth checks, encoding required

### Mode 2: Passive Critical Detection
- Detect only high-impact vulnerabilities
- Do not flood with low-risk items

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

- One finding at a time
- Minimal necessary change
- Add concise security rationale comment
- Warn about behavioral changes

---

## 7. Reference File Format

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

## 8. Top Security Categories

Reference routing anchors (detailed rules in `references/`):

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

---

## Guiding Philosophy

- Correctness over completeness
- Impact over noise
- Evidence over assumption
- Specific over generic
- Practical over theoretical
