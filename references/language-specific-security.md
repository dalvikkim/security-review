---
scope:
  type: ["language-specific"]
priority: 80
applies_to:
  - "code review for specific languages"
  - "language-unique vulnerability patterns"
---

# Language-Specific Security Guide

이 문서는 언어별로 **고유하게 발생하는 보안 취약점과 패턴**을 다룹니다.
공통적인 보안 원칙(입력 검증, 인증/인가, Secrets 관리 등)은 SKILL.md 및 도메인별 가이드를 참조하세요.

---

## C / C++

### Core Risks (C 특성상 고위험)

- **메모리 안전성**: buffer overflow, use-after-free, double free
- **정수 오버플로/언더플로** → 길이 계산 오류 → 메모리 손상
- **포맷 스트링 취약점**: `printf(user_input)`
- **경로/파일 처리**: TOCTOU, symlink, path traversal
- **동시성/레이스 컨디션**

### Secure Defaults

- **안전한 함수 우선**: `snprintf`, `strlcpy/strlcat`, 길이 검증된 memcpy 계열
- **문자열/버퍼**: "길이 + 포인터"로 함께 관리
- **컴파일/런타임 방어**: Stack protector, PIE, RELRO, NX, FORTIFY
- **테스트 시 Sanitizer 활성화**: ASan, UBSan

### High-Risk Patterns

```c
// BAD: 길이 검증 없는 memcpy
memcpy(dst, src, len);  // len이 사용자 입력인 경우

// BAD: overflow 가능한 size 계산
malloc(n * sizeof(T));  // n * sizeof(T) overflow

// BAD: 포맷 스트링
printf(user_input);

// BAD: 위험한 함수
gets(buffer);
strcpy(dst, src);
sprintf(buffer, fmt, ...);
```

### Verification Checklist

- [ ] 모든 버퍼 접근에 상한 검증 존재
- [ ] 문자열 처리 함수가 길이 제한형으로 통일
- [ ] 포맷 스트링에 사용자 입력 직접 사용 없음
- [ ] 정수 overflow-safe 계산(특히 size 계산)
- [ ] 컴파일 방어 옵션 및 Sanitizer 테스트 적용

---

## Go

### Core Risks

- **입력 검증 누락** → injection/SSRF/path traversal
- **템플릿/HTML 처리 실수**(기본 escaping 우회 시)
- **`os/exec` 사용 시 command injection**
- **파일/경로 처리 실수**
- **의존성 및 모듈 공급망**

### Secure Defaults

- 입력은 **구조체 + validation**으로 통일
- DB는 **parameterized query** 사용
- 외부 요청: timeout 필수, redirect 제한, allowlist 고려
- `os/exec`는 **인자 배열 방식**으로 제한적 사용
- **`html/template`**(자동 escape) 선호, `text/template`로 HTML 생성 금지

### High-Risk Patterns

```go
// BAD: SSRF
http.Get(userURL)

// BAD: Command injection
exec.Command("sh", "-c", userInput)

// BAD: HTML with text/template
t := template.New("page")
t.Parse(userHTML)

// BAD: Path traversal
filepath.Join(baseDir, userPath)  // 정규화 없이
```

### Verification Checklist

- [ ] 입력 검증(길이/범위/포맷) 존재
- [ ] outbound request에 timeout/allowlist/redirect 제한
- [ ] `os/exec` 사용 시 shell 호출 없음
- [ ] HTML은 `html/template` 기반 + escaping 우회 없음
- [ ] go.mod 의존성 관리/스캔 흐름 존재

---

## Java

### Core Risks

- **Injection**: SQL/LDAP/EL/OGNL(프레임워크 의존 포함)
- **Unsafe deserialization** (Java serialization)
- **XXE** (XML 파서 기본 설정)
- **SSRF**(HTTP client), open redirect
- **인증/인가 누락 및 IDOR**
- **로그/예외에 민감정보 노출**

### Secure Defaults

- DB 접근: **PreparedStatement/ORM**로 파라미터 바인딩
- XML 처리: **DTD/External entity 기본 차단**(XXE 방지)
- 직렬화: Java native serialization 지양
- 보안 설정을 코드/구성에서 명시적으로 "secure default"로 고정

### High-Risk Patterns

```java
// BAD: SQL Injection
Statement.execute("SELECT * FROM users WHERE id=" + userId);

// BAD: Deserialization
new ObjectInputStream(socket.getInputStream()).readObject();

// BAD: XXE (기본 설정)
DocumentBuilderFactory.newInstance().newDocumentBuilder();

// BAD: LDAP Injection
ctx.search("ou=users", "(uid=" + username + ")", constraints);
```

### Verification Checklist

- [ ] SQL/LDAP 등 쿼리 구성에 바인딩 사용
- [ ] Java serialization/XXE 방어 설정 적용
- [ ] 예외/로그 민감정보 마스킹
- [ ] 인증/인가 체크가 서버에서 강제됨
- [ ] 의존성 취약점 스캔 및 버전 관리 존재

---

## JavaScript / TypeScript

### Core Risks

- **XSS**: DOM 조작, innerHTML
- **Prototype pollution**
- **Injection**: SQL/NoSQL/command (Node.js 백엔드)
- **SSRF**: 서버에서 fetch/axios
- **dependency/supply-chain**: npm 생태계

