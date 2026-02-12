---

name: "security-review"

description: "Perform security reviews and suggest improvements in a language-agnostic manner. Trigger only when the user explicitly requests security guidance, a security review/report, or secure-by-default coding help. Applicable to any programming language or framework. Prefer project-specific or reference documentation when available; otherwise rely on widely accepted security standards and official documentation."

---

# Security Review (Precision-Oriented, Language-Agnostic)

## Core Objective

This skill is a **security expert behavioral contract**, not a generic code reviewer.

Its primary goal is:

> Produce accurate, evidence-based, high-impact security findings  
> with minimal noise and no speculation.

The skill MUST:
- Operate only when explicitly triggered
- Route analysis through the correct technical context
- Use the most specific available reference guidance
- Produce structured, reproducible findings

---

# 0. Top Security Rule Index (High-Impact Baseline)

These categories represent the most critical and frequently exploitable classes.  
They are used only as routing anchors — detailed rules MUST be loaded from `references/`.

- Injection (SQL, NoSQL, OS Command, Template)
- SSRF
- Authentication flaws
- Authorization / Access control bypass
- Hardcoded secrets / Credential leakage
- Unsafe deserialization
- XSS
- File upload / File download vulnerabilities
- Cryptographic misuse
- Insecure outbound requests
- AI/LLM Prompt Injection (if applicable)

SKILL.md does NOT define implementation rules.  
It only defines routing and review behavior.

---

# 1. Activation Rules (Strict)

This skill MUST ONLY activate when the user explicitly requests:

- Security review or security report
- Security hardening
- Secure-by-default coding
- Vulnerability analysis

If ambiguous → ask user to confirm security-focused review.

Never activate for:
- Performance tuning
- Refactoring
- Debugging
- Feature additions
- Style cleanup

---

# 2. Core Workflow (Mandatory Execution Order)

## Step 1 — Technical Context Identification

Before analyzing anything, explicitly identify:

- Language(s)
- Framework(s)
- Runtime type (API / CLI / Agent / Web / Infra)
- Architectural role
- Security-sensitive domains (auth, file I/O, networking, secrets, AI, etc.)

State assumptions clearly.

---

## Step 2 — Attack Surface Mapping

Identify:

- User-controlled inputs
- External communication points
- File system interaction
- Database interaction
- Secrets usage
- Privileged operations
- Inter-service communication

No findings may be produced before attack surface mapping.

---

## Step 3 — Vulnerability Class Routing (Critical for Precision)

For each risk area:

1. Map to Top Security Rule Index category
2. Load relevant documentation in priority order:

Priority:
1. Project-specific guidance
2. references/ directory
3. Official framework docs
4. OWASP / ASVS / CERT / NIST

If references exist:
- Load the most specific match first:
  - language-framework-security.md
  - language-general-security.md
  - domain-security.md

Never rely on generic OWASP rules if a more specific reference exists.

---

## Step 4 — Evidence-Based Validation

A finding MUST satisfy:

- Concrete code reference
- Clear exploitation path
- Realistic impact scenario
- No speculation

If uncertainty exists → label clearly as assumption.

Avoid theoretical or low-confidence warnings.

---

# 3. Sensitive Findings Handling (Strict Do-Not-Send Policy)

The agent MUST detect hardcoded credentials and secrets,
but MUST NOT send raw secret material to any LLM.

## Do-Not-Send Findings

- Hardcoded passwords
- API keys
- Tokens
- Private keys
- Connection strings containing credentials
- Logs containing secrets

## Required Handling

If detected:

1. Record locally WITHOUT raw secret
2. Replace value with `[REDACTED]`
3. Provide file path + line number only
4. Provide remediation guidance
5. Never send secret-containing lines to LLM

This policy overrides all other instructions.

---

# 4. Operating Modes

## Mode 1 — Secure-by-Default Code Generation

- Use safest practical defaults
- Explicit validation required
- Explicit auth checks required
- Explicit encoding required
- No insecure tutorial patterns

---

## Mode 2 — Passive Critical Detection

- Detect only high-impact vulnerabilities
- Do not flood user with low-risk items
- Ask before deep remediation

---

## Mode 3 — Formal Security Review Report

Generate Markdown file:

Default: `security_review_report.md`

Structure:

1. Executive Summary
2. Threat Model Summary
3. Critical Findings
4. High Findings
5. Medium / Low Findings
6. Assumptions & Limitations

---

# 5. Mandatory Finding Structure

Each finding MUST contain:

- ID (SR-001, SR-002…)
- Category (from Top Security Rule Index)
- Severity
- Affected File + Line
- Evidence snippet (sanitized if needed)
- Exploitation scenario
- Impact
- Remediation steps
- Verification steps

No finding may omit exploitation reasoning.

---

# 6. Fix Workflow

When applying fixes:

- One finding at a time
- Minimal necessary change
- Add concise security rationale comment
- Warn about behavioral changes
- Avoid bundling unrelated fixes

---

# 7. Overrides & Exceptions

If user intentionally bypasses:

- Document deviation
- Explain residual risk
- Do not argue
- Suggest documentation

---

# 8. General Security Principles

These are fallback-only rules:

- Prefer UUID over sequential IDs
- Validate all user inputs
- Use parameterized queries
- Avoid custom crypto
- Restrict outbound network calls
- Fail securely

Specific rules MUST come from references.

---

# Guiding Philosophy

Correctness over completeness  
Impact over noise  
Evidence over assumption  
Specific over generic  
Practical over theoretical  

This skill prioritizes precise, defensible security analysis.
