# k8s-labs

> Kubernetes 를 **개념 → 실습 → 토이 프로젝트** 흐름으로 학습하기 위한 저장소.
> 이어서 **Crossplane** 학습으로 연결되는 것을 목표로 설계되었습니다.

---

## 학습 목표

- Kubernetes 핵심 리소스를 **직접 yaml 로 만들어보며** 손에 익힌다.
- "왜 이 리소스가 필요한가" 의 맥락을 단계마다 짚고 넘어간다.
- 마지막에 작은 3-tier 토이 앱(`voting-app`)을 배포해 모든 개념을 한 번에 엮어본다.
- CRD / Operator 패턴까지 닿아 Crossplane 학습으로 무리 없이 이어진다.

---

## 환경

- **OS**: Windows + WSL2 (Ubuntu)
- **컨테이너 런타임**: Docker (Desktop의 WSL2 backend 또는 WSL 내 Docker)
- **클러스터 엔진**: [kind](https://kind.sigs.k8s.io/) (Kubernetes IN Docker)
- **CLI**: `kubectl`, `helm`, `kind`

> 자세한 설치/실행은 → [`00-setup/`](./00-setup/README.md)

---

## 학습 로드맵

각 Phase 폴더에 **개념 설명 README + 실습 yaml** 이 들어 있습니다. 체크박스를 채워가며 진행하세요.

| Phase | 디렉터리 | 다루는 내용 | 상태 |
|:----:|---|---|:---:|
| 0 | [`00-setup/`](./00-setup/) | WSL + kind 환경 준비, 멀티노드 클러스터 | ✅ |
| 1 | [`01-pods/`](./01-pods/) | Pod, 멀티 컨테이너, Init, Probe, Resources | ✅ |
| 2 | [`02-workloads/`](./02-workloads/) | ReplicaSet, Deployment, DaemonSet, Job/CronJob | ⏳ |
| 3 | [`03-networking/`](./03-networking/) | Service(ClusterIP/NodePort/Headless), Ingress, DNS | ⏳ |
| 4 | [`04-config/`](./04-config/) | ConfigMap, Secret, Downward API | ⏳ |
| 5 | [`05-storage/`](./05-storage/) | emptyDir, hostPath, PV/PVC, StorageClass, StatefulSet | ⏳ |
| 6 | [`06-scheduling/`](./06-scheduling/) | Affinity, Taint/Toleration, HPA, ResourceQuota | ⏳ |
| 7 | [`07-security/`](./07-security/) | ServiceAccount, RBAC, SecurityContext, NetworkPolicy | ⏳ |
| 8 | [`08-extensions/`](./08-extensions/) | Helm, CRD, Operator 패턴 (→ Crossplane 준비) | ⏳ |
| 99 | [`99-toy-voting/`](./99-toy-voting/) | 3-tier 토이 프로젝트 (Frontend + API + DB) | ⏳ |

> ✅ = README/yaml 작성 완료, 실습 가능 · ⏳ = 학습 시작 시 함께 채워나갈 예정

---

## 빠른 시작

### 1. 환경 준비

```bash
# 도구 설치는 00-setup/README.md 참고
kind create cluster --config 00-setup/kind-cluster.yaml
kubectl get nodes
```

### 2. 첫 실습 (Phase 1)

```bash
kubectl apply -f 01-pods/01-single.yaml
kubectl -n lab-pods get pod -w
```

그 다음은 [`01-pods/README.md`](./01-pods/README.md) 를 따라가면 됩니다.

---

## 디렉터리 구조

```
k8s-labs/
├── README.md              # 이 문서 — 로드맵 & 빠른 시작
├── CLAUDE.md              # AI 어시스턴트용 컨텍스트
├── 00-setup/              # 환경 준비 (kind cluster 정의)
├── 01-pods/               # Phase 1
├── 02-workloads/          # Phase 2
├── 03-networking/         # Phase 3
├── 04-config/             # Phase 4
├── 05-storage/            # Phase 5
├── 06-scheduling/         # Phase 6
├── 07-security/           # Phase 7
├── 08-extensions/         # Phase 8 (→ Crossplane 준비)
└── 99-toy-voting/         # 토이 프로젝트
```

각 Phase 폴더 안:

```
0X-name/
├── README.md          # 개념 설명 + 실습 가이드 + 정리 질문 ("책" 역할)
├── 01-something.yaml  # 작은 단위로 나뉜 실습 yaml
├── 02-something.yaml
└── ...
```

---

## 자주 쓰는 명령

```bash
# 클러스터
kind get clusters
kind delete cluster --name k8s-labs

# 컨텍스트 / 네임스페이스
kubectl config current-context
kubectl config set-context --current --namespace=lab-pods

# 리소스 조회
kubectl get all -n lab-pods
kubectl get pod -o wide                       # 어느 노드에 떴는지
kubectl describe pod <name> -n lab-pods
kubectl logs -f <pod> -c <container> -n lab-pods
kubectl exec -it <pod> -n lab-pods -- sh

# 디버깅
kubectl get events -n lab-pods --sort-by=.lastTimestamp
kubectl run tmp --rm -it --image=busybox:1.36 -- sh
```

---

## 토이 프로젝트 미리보기 — `voting-app`

모든 Phase 가 끝나면 아래 구성을 직접 배포합니다.

```
           Ingress (vote.localtest.me)
                     │
                     ▼
       ┌──────────────────────────┐
       │   frontend (nginx)       │
       └──────────┬───────────────┘
                  │
       ┌──────────▼───────────────┐
       │   api (Node.js/Python)   │  ← HPA
       └──────────┬───────────────┘
                  │
       ┌──────────▼───────────────┐
       │   db (PostgreSQL)        │  ← StatefulSet + PVC
       └──────────────────────────┘
```

자세한 내용은 [`99-toy-voting/README.md`](./99-toy-voting/README.md).
