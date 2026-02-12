# Security Review Agent Skill

코드베이스의 보안 취약점을 체계적으로 분석하고 리포트를 생성하는 Claude Code Agent Skill입니다.

## 개요

이 스킬은 사용자가 명시적으로 보안 리뷰를 요청할 때 활성화되며, 언어와 프레임워크에 관계없이 보안 취약점을 분석합니다.

### 활성화 조건

- 보안 리뷰 또는 보안 리포트 요청
- 보안 하드닝 요청
- Secure-by-Default 코딩 지원 요청
- 취약점 분석 요청

---

## 탐지 가능한 보안 취약점

### 1. 인젝션 (Injection)

| 취약점 유형 | 설명 | 대상 언어/환경 |
|------------|------|---------------|
| **SQL Injection** | 사용자 입력이 SQL 쿼리에 직접 삽입되는 경우 | Python, Java, JavaScript, Go |
| **NoSQL Injection** | NoSQL 쿼리에 사용자 객체가 직접 전달되는 경우 | JavaScript (Node.js) |
| **Command Injection** | OS 명령어에 사용자 입력이 포함되는 경우 | 모든 언어 |
| **Template Injection** | 템플릿 엔진에 사용자 입력이 직접 삽입되는 경우 | Python, Java |
| **LDAP Injection** | LDAP 쿼리에 사용자 입력이 삽입되는 경우 | Java |
| **EL/OGNL Injection** | Expression Language에 사용자 입력이 삽입되는 경우 | Java |

### 2. 크로스 사이트 스크립팅 (XSS)

| 취약점 유형 | 설명 | 대상 언어/환경 |
|------------|------|---------------|
| **DOM-based XSS** | `innerHTML` 등에 사용자 입력 직접 바인딩 | JavaScript |
| **v-html XSS** | Vue.js `v-html`에 사용자 데이터 바인딩 | Vue.js |
| **Template XSS** | `text/template` 사용으로 인한 이스케이프 누락 | Go |

### 3. SSRF (Server-Side Request Forgery)

| 취약점 유형 | 설명 | 대상 언어/환경 |
|------------|------|---------------|
| **URL 검증 없는 요청** | `fetch(userUrl)`, `requests.get(user_url)` 등 | 모든 서버 사이드 언어 |
| **내부 네트워크 접근** | localhost, 169.254.169.254, 사설 IP 대역 접근 | 모든 환경 |
| **무제한 리다이렉트** | 리다이렉트 제한 없이 외부 URL 요청 | 모든 환경 |
| **LLM Agent URL Fetch** | AI Agent가 임의 URL을 fetch하는 경우 | AI/LLM 환경 |

### 4. 인증/인가 취약점 (Authentication & Authorization)

| 취약점 유형 | 설명 | 대상 언어/환경 |
|------------|------|---------------|
| **인증 누락** | 민감한 엔드포인트에 인증 미들웨어 누락 | API, Web Backend |
| **클라이언트 측 인가** | 프론트엔드에서만 권한 체크 수행 | Vue.js, JavaScript |
| **사용자 ID 신뢰** | 요청 파라미터의 사용자 ID를 신뢰 | 모든 환경 |
| **IDOR** | 직접 객체 참조를 통한 권한 우회 | Java, 모든 API |

### 5. 시크릿/자격증명 관리

| 취약점 유형 | 설명 | 대상 언어/환경 |
|------------|------|---------------|
| **하드코딩된 시크릿** | 소스 코드에 API 키, 비밀번호 직접 작성 | 모든 언어 |
| **빈 비밀번호** | `password = ""` 형태의 빈 비밀번호 할당 | 모든 언어 |
| **로그 내 시크릿** | 로그나 에러 메시지에 시크릿 노출 | 모든 언어 |
| **번들 내 시크릿** | 프론트엔드 빌드에 API 키 포함 | Vue.js, JavaScript |
| **Docker 이미지 내 시크릿** | ENV/ARG/RUN에 시크릿 포함 | Docker |

