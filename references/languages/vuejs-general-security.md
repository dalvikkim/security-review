---
scope:
  language: ["vuejs"]
priority: 75
applies_to:
  - "web frontends built with Vue"
  - "SPAs handling auth/session"
---

# Vue.js (Frontend) General Security Guide

## Core Risks (프론트엔드 공통 + Vue에서 자주 만나는 것)

- XSS (특히 `v-html` 사용)
- 토큰/세션 저장 실수(localStorage 등)
- CORS/CSRF 오해(프론트만으로 해결 불가)
- 민감정보 노출(번들에 비밀키 포함)
- 의존성(supply chain) 및 빌드 파이프라인 오염

---

## Secure Defaults

- HTML 삽입은 기본적으로 금지
  - `v-html`는 “검증/정화된(sanitized) 콘텐츠”에만 사용
- 인증은 **서버가 강제**하고, 프론트는 UX 보조 역할
- 프론트 번들에 secrets 포함 금지
- 사용자 입력은 기본 escape(템플릿 기본 동작 유지)

---

## Do

- `v-html`가 필요하면:
  - 서버에서 sanitize하거나, 신뢰 가능한 소스만 사용
- auth 토큰:
  - 가능하면 httpOnly cookie 기반 세션 선호(서버와 함께 설계)
- 라우팅/가드:
  - 클라이언트 라우트 가드는 “보안”이 아니라 “편의”로 취급
- CSP(Content Security Policy) 적용을 백엔드/프록시와 협업

---

## Don’t

- `v-html`에 사용자 입력/외부 HTML을 그대로 바인딩
- API Key/Secret을 `.env`로 넣고 프론트 빌드에 포함
- localStorage에 장기 토큰 저장을 당연시
- 에러/로그에 개인정보를 출력

---

## High-Risk Patterns

- `<div v-html="userProvidedHtml">`
- 번들 결과물에서 키가 노출되는 형태(`VITE_*` 환경변수 오용)
- “프론트에서 막았으니 서버는 괜찮다”는 접근(권한/검증 누락)

---

## Verification Checklist

- [ ] `v-html` 사용처 존재 시 sanitize/신뢰근거 명확
- [ ] 프론트 번들에 secrets 포함 없음
- [ ] 인증/인가가 서버에서 강제됨
- [ ] 의존성 취약점 점검/업데이트 흐름 존재