---
scope:
  stack: ["web-backend"]
priority: 85
---

# Web Backend General Security

## Secure Defaults

- CSRF 보호 기본 활성화
- 세션은 서버 검증 기반
- 파일 시스템 직접 접근 최소화

## Common Pitfalls

- 프레임워크 기본 설정을 그대로 신뢰
- 디버그 모드 상시 활성화
- 내부 IP/URL 하드코딩

---

## Verification Checklist

- [ ] CSRF 토큰 검증
- [ ] Debug/Dev 설정 분리
- [ ] 세션 탈취 가능성
