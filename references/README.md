# Security Best Practices – References

이 디렉토리는 **언어 무관(Security Language-Agnostic)** 보안 베스트 프랙티스를 제공하기 위한 기준 문서 모음입니다.

이 문서들은 `security-best-practices` SKILL이 다음 목적을 수행할 때 사용됩니다.

- Secure-by-default 코드 작성
- 고위험 보안 이슈 식별
- 보안 베스트 프랙티스 리포트 생성
- 수정(Fix) 시 근거 문서 제공

---

## 적용 우선순위

Agent는 아래 우선순위에 따라 reference 문서를 탐색하고 적용합니다.

1. 프로젝트 또는 조직 내부 보안 가이드
2. references 디렉토리 내 문서
   - framework → language → stack → domain → general 순
3. 공식 문서
4. 범용 보안 표준 (OWASP, NIST, CERT 등)

---

## 디렉토리 구조 요약

references/
├─ stacks/ # 애플리케이션 유형(웹, API 등)
├─ domains/ # 보안 영역/취약점 도메인
├─ languages/ # 언어 공통 보안 가이드
├─ frameworks/ # 프레임워크/런타임별 가이드
├─ checklists/ # 점검 체크리스트
├─ patterns/ # 보안 코드 패턴/템플릿
└─ mappings/ # 자동 탐지를 위한 규칙


---

## 사용 원칙

- 이 문서들은 **취약점 스캐너 결과를 대체하지 않습니다**
- 명확한 근거가 없는 경우, 고확신(high-confidence) 이슈만 다룹니다
- 프로젝트 특성상 우회가 필요한 경우, 그 이유를 문서화하는 것을 권장합니다
