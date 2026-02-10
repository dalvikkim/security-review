---
scope:
  language: ["c"]
priority: 80
applies_to:
  - "native applications"
  - "embedded/firmware"
  - "libraries handling untrusted input"
---

# C General Security Guide

## Core Risks (C 특성상 고위험)

- 메모리 안전성: buffer overflow, use-after-free, double free
- 정수 오버플로/언더플로 → 길이 계산 오류 → 메모리 손상
- 포맷 스트링 취약점 (`printf(user_input)`)
- 경로/파일 처리 취약점 (TOCTOU, symlink, path traversal)
- 동시성/레이스 컨디션

---

## Secure Defaults

- **안전한 함수/패턴 우선**
  - `snprintf`, `strlcpy/strlcat(가능 시)`, 길이 검증된 memcpy 계열
  - 문자열/버퍼는 “길이 + 포인터”로 함께 관리
- 컴파일/런타임 방어 기법 활성화
  - Stack protector, PIE, RELRO, NX, FORTIFY
  - Sanitizer(ASan/UBSan)로 테스트
- 입력은 기본 “불신” (untrusted)로 취급

---

## Do

- 모든 외부 입력에 대해:
  - 길이, 범위, 형식 검증
  - 계산(특히 length/offset)은 overflow-safe로 처리
- 동적 메모리:
  - 소유권(ownership) 명확히 문서화
  - free 후 포인터 NULL 처리(습관화)
- 파일/경로:
  - `open` 시 `O_NOFOLLOW` 등 사용 가능 여부 검토
  - 임시파일은 안전한 생성 API 사용

---

## Don’t

- `gets`, `strcpy`, `strcat`, `sprintf` 사용
- 사용자 입력을 포맷 문자열로 사용 (`printf(user)`)
- 길이 계산을 `int`로만 처리(플랫폼/부호 문제)
- “성공할 것” 가정하고 반환값 무시

---

## High-Risk Patterns

- `memcpy(dst, src, len)`에서 `len`이 사용자 입력에 의해 결정되는데 상한 검증 없음
- `malloc(n * sizeof(T))`에서 `n * sizeof(T)` overflow 가능
- `snprintf` 반환값(잘림 여부) 미확인
- 에러 처리 중 partial init/free 누락(메모리 누수/이중 해제)

---

## Verification Checklist

- [ ] 모든 버퍼 접근에 상한 검증 존재
- [ ] 문자열 처리 함수가 길이 제한형으로 통일
- [ ] 포맷 스트링에 사용자 입력 직접 사용 없음
- [ ] 정수 overflow-safe 계산(특히 size 계산)
- [ ] 컴파일 방어 옵션/ASan 테스트 적용