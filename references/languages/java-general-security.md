---
scope:
  language: ["java"]
priority: 80
applies_to:
  - "web services"
  - "enterprise apps"
  - "libraries processing untrusted data"
---

# Java General Security Guide

## Core Risks (Java에서 자주 문제되는 영역)

- Injection: SQL/LDAP/EL/OGNL(프레임워크 의존 포함)
- Unsafe deserialization (Java serialization)
- XXE (XML 파서 기본 설정)
- SSRF(HTTP client), open redirect
- 인증/인가 누락 및 IDOR
- 로그/예외에 민감정보 노출

---

## Secure Defaults

- DB 접근: PreparedStatement/ORM로 파라미터 바인딩
- XML 처리:
  - DTD/External entity 기본 차단(XXE 방지)
- 직렬화:
  - Java native serialization 지양
- 보안 설정을 코드/구성에서 명시적으로 “secure default”로 고정

---

## Do

- 입력 검증을 DTO/Validator로 일관되게 적용
- crypto는 표준 라이브러리 + 안전한 모드(AEAD) 선호
- 파일/경로:
  - 경로 정규화 후 루트 디렉토리 하위만 허용
- 인증/인가:
  - 컨트롤러 수준뿐 아니라 서비스/도메인 레벨에서도 권한 확인

---

## Don’t

- 문자열 연결로 SQL/LDAP 쿼리 구성
- `ObjectInputStream`로 외부 데이터 역직렬화
- XML 파서 기본값(외부 엔티티 허용) 사용
- 스택트레이스를 사용자에게 노출

---

## High-Risk Patterns

- `Statement.execute("..."+user)`
- `new ObjectInputStream(socket.getInputStream()).readObject()`
- `DocumentBuilderFactory`/`SAXParserFactory` 보안 옵션 미설정
- 액세스 제어를 “UI/프론트엔드”에만 의존

---

## Verification Checklist

- [ ] SQL/LDAP 등 쿼리 구성에 바인딩 사용
- [ ] Java serialization/XXE 방어 설정 적용
- [ ] 예외/로그 민감정보 마스킹
- [ ] 인증/인가 체크가 서버에서 강제됨
- [ ] 의존성 취약점 스캔 및 버전 관리 존재