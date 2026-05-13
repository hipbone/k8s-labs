# Phase 4 · Configuration

> 이미지에 설정을 박지 말자. 환경마다 다른 값은 **Pod 바깥에서 주입**한다.

## 이 단계에서 배울 것

- **ConfigMap**: 비밀이 아닌 설정값. env / 커맨드 인자 / 파일로 주입 가능.
- **Secret**: 민감 정보. base64 인코딩일 뿐, **암호화는 아님**. 운영에서는 외부 시크릿 매니저 연동.
- **Downward API**: Pod 자신의 메타데이터(이름, namespace, label 등)를 컨테이너에 노출.

## 주입 방식 비교

| 방식 | 변경 시 반영 | 용도 |
|------|--------------|------|
| `env` (key 단위) | Pod 재시작 필요 | 단순 설정 값 |
| `envFrom` (전체) | Pod 재시작 필요 | 여러 키 한 번에 |
| `volume` mount | 파일이 자동 업데이트됨 (kubelet sync 주기) | 설정 파일 통째로 (nginx.conf 같은) |

## 핵심 질문

- ConfigMap 과 Secret 의 진짜 차이는?
- Secret 을 안전하게 다루기 위한 운영상의 추가 장치는 무엇이 있나? (encryption-at-rest, RBAC, sealed-secrets, external-secrets…)
- Volume 마운트 방식이 env 방식보다 좋은 케이스는?

> ⏳ 학습 시작 시 README 와 yaml 들을 채워나간다.
