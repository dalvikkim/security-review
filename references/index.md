# Security Best Practices Reference Index

이 문서는 `references/` 디렉토리의 **진입점(index)** 입니다.

Agent는 이 문서를 참고하여:
- 어떤 문서가 어떤 상황에 적합한지
- 어떤 순서로 문서를 로드해야 하는지
를 판단합니다.

---

## Core Stack Guides (우선)

- stacks/api-general-security.md
- stacks/web-backend-general-security.md

---

## Core Domain Guides (MVP)

- domains/authn-authz-security.md
- domains/input-validation-security.md
- domains/secrets-management-security.md
- domains/file-upload-download-security.md
- domains/ssrf-security.md

---

## Checklists

- checklists/code-review-security-checklist.md

---

## Usage Notes

- Stack 가이드는 **전체 구조의 기본값**
- Domain 가이드는 **취약점/기능 단위의 세부 규칙**
- 둘이 충돌할 경우 **Domain 가이드 우선**
