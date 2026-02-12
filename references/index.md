# Security Best Practices Reference Index

이 문서는 `references/` 디렉토리의 **진입점(index)** 입니다.

Agent는 이 문서를 참고하여:
- 어떤 문서가 어떤 상황에 적합한지
- 어떤 순서로 문서를 로드해야 하는지
를 판단합니다.

---

## Document Loading Priority

1. **Domain Guides (최우선)**: 특정 취약점/기능에 대한 상세 가이드
2. **Stack Guides**: 아키텍처/스택 수준의 보안 가이드
3. **Language Guide**: 언어별 고유 취약점 패턴
4. **Checklist**: 최종 검토용 체크리스트

---

## Domain Guides (취약점/기능 단위)

| File | Use When |
|------|----------|
| `authn-authz-security.md` | 인증, 인가, 세션, 권한 검토 |
| `secrets-management-security.md` | 하드코딩된 비밀, 자격증명 관리 검토 |
| `input-validation-security.md` | 입력 검증, Injection 취약점 검토 |
| `rce-command-exec-security.md` | OS 명령 실행, shell 호출 검토 |
| `ssrf-security.md` | 외부 HTTP 요청, URL fetch 검토 |
| `file-upload-download-security.md` | 파일 업로드/다운로드 기능 검토 |
| `backdoor-malicious-behavior-security.md` | 숨겨진 기능, 의심스러운 패턴 검토 |

---

## Stack Guides (아키텍처/환경)

| File | Use When |
|------|----------|
| `api-general-security.md` | REST/GraphQL API 엔드포인트 검토 |
| `web-backend-general-security.md` | 서버 사이드 웹 애플리케이션 검토 |
| `docker-container-security.md` | Dockerfile, 컨테이너 이미지 검토 |
| `ci-cd-general-security.md` | CI/CD 파이프라인, 빌드 스크립트 검토 |

---

## Language Guide

| File | Use When |
|------|----------|
| `language-specific-security.md` | 특정 언어 고유의 취약점 패턴 검토 (C, Go, Java, JavaScript, Python, Vue.js 등) |

---

## Checklist

| File | Use When |
|------|----------|
| `code-review-security-checklist.md` | 최종 보안 검토 체크리스트 |

---

## Quick Reference: When to Load What

### API/Backend 코드 리뷰 시
1. `api-general-security.md` 또는 `web-backend-general-security.md`
2. `input-validation-security.md`
3. `authn-authz-security.md`
4. `language-specific-security.md` (해당 언어 섹션)

### 파일 처리 기능 리뷰 시
1. `file-upload-download-security.md`
2. `input-validation-security.md` (경로 검증)

### 외부 통신 기능 리뷰 시
1. `ssrf-security.md`
2. OS 명령 실행이 있으면 `rce-command-exec-security.md`

### 인증/권한 로직 리뷰 시
1. `authn-authz-security.md`
2. `secrets-management-security.md`

### 의심스러운 코드 발견 시
1. `backdoor-malicious-behavior-security.md`
2. `rce-command-exec-security.md`

### Dockerfile/CI 리뷰 시
1. `docker-container-security.md`
2. `ci-cd-general-security.md`
3. `secrets-management-security.md`

---

## Conflict Resolution

- **Domain 가이드 > Stack 가이드**: 특정 취약점에 대해서는 Domain 가이드 우선
- **더 구체적인 가이드 우선**: 일반 원칙보다 구체적인 규칙 우선
- **더 제한적인 규칙 적용**: 충돌 시 보안상 더 안전한 쪽 선택

---

## Notes

- 공통 보안 원칙(입력 검증, 인증, Secrets 등)은 `SKILL.md`에 정의됨
- 각 가이드 파일의 `scope`와 `priority` 메타데이터 참조
- 프로젝트 특화 가이드가 있는 경우 해당 가이드가 최우선
