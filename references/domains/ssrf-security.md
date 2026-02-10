---
scope:
  domain: ["ssrf"]
applies_to:
  - "services making outbound HTTP/HTTPS requests"
  - "URL, webhook, callback, proxy features"
priority: 95
---

# Server-Side Request Forgery (SSRF) Security

## Overview

SSRF는 서버가 공격자가 지정한 URL로 요청을 보내도록 유도하여  
**내부 네트워크 접근, 메타데이터 탈취, 권한 상승**으로 이어질 수 있는 고위험 취약점입니다.

언어·프레임워크와 무관하게 **외부 요청을 만드는 모든 코드**에 적용됩니다.

---

## Common SSRF Entry Points

- URL 입력 파라미터
- Webhook / Callback URL
- File download via URL
- Proxy / Fetch / Scraper 기능
- LLM tool / agent에서의 URL fetch

---

## Secure Defaults

- 사용자 입력 URL을 **직접 요청하지 않는다**
- 내부 네트워크 접근 기본 차단
- 아웃바운드 네트워크는 최소화

---

## High-Risk Targets

- `localhost`, `127.0.0.1`
- `169.254.169.254` (cloud metadata)
- `10.0.0.0/8`, `172.16.0.0/12`, `192.168.0.0/16`
- Unix socket / file scheme (`file://`)

---

## Recommended Defenses

### Input Validation
- URL allowlist 기반 검증
- Scheme 제한 (`https` only)
- IP 직접 입력 차단

### Network Controls
- Egress firewall 규칙
- Metadata endpoint 차단
- DNS rebind 방어 고려

### Request Handling
- Redirect 제한 또는 비활성화
- Timeout, response size 제한

---

## Do

- URL 파싱 후 host/IP 재검증
- DNS resolve 결과 검사
- 내부 IP 대역 차단
- 외부 호출 기능 최소화

---

## Don't

- `fetch(userInputUrl)` 직접 호출
- 단순 문자열 검증으로 URL 필터링
- Redirect를 무제한 허용
- “외부 API니까 안전하다”는 가정

---

## High-Risk Patterns

- URL을 그대로 HTTP client에 전달
- Webhook URL을 DB에 저장 후 재사용
- LLM Agent가 임의 URL을 fetch 가능
- `curl`, `wget` 등 OS 명령 호출

---

## Verification Checklist

- [ ] 사용자 입력 기반 URL 요청 여부
- [ ] 내부 IP / metadata 접근 차단
- [ ] Redirect 제한 여부
- [ ] Egress 네트워크 정책 존재 여부

---

## Notes

- SSRF는 **기능 요구사항 때문에 자주 방치됨**
- “내부에서만 쓰는 기능”이라는 가정은 위험
- 클라우드 환경에서는 metadata 접근이 치명적