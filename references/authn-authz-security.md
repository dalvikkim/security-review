---
scope:
  domain: ["authn-authz"]
priority: 100
---

# Authentication & Authorization Security

## Core Principles

- Authentication과 Authorization 분리
- 서버 측 권한 검증 필수
- 클라이언트 정보 신뢰 금지

---

## Do

- Role/Policy 기반 접근 제어
- 토큰 만료 시간 설정
- 권한 체크를 공통 미들웨어로 처리

## Don't

- 프론트엔드 권한 검증에 의존
- 사용자 ID를 요청 파라미터로 신뢰

---

## High-Risk Patterns

- `isAdmin=true` 같은 플래그 신뢰
- URL 기반 권한 판별
