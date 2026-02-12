---
scope:
  domain: ["file-upload", "file-download", "file-handling"]
applies_to:
  - "applications with file upload functionality"
  - "file download/export features"
  - "file processing services"
priority: 90
---

# File Upload & Download Security

## Overview

파일 업로드/다운로드 기능은 **웹 애플리케이션에서 가장 위험한 기능** 중 하나입니다.
잘못 구현된 파일 처리는 원격 코드 실행, 서버 장악, 데이터 유출로 이어질 수 있습니다.

---

## Common Attack Vectors

### File Upload Attacks

| Attack | Description | Impact |
|--------|-------------|--------|
| Web Shell Upload | 실행 가능한 스크립트(.php, .jsp, .aspx) 업로드 | RCE, 서버 장악 |
| Path Traversal | `../` 사용하여 의도하지 않은 경로에 저장 | 파일 덮어쓰기, 설정 변조 |
| MIME Type Bypass | Content-Type 조작으로 검증 우회 | 악성 파일 업로드 |
| Double Extension | `file.php.jpg` 등 이중 확장자 | 실행 파일 위장 |
| Null Byte Injection | `file.php%00.jpg` | 확장자 검증 우회 |
| ZIP Slip | 악성 경로가 포함된 압축 파일 | Path traversal via archive |
| XML/XXE in Files | SVG, DOCX 등에 포함된 XXE | SSRF, 파일 읽기 |
| Polyglot Files | 여러 포맷으로 해석 가능한 파일 | 검증 우회 |

### File Download Attacks

| Attack | Description | Impact |
|--------|-------------|--------|
| Path Traversal | `../../etc/passwd` 등 | 민감 파일 노출 |
| IDOR | 다른 사용자 파일 접근 | 데이터 유출 |
| Content-Type Confusion | 잘못된 MIME 설정 | XSS, 브라우저 취약점 |

---

## Secure Defaults

### Upload
- 업로드 디렉토리를 **웹 루트 외부**에 배치
- 업로드된 파일에 **실행 권한 제거**
- **랜덤 파일명** 사용 (원본 파일명 저장은 DB에)
- 파일 크기 **제한** 설정
- 허용된 확장자 **allowlist** 적용

### Download
- 파일 경로는 **DB ID로 조회** (경로 직접 전달 금지)
- **Content-Disposition** 헤더로 다운로드 강제
- 적절한 **Content-Type** 설정
- **접근 권한 확인** 후 제공

---

## Do

### Upload

- **서버 측 검증**: MIME type, 확장자, magic bytes 모두 확인
- **파일명 정규화**: 특수문자, 경로 구분자 제거
- **저장 경로 분리**: 업로드 파일은 별도 스토리지/CDN
- **안티바이러스 스캔**: 가능한 경우 업로드 시 스캔
- **파일 크기 제한**: 서버 및 애플리케이션 레벨 모두
- **이미지 재처리**: 업로드된 이미지는 re-encode하여 메타데이터/악성코드 제거

### Download

- **ID 기반 조회**: `/download?id=123` (경로 노출 X)
- **권한 확인**: 요청자가 해당 파일에 접근 가능한지 확인
- **Content-Type 고정**: 알려진 타입만 제공, 나머지는 `application/octet-stream`
- **Filename 인코딩**: Content-Disposition에서 파일명 안전하게 인코딩

---

## Don't

- **클라이언트 제공 MIME type만 신뢰**
- **원본 파일명 그대로 저장**: Path traversal 위험
- **업로드 디렉토리에서 직접 실행 허용**
- **경로를 파라미터로 직접 전달**: `/download?path=/uploads/file.pdf`
- **모든 확장자 허용**
- **파일 크기 무제한**

---

## High-Risk Patterns

### Path Traversal (Upload)
```python
# BAD
filename = request.files['file'].filename
path = os.path.join(UPLOAD_DIR, filename)

# GOOD
import os
import uuid
filename = request.files['file'].filename
safe_name = str(uuid.uuid4()) + os.path.splitext(filename)[1]
# 확장자도 allowlist 검증 필요
```

### Path Traversal (Download)
```python
# BAD
@app.route('/download')
def download():
    path = request.args.get('path')
    return send_file(path)

# GOOD
@app.route('/download/<int:file_id>')
def download(file_id):
    file_record = db.get_file(file_id)
    if not user_can_access(current_user, file_record):
        abort(403)
    return send_file(file_record.path)
```

### MIME Type Validation
```python
# BAD: 클라이언트 제공 type만 확인
if request.files['file'].content_type == 'image/jpeg':
    save_file()

# GOOD: Magic bytes 확인
import magic
mime = magic.from_buffer(file.read(2048), mime=True)
if mime not in ALLOWED_MIMES:
    abort(400)
```

### ZIP Slip Prevention
```python
# BAD
import zipfile
with zipfile.ZipFile(uploaded_file) as z:
    z.extractall(target_dir)

# GOOD
import zipfile
import os
with zipfile.ZipFile(uploaded_file) as z:
    for member in z.namelist():
        target_path = os.path.join(target_dir, member)
        # Ensure the target is within the extraction directory
        if not os.path.abspath(target_path).startswith(os.path.abspath(target_dir)):
            raise ValueError("Attempted path traversal in zip file")
    z.extractall(target_dir)
```

---

## Content-Type Guidelines

### Safe to Display
- `image/jpeg`, `image/png`, `image/gif`, `image/webp`
- `application/pdf` (주의: JavaScript 포함 가능)
- `text/plain`

### Force Download
- 알 수 없는 타입
- 실행 가능 파일
- Office 문서 (매크로 위험)

### Never Allow
- `text/html` (XSS)
- `application/javascript`
- `image/svg+xml` (XSS via SVG)

---

## Verification Checklist

### Upload
- [ ] 서버 측 MIME type 검증 (magic bytes)
- [ ] 확장자 allowlist 적용
- [ ] 파일명 정규화 (랜덤 생성 권장)
- [ ] 업로드 디렉토리가 웹 루트 외부
- [ ] 파일 크기 제한 존재
- [ ] 실행 권한 제거
- [ ] 압축 파일 해제 시 path traversal 방어

### Download
- [ ] 경로 직접 전달 없음 (ID 기반)
- [ ] 접근 권한 확인
- [ ] Content-Disposition 헤더 설정
- [ ] Content-Type 적절히 설정
- [ ] 파일명 인코딩 안전 처리

---

## Severity Guidance

- **Critical**: Web shell 업로드 가능, 임의 파일 덮어쓰기
- **High**: Path traversal로 민감 파일 접근, IDOR
- **Medium**: MIME type 검증 우회, 부적절한 Content-Type
- **Low**: 파일 크기 미제한, 불완전한 검증

---

## Notes

- 파일 업로드는 **가장 신중하게 구현해야 하는 기능**
- 가능하다면 **별도 스토리지 서비스**(S3, GCS 등) 사용 권장
- 민감 파일은 **서명된 URL** 또는 **별도 인증** 후 제공
- 이미지의 경우 **리사이징/재인코딩**으로 메타데이터 및 악성코드 제거
