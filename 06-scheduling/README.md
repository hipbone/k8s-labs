# Phase 6 · Scheduling & Autoscaling

> 어디에 / 얼마나 / 어떤 조건으로 띄울지 — 클러스터를 효율적으로 쓰는 단계.

## 이 단계에서 배울 것

- **Label / Selector**: 거의 모든 리소스 관계의 기본.
- **nodeSelector**: 가장 단순한 노드 선택.
- **Affinity / Anti-Affinity**: "이 Pod 옆에" / "이 Pod 떨어져서" 같은 정교한 배치.
- **Taints & Tolerations**: 노드가 "나 이런 Pod 만 받을래" 를 선언.
- **HPA (Horizontal Pod Autoscaler)**: 부하에 따라 Replica 수 자동 조절.
- **ResourceQuota / LimitRange**: 네임스페이스 단위 자원 통제.

## 핵심 질문

- Affinity 와 nodeSelector 의 차이는?
- Anti-Affinity 가 멀티노드 배포에서 왜 중요한가? (가용성)
- HPA 가 동작하려면 무엇이 설치되어 있어야 하는가? (`metrics-server`)
- ResourceQuota 가 적용된 네임스페이스에 `requests` 없는 Pod 을 만들면 어떻게 되는가?

> ⏳ 학습 시작 시 README 와 yaml 들을 채워나간다.
