---
scope:
  stack: ["api"]
priority: 90
---

# API General Security Best Practices

## Secure Defaults

- 모든 API는 인증을 기본으로 요구
- 명시적으로 public endpoint만 인증 예외 처리
- JSON schema 또는 DTO 기반 입력 검증 필수

---

## Do

- Rate limiting 적용
- 인증/인가 실패 메시지 최소화
- OpenAPI 문서와 실제 구현 동기화

## Don't

- 인증 없는 write API 제공
- 에러 메시지에 내부 스택 노출
- 요청 바디 그대로 DB/OS 호출에 사용

---

## Verification Checklist

- [ ] 인증 미적용 엔드포인트 존재 여부
- [ ] 입력값 타입/범위 검증
- [ ] 과도한 응답 데이터 반환 여부
