# Phase 2 · Workloads

> 직접 만든 Pod은 노드가 죽으면 같이 사라진다. **컨트롤러**가 이걸 대신 관리해준다.

## 이 단계에서 배울 것

- **ReplicaSet**: "Pod이 항상 N개 있도록" 보장하는 가장 단순한 컨트롤러.
- **Deployment**: ReplicaSet 위에서 **롤링 업데이트 / 롤백**까지 해주는 사실상의 표준.
- **DaemonSet**: 모든 노드에 하나씩. (로그 수집기, 모니터링 에이전트 패턴)
- **Job / CronJob**: 일회성 / 주기 실행 워크로드.

## 핵심 질문

- Deployment, ReplicaSet, Pod 의 관계는?
- 롤링 업데이트 중에 다운타임이 생기지 않으려면 어떤 설정이 필요한가? (`maxSurge`, `maxUnavailable`, readinessProbe)
- 롤백은 어떻게 동작하는가? (`revisionHistoryLimit`)
- DaemonSet 은 왜 Deployment 로 대체할 수 없는가?
- StatefulSet 은 왜 Phase 5(Storage) 에서 다루는가?

> ⏳ 학습 시작 시 README 와 yaml 들을 채워나간다.
