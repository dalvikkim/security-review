---
scope:
  domain: ["input-validation"]
priority: 95
---

# Input Validation Security

## Secure Defaults

- Allowlist 기반 검증
- 타입, 길이, 포맷 검증 필수

---

## Common Attacks

- SQL Injection
- Command Injection
- Path Traversal

---

## Do

- 중앙 검증 로직 사용
- 라이브러리 기반 파싱

## Don't

- 정규식 하나로 모든 검증 처리
- 클라이언트 검증만 신뢰
