---
scope:
  stack: ["ci-cd"]
priority: 70
---

# CI/CD General Security

## Secure Defaults

- Build provenance / artifact integrity 고려 (서명, attestation)
- 이미지 스캔(취약점 + secret) 게이트 적용
- 최소 권한 토큰 사용 (short-lived)
- 빌드 로그에 secrets 마스킹

## Checklist

- [ ] SBOM 생성 및 저장
- [ ] 이미지 스캔 실패 시 배포 차단
- [ ] 서명/검증 정책 존재