---
scope:
  domain: ["backdoor", "malicious-behavior"]
applies_to:
  - "any application handling untrusted input"
  - "services with OS or network access"
  - "agents, plugins, CI/CD scripts"
priority: 90
---

# Backdoor / Malicious Behavior Security

## Overview

Backdoor 또는 Malicious Behavior는 정상적인 기능과 무관하게  
**비인가 접근, 은닉된 제어, 데이터 탈취, 원격 실행**을 가능하게 하는 코드 패턴을 의미합니다.

이 문서는 개발자의 의도를 단정하지 않고,  
**관찰 가능한 코드 구조와 동작만을 기준으로 의심스러운 행위**를 식별하기 위한 보안 정책입니다.

언어·프레임워크와 무관하게  
**시스템 자원(OS, 네트워크, 인증 흐름)에 접근하는 모든 코드**에 적용됩니다.

---

## Common Backdoor Entry Points

- 숨겨진 HTTP 파라미터 / 헤더 / 쿠키
- 특정 값(magic value)에 반응하는 조건문
- 디버그/관리자 전용으로 위장된 코드 경로
- 환경 변수, 설정 파일 기반 은닉 플래그
- LLM Agent / Tool 입력
- CI/CD 스크립트, 빌드 단계 코드

---

## Secure Defaults

- 숨겨진 기능(hidden feature)을 만들지 않는다
- 관리자/디버그 기능은 **명시적 인증·인가 흐름**을 따른다
- 조건부로 실행되는 민감 기능은 반드시 문서화한다
- 네트워크/OS 접근은 최소화하고 추적 가능하게 한다

---

## High-Risk Behaviors

다음 행위는 **백도어 의심 패턴**으로 취급한다.

- 특정 값이 입력되면 인증/인가를 우회
- 문서화되지 않은 관리자 접근 경로
- 조건부로 활성화되는 OS 명령 실행
- 외부와의 은닉된 통신(비인가 egress)
- 코드 난독화 또는 동적 로딩으로 동작 은폐

---

## Recommended Defenses

### Authentication & Authorization
- 모든 관리자/유지보수 기능에 명시적 인증 요구
- 역할(Role)·정책 기반 접근 제어 적용
- “디버그 전용”이라도 운영 코드에서는 보호

### Code Transparency
- 숨겨진 조건(magic string, flag) 제거
- 기능 활성화 조건을 설정/문서로 명시
- 보안 민감 코드에 주석과 설계 문서 추가

### Execution & Network Control
- OS 명령 실행은 RCE 정책(`rce-command-exec-security.md`) 준수
- 외부 통신은 allowlist 기반 제한
- 비정상 네트워크 호출에 로깅·감사 추가

---

## Do

- 관리자/운영 기능을 명확히 분리
- 보안 민감 분기 로직을 코드 리뷰 대상에 포함
- 조건부 기능의 활성화 경로를 테스트로 검증
- 의심되는 코드에 명확한 주석과 근거 남기기

---

## Don't

- `if (password == "magic")` 같은 우회 조건
- 문서화되지 않은 관리자 엔드포인트
- 특정 헤더/파라미터로만 활성화되는 기능
- 은닉된 외부 서버로의 통신
- “편의를 위해 남겨둔” 디버그 코드

---

## High-Risk Patterns

- Hardcoded master password / token
- `x-debug`, `x-admin` 같은 숨겨진 헤더 트리거
- 특정 시간/환경에서만 활성화되는 로직
- Base64/문자열 분할로 숨긴 URL·명령
- 사용자 모르게 실행되는 백그라운드 작업

---

## Severity Guidance

- **Critical**
  - 인증/인가 우회
  - 숨겨진 원격 명령 실행
  - 데이터 탈취 목적의 은닉 통신

- **High**
  - 의도 불분명한 OS 명령 실행
  - 문서화되지 않은 관리자 기능
  - 조건부 네트워크 egress

- **Medium**
  - 난독화·동적 로딩 등 분석 회피 패턴
  - 불필요하게 남아 있는 디버그 코드

---

## Verification Checklist

- [ ] 문서화되지 않은 관리자/디버그 기능 존재 여부
- [ ] 인증/인가 우회 조건 존재 여부
- [ ] 조건부 OS 명령 실행 로직 존재 여부
- [ ] 외부 통신 대상이 명확히 정의되어 있는가?
- [ ] 난독화/동적 코드 실행 사용 여부

---

## Notes

- Backdoor 판단은 **의도 추정이 아니라 구조 기반**으로 한다
- 오탐 가능성이 있는 경우  
  → “Suspicious – needs human review”로 표시
- RCE 구조가 결합되면  
  → `rce-command-exec-security.md` 기준으로 함께 검토