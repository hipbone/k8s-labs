# Phase 5 · Storage

> Pod 은 일회용이다. 데이터를 살리려면 **Pod 의 수명과 분리된 저장소**가 필요하다.

## 이 단계에서 배울 것

- **emptyDir**: Pod 안의 임시 디스크. Pod 죽으면 사라짐.
- **hostPath**: 노드의 파일시스템. (kind 에서는 노드 컨테이너 안)
- **PersistentVolume (PV)**: 클러스터 차원에서 등록된 실제 저장소 한 조각.
- **PersistentVolumeClaim (PVC)**: 앱이 "이 정도 크기의 저장소 줘" 하고 요청하는 추상.
- **StorageClass**: PV 를 **자동으로 만들어주는** 프로비저너 (동적 프로비저닝).
- **StatefulSet + `volumeClaimTemplates`**: 인스턴스마다 자기만의 PVC 를 갖는 워크로드.

## 핵심 질문

- PV 와 PVC 가 분리되어 있는 이유는? (관리자 vs 개발자의 역할 분리)
- AccessMode `ReadWriteOnce` / `ReadOnlyMany` / `ReadWriteMany` 의 차이는?
- StorageClass 의 `reclaimPolicy` 가 `Delete` vs `Retain` 일 때 PVC 를 지우면 무슨 일이 일어나는가?
- StatefulSet 은 왜 일반 Deployment 로 대체할 수 없는가?

## kind 환경 메모

- kind 는 기본 StorageClass `standard` (rancher-local-path) 를 제공한다 → 동적 프로비저닝 바로 가능.
- `hostPath` 의 실체는 노드 컨테이너 내부 디렉터리. 진짜 호스트에 보고 싶다면 cluster 정의에 `extraMounts` 필요.

> ⏳ 학습 시작 시 README 와 yaml 들을 채워나간다.