### Secure Defaults

- DOM 조작: `innerHTML`/`dangerouslySetInnerHTML` 지양
- 서버(Node): 입력 검증(스키마), parameterized query, subprocess 최소화
- 패키지: lockfile 사용, 버전 고정, 정기 업데이트, 스캔 도입

### High-Risk Patterns

```javascript
// BAD: XSS
element.innerHTML = userInput;

// BAD: Prototype pollution
Object.assign({}, userObj);  // __proto__, constructor 키 검증 없음
deepMerge(target, userInput);

// BAD: Command injection
child_process.exec(userInput);
exec("cmd " + user);

// BAD: SSRF (server-side)
fetch(userUrl);

// BAD: eval
eval(userCode);
new Function(userInput);
```

### Verification Checklist

- [ ] XSS 위험 API 사용처 점검(innerHTML 등)
- [ ] prototype pollution 방어(키 필터링)
- [ ] SSRF 방어(allowlist/timeout/redirect)
- [ ] dependencies lock/pin/scan 운영

---

## Python

### Core Risks

- **Injection**: SQL/NoSQL, command injection, template injection
- **역직렬화**(pickle) 사용
- **SSRF/외부 요청** 남용
- **secrets 노출**(로그/환경/설정)
- **dependency/supply-chain**: typosquatting, 취약 버전

### Secure Defaults

- 입력 검증/스키마(pydantic 등)로 "신뢰 경계"를 명확히
- DB는 **parameterized query/ORM** 사용
- OS 명령 실행 최소화, 필요 시 allowlist + 인자 배열 호출
- `pickle`/`yaml.load(unsafe)` 같은 위험 기능 금지
- 로깅은 기본 마스킹(토큰/비밀번호)

### High-Risk Patterns

```python
# BAD: SSRF
requests.get(user_url)

# BAD: Command injection
os.system("cmd " + user)
subprocess.run(user_string, shell=True)

# BAD: Pickle deserialization
pickle.loads(external_data)

# BAD: Template injection
render_template_string(user_input)

# BAD: eval/exec
eval(user_input)
exec(user_code)
```

### Verification Checklist

- [ ] 외부 입력이 DB/OS/템플릿에 직접 연결되지 않음
- [ ] `pickle`, unsafe YAML 로딩 사용 없음
- [ ] HTTP 요청에 timeout/allowlist/redirect 제한 존재
- [ ] secrets가 코드/로그/리포지토리에 없음
- [ ] dependencies 버전 고정 및 업데이트/스캔 흐름 존재

---

## Vue.js / React (Frontend Frameworks)

### Core Risks

- **XSS**: `v-html`, `dangerouslySetInnerHTML`
- **토큰/세션 저장 실수**: localStorage 등
- **CORS/CSRF 오해**: 프론트만으로 해결 불가
- **민감정보 노출**: 번들에 비밀키 포함
- **의존성/빌드 파이프라인 오염**

### Secure Defaults

- HTML 삽입은 기본적으로 금지
- `v-html`/`dangerouslySetInnerHTML`은 **검증된(sanitized) 콘텐츠**에만 사용
- 인증은 **서버가 강제**, 프론트는 UX 보조 역할
- 프론트 번들에 secrets 포함 금지
- 사용자 입력은 기본 escape(템플릿 기본 동작 유지)

### High-Risk Patterns

```vue
<!-- BAD: XSS in Vue -->
<div v-html="userProvidedHtml"></div>

<!-- BAD: Secrets in bundle -->
VITE_API_SECRET=xxx  // .env에 노출
```

```jsx
// BAD: XSS in React
<div dangerouslySetInnerHTML={{__html: userInput}} />
```

### Verification Checklist

- [ ] `v-html`/`dangerouslySetInnerHTML` 사용처에 sanitize/신뢰근거 명확
- [ ] 프론트 번들에 secrets 포함 없음
- [ ] 인증/인가가 서버에서 강제됨
- [ ] 의존성 취약점 점검/업데이트 흐름 존재

---

## Common Patterns Across Languages

### Dangerous Functions to Watch

| Language | High-Risk Functions/Patterns |
|----------|------------------------------|
| C/C++ | `gets`, `strcpy`, `sprintf`, `printf(user)`, `system()` |
| Go | `exec.Command("sh", "-c", ...)`, `text/template` for HTML |
| Java | `Statement.execute()`, `ObjectInputStream`, XXE parsers |
| JavaScript | `eval`, `innerHTML`, `child_process.exec`, deep merge |
| Python | `eval`, `exec`, `pickle.loads`, `shell=True`, `os.system` |

### Safe Alternatives

| Risk | Safe Alternative |
|------|------------------|
| String concat SQL | Parameterized queries, ORM |
| Shell command strings | Argument arrays, avoid shell |
| User HTML rendering | Sanitization libraries, auto-escaping |
| Unsafe deserialization | JSON, validated schemas |
| Hardcoded secrets | Environment variables, secret managers |

---

## Notes

- 언어별 가이드는 **해당 언어 고유의 위험**에 집중합니다
- 공통 원칙(입력 검증, 인증, Secrets 등)은 SKILL.md 및 도메인 가이드 참조
- 프레임워크별 추가 가이드가 필요한 경우 별도 문서로 확장 가능
