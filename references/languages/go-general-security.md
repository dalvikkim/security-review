---
scope:
  language: ["go"]
priority: 80
applies_to:
  - "api services"
  - "microservices"
  - "cli tools"
---

# Go General Security Guide

## Core Risks

- 입력 검증 누락 → injection/SSRF/path traversal
- 템플릿/HTML 처리 실수(XSS) (기본 escaping 우회 시)
- `os/exec` 사용 시 command injection
- 파일/경로 처리 실수
- 의존성 및 모듈 공급망

---

## Secure Defaults

- 입력은 구조체 + validation으로 통일
- DB는 parameterized query 사용
- 외부 요청은:
  - timeout 필수, redirect 제한, allowlist 고려
- `os/exec`는 인자 배열 방식으로 제한적으로 사용
- `html/template`(자동 escape) 선호, `text/template`로 HTML 만들지 않기

---

## Do

- `context.WithTimeout`로 outbound request 제한
- 파일 경로는 `filepath.Clean` 후 루트 디렉토리 하위만 허용
- 에러 메시지는 내부 정보 최소화(로그/클라이언트 분리)
- secrets는 env/secret store로 주입하고 로그 마스킹

---

## Don’t

- `http.Get(userURL)` 형태의 무검증 요청(SSRF)
- `exec.Command("sh", "-c", userInput)` (사실상 shell 주입)
- HTML 생성에 `text/template` 사용
- 디버그 로그에 토큰/개인정보 출력

---

## High-Risk Patterns

- redirect 따라가는 기본 client 사용 + 내부망 접근 가능
- 파일 다운로드 기능에서 경로/파일명 검증 없음
- 에러를 그대로 클라이언트에 반환(내부 구조 노출)

---

## Verification Checklist

- [ ] 입력 검증(길이/범위/포맷) 존재
- [ ] outbound request에 timeout/allowlist/redirect 제한
- [ ] `os/exec` 사용 시 shell 호출 없음
- [ ] HTML은 `html/template` 기반 + escaping 우회 없음
- [ ] go.mod 의존성 관리/스캔 흐름 존재