---
scope:
  domain: ["input-validation", "injection"]
applies_to:
  - "any application processing external input"
  - "APIs, web forms, file parsers, CLI tools"
priority: 95
---

# Input Validation Security

## Overview

입력 검증은 **모든 보안의 첫 번째 방어선**입니다.
외부에서 들어오는 모든 데이터는 잠재적으로 악의적이며,
적절한 검증 없이 사용할 경우 다양한 Injection 공격의 원인이 됩니다.

---

## Core Principles

### Trust Boundaries

- **외부 입력은 모두 불신**: HTTP 요청, 파일, 환경변수, DB 데이터(다른 시스템이 저장한 경우)
- **검증은 서버에서**: 클라이언트 검증은 UX용, 보안은 서버에서 강제
- **검증 후 사용**: 검증되지 않은 데이터는 어떤 연산에도 사용 금지

### Allowlist vs Denylist

- **Allowlist 우선**: 허용된 값/패턴만 통과, 나머지는 거부
- **Denylist 지양**: 알려진 악성 패턴만 차단하는 방식은 우회 가능

---

## Secure Defaults

- 모든 입력에 **타입, 길이, 포맷, 범위** 검증 적용
- 검증 로직은 **중앙화**: 스키마, DTO, 검증 라이브러리 사용
- 실패 시 **거부 후 로깅** (에러 메시지에 입력값 노출 금지)
- 정규화(normalization) 후 검증

---

## Common Injection Attacks

### SQL Injection
- **원인**: 사용자 입력이 SQL 쿼리에 직접 연결
- **방어**: Parameterized queries, ORM, prepared statements
- **CWE**: CWE-89

### Command Injection
- **원인**: 사용자 입력이 OS 명령에 전달
- **방어**: Shell 미사용, 인자 배열 방식, allowlist
- **CWE**: CWE-78

### Path Traversal
- **원인**: 사용자 입력이 파일 경로에 사용
- **방어**: 경로 정규화, 루트 디렉토리 제한, allowlist
- **CWE**: CWE-22

### XSS (Cross-Site Scripting)
- **원인**: 사용자 입력이 HTML/JS에 그대로 출력
- **방어**: Context-aware escaping, CSP, sanitization
- **CWE**: CWE-79

### LDAP Injection
- **원인**: 사용자 입력이 LDAP 쿼리에 직접 연결
- **방어**: LDAP 특수문자 이스케이프, parameterized filters
- **CWE**: CWE-90

### Template Injection
- **원인**: 사용자 입력이 템플릿 엔진에 전달
- **방어**: 사용자 입력을 템플릿으로 처리하지 않음
- **CWE**: CWE-1336

---

## Validation Strategies

### Type Validation
```
- 숫자 필드에 문자열 입력 차단
- Boolean은 true/false만 허용
- 날짜는 유효한 포맷만 허용
```

### Length Validation
```
- 최소/최대 길이 제한
- 버퍼 오버플로 방지
- DoS 공격(대량 데이터) 방지
```

### Format Validation
```
- 이메일: RFC 5322 또는 간소화된 정규식
- URL: 스킴 제한 (https only)
- 전화번호: 국가별 포맷
```

### Range Validation
```
- 숫자: 최소/최대 값
- 날짜: 유효 범위
- Enum: 허용된 값 목록
```

### Semantic Validation
```
- 비즈니스 로직에 맞는 검증
- 참조 무결성 확인
- 상태 전이 유효성
```

---

## Do

- **스키마 기반 검증** 사용 (JSON Schema, Pydantic, Zod, class-validator 등)
- **라이브러리 기반 파싱**: 직접 파싱보다 검증된 라이브러리 사용
- **정규화 후 검증**: Unicode 정규화, 경로 정규화 후 검증
- **검증 실패 시 명확한 거부**: 부분적 처리 금지
- **로깅**: 검증 실패 이벤트 기록 (단, 입력값 자체는 제한적으로)

---

## Don't

- **클라이언트 검증만 신뢰**: JavaScript 검증은 우회 가능
- **정규식 하나로 모든 검증**: 복잡한 정규식은 ReDoS 위험
- **Denylist 의존**: 알려진 패턴만 차단하는 방식
- **에러 메시지에 입력값 노출**: 공격자에게 힌트 제공
- **검증 없이 리다이렉트**: Open redirect 취약점

---

## High-Risk Patterns

### SQL
```sql
-- BAD
"SELECT * FROM users WHERE id = " + userId

-- GOOD (parameterized)
"SELECT * FROM users WHERE id = ?"
```

### Command
```bash
# BAD
os.system("convert " + filename)

# GOOD
subprocess.run(["convert", filename], shell=False)
```

### Path
```python
# BAD
open("/data/" + user_filename)

# GOOD
import os
safe_path = os.path.normpath(os.path.join("/data", user_filename))
if not safe_path.startswith("/data/"):
    raise ValueError("Invalid path")
```

### HTML
```html
<!-- BAD -->
<div>{{userInput | safe}}</div>

<!-- GOOD (auto-escape) -->
<div>{{userInput}}</div>
```

---

## Verification Checklist

- [ ] 모든 외부 입력에 서버 측 검증 존재
- [ ] Parameterized queries 사용 (SQL/LDAP/NoSQL)
- [ ] 파일 경로 입력에 정규화 + 루트 제한 적용
- [ ] HTML 출력에 context-aware escaping 적용
- [ ] OS 명령 실행 시 shell=False 또는 인자 배열 사용
- [ ] 검증 실패 시 에러 메시지에 입력값 미노출
- [ ] 스키마/DTO 기반 중앙화된 검증 로직 사용

---

## Severity Guidance

- **Critical**: SQL/Command Injection이 가능한 경우
- **High**: Path Traversal로 민감 파일 접근 가능
- **Medium**: XSS, 불완전한 검증
- **Low**: 검증은 있으나 일관성 부족

---

## Notes

- 입력 검증은 **방어의 첫 번째 계층**이며, 유일한 방어가 아님
- 출력 인코딩, 권한 제어, 모니터링과 함께 다층 방어 적용 필요
- 정규식 기반 검증 시 ReDoS(Regular Expression DoS) 주의