### 6. 원격 코드 실행 (RCE)

| 취약점 유형 | 설명 | 대상 언어/환경 |
|------------|------|---------------|
| **Shell 명령 실행** | `shell=True`, `sh -c`와 사용자 입력 조합 | Python, Go, JavaScript |
| **eval/exec** | 사용자 입력에 대한 `eval()`, `exec()` 실행 | Python, JavaScript |
| **동적 명령 구성** | 문자열 연결로 명령어 구성 | 모든 언어 |

### 7. 안전하지 않은 역직렬화 (Deserialization)

| 취약점 유형 | 설명 | 대상 언어/환경 |
|------------|------|---------------|
| **Pickle** | 신뢰할 수 없는 데이터에 `pickle.loads` 사용 | Python |
| **YAML unsafe load** | `yaml.load()` 안전하지 않은 로더 사용 | Python |
| **Java Serialization** | `ObjectInputStream`으로 신뢰할 수 없는 데이터 역직렬화 | Java |

### 8. XXE (XML External Entity)

| 취약점 유형 | 설명 | 대상 언어/환경 |
|------------|------|---------------|
| **외부 엔티티 활성화** | XML 파서에서 외부 엔티티/DTD 비활성화 누락 | Java |

### 9. 파일 업로드/다운로드 취약점

| 취약점 유형 | 설명 | 대상 언어/환경 |
|------------|------|---------------|
| **웹쉘 업로드** | 실행 가능한 파일 업로드 허용 | 모든 웹 환경 |
| **경로 탐색 (Path Traversal)** | `../` 등을 통한 디렉토리 탈출 | 모든 환경 |
| **MIME 타입 우회** | 확장자만 검증하고 실제 콘텐츠 미검증 | 모든 웹 환경 |
| **파일명 미검증** | 사용자 제공 파일명 그대로 사용 | 모든 환경 |

### 10. 메모리 안전성 (C/C++ 전용)

| 취약점 유형 | 설명 |
|------------|------|
| **버퍼 오버플로우** | 경계 검사 없는 버퍼 접근 |
| **Use-After-Free** | 해제된 메모리 접근 |
| **Double Free** | 이중 메모리 해제 |
| **정수 오버플로우** | 메모리 할당 시 정수 오버플로우 |
| **포맷 스트링** | 사용자 입력을 포맷 스트링으로 사용 |
| **안전하지 않은 함수** | `gets`, `strcpy`, `sprintf` 등 사용 |

### 11. Prototype Pollution (JavaScript)

| 취약점 유형 | 설명 |
|------------|------|
| **객체 병합** | `Object.assign`에 사용자 객체 직접 전달 |
| **__proto__ 오염** | `__proto__`, `constructor` 키 필터링 누락 |

### 12. 백도어/악성 행위 탐지

| 취약점 유형 | 설명 | 대상 언어/환경 |
|------------|------|---------------|
| **인증 우회 조건** | 특정 값으로 인증 우회하는 조건문 | 모든 환경 |
| **숨겨진 관리자 엔드포인트** | 문서화되지 않은 관리 기능 | 모든 환경 |
| **조건부 OS 명령 실행** | 특정 조건에서만 실행되는 시스템 명령 | 모든 환경 |
| **은닉 외부 통신** | 알려지지 않은 외부 서버와의 통신 | 모든 환경 |
| **난독화/동적 로딩** | 의도적인 코드 난독화 | 모든 환경 |

### 13. 컨테이너/Docker 보안

| 취약점 유형 | 설명 |
|------------|------|
| **Root 실행** | 컨테이너가 root 사용자로 실행 |
| **고정되지 않은 베이스 이미지** | `FROM image:latest` 사용 |
| **curl \| bash** | 검증 없이 원격 스크립트 실행 |
| **ADD 대신 COPY 미사용** | 불필요한 ADD 사용 |

### 14. CI/CD 보안

