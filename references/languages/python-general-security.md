---
scope:
  language: ["python"]
priority: 80
applies_to:
  - "web backends"
  - "automation/agents"
  - "data pipelines"
---

# Python General Security Guide

## Core Risks (Python에서 흔한 것)

- Injection: SQL/NoSQL, command injection, template injection
- 역직렬화(pickle) 사용
- SSRF/외부 요청 남용
- secrets 노출 (로그/환경/설정)
- dependency/supply-chain (typosquatting, 취약 버전)

---

## Secure Defaults

- 입력 검증/스키마(예: pydantic 등)로 “신뢰 경계”를 명확히
- DB는 parameterized query/ORM 사용
- OS 명령 실행은 최소화, 필요 시 allowlist + 인자 배열 형태로 호출
- `pickle`/`yaml.load(unsafe)` 같은 위험 기능 금지
- 로깅은 기본 마스킹(토큰/비밀번호)

---

## Do

- SQL: 바인딩 파라미터 사용 (`?`, `%s`, named params)
- 외부 요청: allowlist, timeout, redirect 제한, response size 제한
- 파일 처리: 업로드/다운로드 경로 분리 + 경로 정규화 + 확장자/콘텐츠 검증
- 패키지:
  - pinning(버전 고정), 정기 업데이트, lockfile 관리
  - 취약점 스캔(SBOM/Dependabot 등)과 결합

---

## Don’t

- `eval`, `exec`로 외부 입력 실행
- `subprocess`에 `shell=True` + 사용자 입력 결합
- `pickle.loads`에 외부 데이터 사용
- 민감정보를 예외 메시지/로그에 출력

---

## High-Risk Patterns

- `requests.get(user_url)` 형태의 SSRF
- `os.system("cmd " + user)` / `subprocess.run(user_string, shell=True)`
- 템플릿에 raw HTML 삽입(프레임워크 escaping 우회)
- 디버그 모드 상시 활성화

---

## Verification Checklist

- [ ] 외부 입력이 DB/OS/템플릿에 직접 연결되지 않음
- [ ] `pickle`, unsafe YAML 로딩 사용 없음
- [ ] HTTP 요청에 timeout/allowlist/redirect 제한 존재
- [ ] secrets가 코드/로그/리포지토리에 없음
- [ ] dependencies 버전 고정 및 업데이트/스캔 흐름 존재