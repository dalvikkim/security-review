---
scope:
  domain: ["rce", "command-execution"]
applies_to:
  - "services executing OS or shell commands"
  - "web applications, APIs, agents, CLIs"
  - "CI/CD scripts and automation"
priority: 95
---

# Remote Command Execution (RCE) / Command Execution Security

## Overview

RCE(Remote Command Execution)는 외부 입력 또는 외부 영향에 의해  
서버나 실행 환경에서 **임의의 OS 명령이 실행될 수 있는 상태**를 의미하며,  
시스템 장악, 데이터 유출, 서비스 파괴로 직결되는 **치명적인 취약점**입니다.

언어·프레임워크와 무관하게  
**OS 명령 실행 기능이 존재하는 모든 코드**에 적용됩니다.

---

## Common RCE Entry Points

- HTTP 요청 파라미터 / 헤더 / 바디
- 환경 변수 (`ENV`, `.env`)
- 설정 파일 (YAML, JSON, properties 등)
- DB에 저장된 값
- 네트워크 응답 / 외부 파일
- LLM Agent / Tool 입력

---

## Secure Defaults

- OS 명령 실행 기능은 **기본적으로 사용하지 않는다**
- Shell 기반 실행(`sh -c`, `cmd /c`, `shell=True`)은 금지
- 외부 입력은 **절대 명령 문자열로 직접 사용하지 않는다**
- 가능한 경우 OS 명령 대신 **내부 라이브러리/API**를 사용한다

---

## High-Risk Command Interfaces

언어와 무관하게 아래 유형의 API는 **RCE 고위험 지점**으로 취급한다.

- Shell 기반 실행 인터페이스
- 문자열 기반 명령 전달 API
- 동적 명령 생성 (`+`, format, template)
- 외부 입력이 인자로 전달되는 실행 함수

---

## Recommended Defenses

### Command Design
- 명령 실행 자체를 제거하거나 최소화
- 반드시 필요할 경우 **고정된 명령 + allowlist 인자**만 허용
- 인자는 배열/리스트 형태로 전달 (문자열 결합 금지)

### Input Control
- 사용자 입력을 명령 인자로 직접 사용하지 않음
- 경로/파일 인자는 정규화 후 allowlist 적용
- 플래그(`-`, `--`) 입력 차단

### Execution Control
- Shell 미사용 실행 API만 사용
- 실행 권한 최소화
- 실행 시간/횟수 제한

---

## Do

- 명령 실행 필요성을 문서화
- 실행 가능한 명령 목록을 코드에 명시적으로 제한
- 외부 입력을 명령어가 아닌 **데이터**로 처리
- 실패 시 안전하게 중단(fail-safe)

---

## Don't

- `system(userInput)`
- `exec("sh -c " + input)`
- `shell=True` 사용
- 외부 입력으로 전체 명령 문자열 생성
- “내부 기능이니까 괜찮다”는 가정

---

## High-Risk Patterns

- 사용자 입력이 그대로 OS 명령에 전달됨
- 문자열 결합/포맷으로 명령 생성
- 환경 변수 기반 명령 제어
- LLM/Agent가 임의 명령을 실행할 수 있는 구조
- `curl | bash`, `wget | sh` 패턴

---

## Severity Guidance

- **Critical**
  - 외부 입력이 명령 전체 또는 핵심 인자를 직접 제어
  - Shell 실행이 포함된 경우

- **High**
  - 명령은 고정이지만 인자가 외부 입력에 의존
  - allowlist/검증이 불완전한 경우

- **Medium**
  - 명령/인자가 모두 고정
  - 외부 입력과 무관하나 OS 명령 실행이 존재

---

## Verification Checklist

- [ ] 외부 입력이 명령 실행 경로에 도달하는가?
- [ ] Shell 기반 실행이 사용되는가?
- [ ] 명령/인자가 문자열 결합으로 생성되는가?
- [ ] allowlist 기반 인자 제한이 존재하는가?
- [ ] OS 명령 대신 대체 API 사용 가능 여부 검토했는가?

---

## Notes

- RCE는 **의도와 무관하게 구조만으로 성립**할 수 있다
- “관리용”, “운영용”, “내부 전용”이라는 가정은 위험
- RCE + 은닉 트리거가 결합되면 backdoor로 승격 검토