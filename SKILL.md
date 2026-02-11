---

name: "security-review"

description: "Perform security reviews and suggest improvements in a language-agnostic manner. Trigger only when the user explicitly requests security guidance, a security review/report, or secure-by-default coding help. Applicable to any programming language or framework. Prefer project-specific or reference documentation when available; otherwise rely on widely accepted security standards and official documentation."

---

# Security Review (Language-Agnostic)

## Overview

This skill defines how to perform **security analysis independently of programming language or framework**.

The goal of this skill is to ensure that the assistant:
- Acts only when explicitly asked for security guidance
- Identifies the relevant technical context (language, framework, stack, domain)
- Applies the most relevant and authoritative security guidance available
- Produces clear, prioritized, and actionable security feedback

This skill may be used to:
- Write new code that is secure by default
- Passively identify high-impact security issues
- Produce a formal security review report with suggested fixes

---

## Sensitive Findings Handling (Strict Do-Not-Send Policy)

The agent MUST detect hardcoded credentials and secrets, but MUST NOT send any secret material
(or secret-containing findings) to the LLM.

### Definition: Do-Not-Send Findings
Treat the following as Do-Not-Send findings:
- Hardcoded credentials (passwords, tokens, API keys, private keys, client secrets)
- Embedded secrets in connection strings (db urls with credentials)
- Any finding that includes a raw secret value, even if masked is possible

### Required Behavior
When a Do-Not-Send finding is detected:
1) Record the finding locally (file/report/log) WITHOUT including the raw secret value
2) Provide user-visible guidance (what/where/how to fix) with redacted value
3) Do NOT include the finding in any prompt to the LLM
4) If summarizing to the user, include only:
   - file path
   - line number
   - secret type (e.g., password/token)
   - safe remediation steps
   - NEVER the secret value

### Redaction Rule (if any metadata must be shared)
- Replace secret values with "[REDACTED]"
- Never send full lines containing secrets to the LLM
- Never send stack traces or logs that might contain secrets

---

## Activation Rules (Strict)

This skill MUST ONLY activate when the user explicitly requests one of the following:
- Security review or security report
- Secure-by-default design or coding guidance
- Hardening or security improvement of an existing codebase

This skill MUST NOT activate for:
- General code review
- Debugging or error fixing
- Performance optimization
- Refactoring without a security focus
- Feature development without an explicit security request

If the request is ambiguous, ask the user to confirm whether a **security-focused review** is desired before proceeding.

---

## Core Workflow

### Step 1: Identify Technical Context

Before performing any analysis, identify **all relevant technical dimensions**, including:

- Programming language(s)
- Framework(s) or libraries
- Runtime environment (web app, API, CLI, batch job, mobile, etc.)
- Architectural role (frontend, backend, infrastructure, CI/CD, agent, etc.)
- Domain concerns (authentication, secrets, file handling, networking, AI/LLM usage, etc.)

If the language or framework is unclear:
- Inspect the repository or provided files
- List the evidence used to infer the stack
- Clearly state any assumptions

---

### Step 2: Load Relevant Security Guidance (Priority Order)

Security guidance MUST be applied using the following priority order:

1. **Project-provided or organization-specific guidance**
2. **Skill references directory**, if available, using the following matching order:
   - `<language>-<framework>-<stack>-security.md`
   - `<language>-general-<stack>-security.md`
   - `<stack>-general-security.md`
   - `<domain>-security.md`
3. **Official documentation** for the language/framework
4. **Widely accepted security standards**, such as:
   - OWASP Top 10
   - OWASP ASVS
   - CERT Secure Coding Guidelines
   - NIST recommendations

If no concrete guidance exists:
- Proceed cautiously
- Focus on high-confidence, high-impact security issues
- Explicitly state that guidance is based on general security principles

---

## Operating Modes

This skill operates in three modes.

### Mode 1: Secure-by-Default Code Generation

When writing new code:
- Default to secure configurations and patterns
- Avoid insecure defaults even if commonly seen in examples
- Prefer explicit validation, authentication, and authorization
- Clearly document any security-sensitive decisions

---

### Mode 2: Passive Vulnerability Detection

While working on code:
- Passively detect **critical or high-impact security issues**
- Do not overwhelm the user with low-risk or speculative findings
- Notify the user and ask whether they want to proceed with fixes

---

### Mode 3: Security Review Report

When explicitly requested:
- Produce a full security review report
- Prioritize findings by severity and urgency
- Suggest concrete remediation steps
- Offer to apply fixes incrementally

---

## Report Requirements

When producing a report:

- Write the report as a Markdown file  
  Default filename: `security_review_report.md`
- Ask the user for a preferred location if not specified

### Report Structure

1. **Executive Summary**
2. **Critical Findings**
3. **High Severity Findings**
4. **Medium / Low Severity Findings**
5. **Notes, Assumptions, and Limitations**

### Finding Requirements

Each finding MUST include:
- A numeric ID
- Severity level
- A short description
- A one-sentence impact statement for Critical findings
- Code references with line numbers (if applicable)
- Clear remediation guidance

After writing the report:
- Summarize the key findings to the user
- Clearly state where the report was written

---

## Fixes Workflow

If fixes are requested:

- Address **one finding at a time**
- Add concise comments explaining:
  - Which security review is being applied
  - Why the previous approach was risky
- Carefully consider backward compatibility and regressions
- Warn the user if behavior or assumptions may change
- Follow the project’s existing commit and testing workflows

Avoid bundling unrelated security fixes into a single change.

---

## Overrides and Exceptions

Projects may intentionally bypass certain security review due to:
- Legacy constraints
- Operational requirements
- Environmental limitations

When encountering an override:
- Do not argue with the user
- Clearly document the deviation and its rationale
- Suggest adding documentation to avoid future confusion

---

## General Security Principles (Language-Agnostic)

### Avoid Predictable Public Identifiers

Do not expose auto-incrementing or sequential IDs in public interfaces.  
Prefer UUIDv4 or cryptographically random identifiers.

---

### TLS Considerations

- Do not report missing TLS as a vulnerability by default
- Many development and internal environments rely on upstream TLS termination
- Secure cookies must only be enabled when TLS is guaranteed
- Avoid recommending HSTS unless the user explicitly understands and requests it

---

## Guiding Philosophy

This skill is not a vulnerability scanner.

It is a **security expert behavioral contract** that prioritizes:
- Correctness over completeness
- High-impact issues over noise
- Practical remediation over theoretical perfection
- Respect for real-world project constraints
