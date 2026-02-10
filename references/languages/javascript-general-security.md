---
scope:
  language: ["javascript"]
priority: 80
applies_to:
  - "node backends"
  - "browser frontends"
  - "shared libraries"
---

# JavaScript General Security Guide

## Core Risks

- XSS (DOM 조작, innerHTML)
- Prototype pollution
- Injection (SQL/NoSQL/command) — 주로 백엔드(Node)에서
- SSRF (서버에서 fetch/axios)
- dependency/supply-chain (npm 생태계)

---

## Secure Defaults

- DOM 조작:
  - `innerHTML`/`dangerouslySetInnerHTML` 유사 패턴 지양
- 서버(Node):
  - 입력 검증(스키마), parameterized query
  - subprocess 실행 최소화
- 패키지:
  - lockfile 사용, 버전 고정, 정기 업데이트, 스캔 도입

---

## Do

- 사용자 입력은 항상 validate + normalize
- 쿠키/세션:
  - httpOnly, sameSite 설정은 서버에서 관리(가능하면)
- 외부 요청:
  - allowlist, timeout, redirect 제한, response size 제한
- JSON 파싱/객체 병합:
  - 깊은 merge 시 prototype pollution 방어

---

## Don’t

- `eval`, `new Function`에 외부 입력 전달
- `child_process.exec(userInput)`
- NoSQL 쿼리에 사용자 객체 그대로 전달
- 취약한 패키지/미고정 버전 사용

---

## High-Risk Patterns

- `element.innerHTML = userInput`
- `Object.assign({}, userObj)` / deep merge 라이브러리 사용 시 키 검증 없음(`__proto__`, `constructor`)
- `fetch(userUrl)` (서버 SSRF)
- `exec("cmd " + user)`

---

## Verification Checklist

- [ ] XSS 위험 API 사용처 점검(innerHTML 등)
- [ ] prototype pollution 방어(키 필터링)
- [ ] SSRF 방어(allowlist/timeout/redirect)
- [ ] dependencies lock/pin/scan 운영