# Phase 0 · 환경 준비

> 이번 단계 목표: WSL2 위에 Docker, kubectl, kind, helm을 설치하고 **멀티 노드 kind 클러스터**를 띄운다.

---

## 왜 kind인가

Kubernetes는 "분산 시스템"이다. 실제 운영 클러스터처럼 **여러 노드**가 있어야 스케줄러, 서비스 라우팅, PVC 친화도 같은 것들이 의미 있게 동작한다.

- **kind**(Kubernetes IN Docker)는 컨테이너 하나하나를 노드처럼 띄워준다. 노트북 한 대로 멀티 노드 클러스터를 흉내낼 수 있다.
- minikube는 노드를 늘리려면 VM/Docker를 여러 개 띄워야 해서 조작이 번거롭고 무겁다.
- k3d는 가볍지만 k3s 기반이라 일부 리소스(StorageClass, 인증) 동작이 일반 k8s와 미묘하게 다르다.
- Crossplane 공식 가이드도 kind를 기준으로 작성되어 있다.

> 이 저장소에서 만든 yaml은 minikube, EKS 같은 다른 클러스터에서도 거의 그대로 동작한다.

---

## 설치

### 1) Docker 동작 확인 (WSL 안에서)

```bash
docker run --rm hello-world
```

> Docker Desktop을 쓴다면 *Settings → Resources → WSL Integration* 에서 사용 중인 배포판을 켜둬야 한다.

### 2) kubectl

```bash
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl
kubectl version --client
```

### 3) kind

```bash
curl -Lo ./kind https://kind.sigs.k8s.io/dl/v0.23.0/kind-linux-amd64
chmod +x ./kind && sudo mv ./kind /usr/local/bin/kind
kind version
```

### 4) helm

```bash
curl https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3 | bash
helm version
```

---

## 클러스터 생성

```bash
kind create cluster --config kind-cluster.yaml
```

확인:

```bash
kubectl cluster-info --context kind-k8s-labs
kubectl get nodes
# NAME                     STATUS   ROLES           AGE   VERSION
# k8s-labs-control-plane   Ready    control-plane   ...   v1.30.x
# k8s-labs-worker          Ready    <none>          ...   v1.30.x
# k8s-labs-worker2         Ready    <none>          ...   v1.30.x

kubectl get pods -A
```

### 클러스터 설정 한눈에 보기

`kind-cluster.yaml`에 들어간 의도:

| 설정 | 왜 |
|------|------|
| `nodes: 1 control-plane + 2 worker` | 노드 분산 스케줄링/affinity 실습을 가능하게 함 |
| `extraPortMappings: 80, 443` | Phase 3(Ingress)에서 브라우저로 바로 접근하기 위함 |
| `extraPortMappings: 30080` | Phase 3(NodePort) 실습용 |
| `node-labels: ingress-ready=true` | nginx-ingress가 control-plane 노드에 스케줄되도록 |

---

## 정리

```bash
kind delete cluster --name k8s-labs
```

> 학습 중 클러스터가 꼬이면 망설이지 말고 지우고 다시 만들자. kind의 진짜 장점이다.

---

## WSL 환경 자주 만나는 함정

- `hostPath`는 **WSL 파일시스템 경로**(예: `/mnt/data`) 사용. `/mnt/c/...` 같은 Windows 마운트 경로는 권한/성능 문제가 잦다.
- kind 노드는 컨테이너이므로 `hostPath` 의 실체는 **노드 컨테이너 내부**다. 진짜 WSL 호스트에 보고 싶다면 kind `extraMounts` 설정이 필요.
- `LoadBalancer` 타입 Service는 kind에서 기본 동작하지 않는다. Phase 3에서 `cloud-provider-kind` 로 해결한다.
- 메모리가 부족해서 Pod이 `OOMKilled` 되거나 `Pending` 상태로 멈춘다면 Docker Desktop의 Resources에서 메모리를 4GB 이상으로 늘리자.

---

## 다음

→ [Phase 1: Pods](../01-pods/README.md)
