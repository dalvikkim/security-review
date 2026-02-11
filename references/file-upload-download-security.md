---
scope:
  domain: ["file-upload", "file-download"]
priority: 90
---

# File Upload & Download Security

## Secure Defaults

- 파일 타입 서버 검증
- 업로드 경로 분리
- 실행 권한 제거

---

## Common Attacks

- Web Shell 업로드
- Path Traversal
- MIME 타입 우회

---

## Do

- 랜덤 파일명 사용
- 다운로드 시 Content-Disposition 고정
