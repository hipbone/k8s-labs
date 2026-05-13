# Phase 7 · Security & RBAC

> "기본값 = 안전" 이 아닌 Kubernetes. 직접 잠가야 한다.

## 이 단계에서 배울 것

- **Namespace**: 가장 가벼운 격리 단위.
- **ServiceAccount**: Pod 이 API 서버에 자신을 증명하는 신원.
- **Role / ClusterRole**: 무엇을 할 수 있는지 (permission).
- **RoleBinding / ClusterRoleBinding**: 누구에게 그 권한을 줄지.
- **SecurityContext**: 컨테이너의 권한 축소 (runAsNonRoot, readOnlyRootFilesystem, capabilities drop).
- **NetworkPolicy**: Pod 간 통신 제한 (CNI 가 지원해야 동작).

## 핵심 질문

- Role 과 ClusterRole 의 차이는?
- Pod 이 명시적으로 ServiceAccount 를 지정하지 않으면 어떻게 되는가?
- 컨테이너를 `runAsNonRoot: true` 로 두면 어떤 공격면이 줄어드는가?
- kind 기본 CNI 가 NetworkPolicy 를 지원하는가? 아니면 Calico/Cilium 으로 교체해야 하는가?

> ⏳ 학습 시작 시 README 와 yaml 들을 채워나간다.
