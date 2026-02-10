---
scope:
  domain: ["secrets-management"]
applies_to:
  - "applications handling credentials, tokens, API keys"
  - "CI/CD pipelines and automation"
  - "cloud and on-prem services"
priority: 100
---

# Secrets Management Security

## Overview

Secrets(비밀정보)는 노출 즉시 **시스템 전체가 침해될 수 있는 단일 실패 지점**이 됩니다.  
이 문서는 언어와 프레임워크에 관계없이 적용 가능한 **Secrets 관리 보안 원칙**을 정의합니다.

---

## What Counts as a Secret

다음 항목은 모두 Secret으로 취급해야 합니다.

- API Keys / Tokens
- Passwords / Credentials
- OAuth Client Secrets
- Private Keys (SSH, JWT signing keys)
- Cloud Access Keys
- Database connection strings (with credentials)

---

## Secure Defaults

- Secret은 **소스코드, 설정 파일, 이미지에 절대 포함하지 않는다**
- 실행 시점(Runtime)에만 주입
- 최소 권한 원칙(Least Privilege) 적용
- 유효 기간(expiration)과 rotation 고려

---

## Recommended Practices

### Storage
- 환경 변수 (임시 / 로컬 개발)
- Secret Manager 사용
  - Vault, AWS Secrets Manager, Azure Key Vault, GCP Secret Manager
- Kubernetes Secret (단, 암호화 여부 확인)

### Access Control
- Secret 접근 권한 최소화
- 서비스별 Secret 분리
- 읽기 전용 권한 우선

### Rotation
- 자동 또는 반자동 rotation 설계
- 장기 고정(static) secret 지양

---

## Do

- Secret 접근을 코드 레벨에서 추상화
- CI/CD 로그 마스킹 처리
- Secret 사용 실패 시 명확하지만 정보 최소화된 에러 반환

---

## Don't

- Git에 `.env`, `.pem`, `.key` 커밋
- Secret을 URL, Query String에 포함
- 로그/에러 메시지에 Secret 출력
- 하나의 Secret을 여러 시스템에서 공유

---

## Handling Secrets in Automation / LLM Workflows

- Never send secret values to external services or LLMs.
- If analysis is needed, send only redacted metadata (path, line, type).
- Prefer local-only detection and remediation guidance for any secret findings.

---

## High-Risk Patterns

- `API_KEY=xxxx` 형태 문자열이 코드에 존재
- `.env` 파일이 저장소에 포함됨
- Dockerfile에 `ENV SECRET=...`
- 테스트용 Secret을 운영 환경에서 재사용

## Empty Password / Blank Credential (Detection Rule)

빈 문자열 `""` 또는 공백만 있는 문자열로 password/secret/credential 값을 초기화하거나 전달하는 패턴은
**Password management: empty password**로 분류한다.

### Treat as Finding when

- key name이 아래 중 하나에 해당하고 값이 `""` 또는 공백 문자열인 경우:
  - `password`, `passwd`, `pwd`
  - `pass`, `passphrase`
  - `secret`, `clientSecret`, `apiSecret`
  - `token` (단, 빈 문자열이 인증 흐름에 영향을 주는 경우)

### Examples (Red Flag)

- `password: ""`
- `password = ""`
- `const password = ""`
- `process.env.PASSWORD || ""`  (기본값이 빈 문자열)

### Impact

빈 비밀번호 허용/우회로 인해 인증이 무력화되거나, “비밀번호 미설정 상태”가 운영까지 전파될 수 있다.

### Recommended Fix

- 개발용 기본값을 두더라도 `""`로 두지 말고,
  - (1) **필수 값이면** 시작 시점에 누락 시 실패(fail-fast)
  - (2) **선택 값이면** `null/undefined`로 표현하고, 명시적으로 분기 처리
- 운영 환경에서는 secret store/환경변수에서 주입되도록 강제한다.
---

## Verification Checklist

- [ ] 소스코드 내 하드코딩된 Secret 존재 여부
- [ ] 로그/에러 메시지 노출 여부
- [ ] Secret 접근 권한 최소화 여부
- [ ] Rotation 전략 존재 여부

---

## Notes

- Base64 인코딩은 **암호화가 아님**
- Secret 암호화 여부는 “저장 시 + 전송 시” 모두 고려