| 취약점 유형 | 설명 |
|------------|------|
| **워크플로우 내 평문 시크릿** | 파이프라인 설정 파일에 시크릿 노출 |
| **고정되지 않은 액션/이미지** | 버전 미고정 의존성 사용 |
| **보안 스캔 비활성화** | 보안 게이트 우회 |

### 15. 웹 백엔드 보안

| 취약점 유형 | 설명 | 대상 환경 |
|------------|------|----------|
| **CSRF 보호 미적용** | CSRF 토큰 검증 누락 | Web Backend |
| **프로덕션 디버그 모드** | 프로덕션 환경에서 디버그 모드 활성화 | 모든 프레임워크 |
| **안전하지 않은 세션 설정** | httpOnly, secure, sameSite 미설정 | Web Backend |
| **상세한 에러 메시지** | 스택 트레이스가 클라이언트에 노출 | 모든 환경 |

---

## 지원 언어 및 프레임워크

### 프로그래밍 언어

| 언어 | 전용 참조 문서 |
|-----|--------------|
| JavaScript / TypeScript | `javascript-general-security.md` |
| Python | `python-general-security.md` |
| Java / Kotlin | `java-general-security.md` |
| Go | `go-general-security.md` |
| C / C++ | `c-general-security.md` |
| Vue.js | `vuejs-general-security.md` |

### 스택 및 도메인

| 카테고리 | 전용 참조 문서 |
|---------|--------------|
| API | `api-general-security.md` |
| Web Backend | `web-backend-general-security.md` |
| Docker/Container | `docker-container-security.md` |
| CI/CD | `ci-cd-general-security.md` |

---

## 운영 모드

### Mode 1: Secure-by-Default 코드 생성
새 코드 작성 시 가장 안전한 기본값을 사용하여 생성합니다.

### Mode 2: Passive Critical Detection
높은 영향도의 취약점만 선별적으로 탐지합니다.

### Mode 3: Formal Security Review Report
`security_review_report.md` 형식의 정식 보안 리뷰 리포트를 생성합니다.

리포트 구성:
1. Executive Summary
2. Threat Model Summary
3. Critical Findings
4. High Findings
5. Medium / Low Findings
6. Assumptions & Limitations

---

## 취약점 리포트 형식

각 발견 항목은 다음 필드를 포함합니다:

| 필드 | 설명 |
|-----|------|
| ID | SR-001, SR-002, ... |
| Category | 취약점 분류 |
| Severity | Critical / High / Medium / Low |
| Location | 파일명 + 라인 번호 |
| Evidence | 코드 스니펫 (민감 정보 마스킹) |
| Exploitation | 악용 방법 |
| Impact | 악용 시 영향 |
| Remediation | 수정 방법 |
| Verification | 수정 확인 방법 |

---

## 민감 정보 보호 정책

시크릿이 탐지된 경우:
- 원본 값은 LLM에 전송되지 않습니다
- `[REDACTED]`로 대체되어 기록됩니다
- 파일 경로와 라인 번호만 제공됩니다

---

## 사용 방법

```
보안 리뷰를 요청합니다.
```

```
이 코드의 보안 취약점을 분석해주세요.
```

```
보안 리포트를 생성해주세요.
```

---

## 참조 문서 구조

`references/` 디렉토리에는 언어별, 도메인별, 스택별 보안 가이드라인이 포함되어 있습니다:

```
references/
├── domain-keywords.yml      # 도메인 키워드 매핑
├── stack-map.yml            # 스택 증거 파일 매핑
├── api-general-security.md
├── authn-authz-security.md
├── input-validation-security.md
├── web-backend-general-security.md
├── ci-cd-general-security.md
├── file-upload-download-security.md
├── secrets-management-security.md
├── ssrf-security.md
├── rce-command-exec-security.md
├── backdoor-malicious-behavior-security.md
├── docker-container-security.md
├── javascript-general-security.md
├── python-general-security.md
├── java-general-security.md
├── go-general-security.md
├── c-general-security.md
└── vuejs-general-security.md
```
