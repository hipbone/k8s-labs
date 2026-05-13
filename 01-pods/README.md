# Phase 1 · Pods

> 이번 단계 목표: Kubernetes의 **가장 작은 배포 단위**인 Pod를 이해하고, 컨테이너 그룹 / 초기화 / 헬스 체크 / 리소스 제어를 직접 만져본다.

---

## 핵심 개념

### Pod이란?

> **"같은 노드 위에서, 같은 네트워크와 스토리지를 공유하는 컨테이너 묶음"**

흔한 오해: "Pod = 컨테이너". 아니다. Pod은 **컨테이너들의 그룹**이고, 대부분 한 개만 들어 있을 뿐이다.

같은 Pod 안의 컨테이너들은:

- **같은 IP**를 쓴다 → `localhost` 로 서로 호출 가능.
- **같은 볼륨**을 마운트할 수 있다 → 파일을 공유 가능.
- **같은 노드**에 함께 스케줄된다 → 살아도 같이 살고, 죽어도 같이 죽는다.

### 왜 컨테이너가 아니라 Pod인가?

Docker만 쓰던 시절엔 "프로세스 = 컨테이너" 였다. 그런데 현실엔 "로그를 수집해주는 부속 프로세스", "프록시를 앞에 세우는 사이드카" 같이 **메인 프로세스에 딱 붙어다녀야 하는 보조 컨테이너**가 필요했다. Pod은 이런 묶음을 한 단위로 다루기 위한 추상화다.

### Pod의 생명주기

| Phase | 의미 |
|-------|------|
| `Pending` | 스케줄링 대기 또는 이미지 pull 중 |
| `Running` | 컨테이너 중 최소 하나가 실행 중 |
| `Succeeded` | 모든 컨테이너가 0으로 종료 (Job 같은 일회성 워크로드) |
| `Failed` | 컨테이너 중 하나라도 비정상 종료 |
| `CrashLoopBackOff` | (조건) 자꾸 죽어서 재시작 간격이 점점 길어지는 상태 |

> Phase는 `kubectl get pod` 의 `STATUS` 칼럼에서 볼 수 있지만, 같은 칼럼에 `Init:0/1`, `ContainerCreating`, `ImagePullBackOff` 같은 더 구체적인 사유가 나오기도 한다.

### Pod은 직접 만들면 안 된다 (실무 한정)

학습용으론 직접 만들지만, 실무에서 Pod를 직접 만드는 일은 거의 없다. 노드가 죽으면 그 Pod는 그대로 사라진다. 이걸 대신 관리해주는 게 **Deployment, StatefulSet, DaemonSet, Job** 같은 컨트롤러다. Phase 2에서 다룬다.

---

## 실습

> 모든 실습은 `lab-pods` 네임스페이스에서 진행한다. 첫 yaml 적용 시 함께 생성된다.

### 실습 1 · 단일 컨테이너 Pod

```bash
kubectl apply -f 01-single.yaml
kubectl -n lab-pods get pod -w        # Ctrl+C 로 빠져나오기

# 상세 정보
kubectl -n lab-pods describe pod nginx

# 컨테이너 안으로
kubectl -n lab-pods exec -it nginx -- sh

# Pod로 port-forward 해서 브라우저로
kubectl -n lab-pods port-forward pod/nginx 8080:80
# → http://localhost:8080
```

**한 줄 체크**

- `kubectl describe` 의 *Events* 섹션을 본다. 스케줄 → 이미지 pull → 컨테이너 시작 순서가 보인다.
- 노드 분포를 확인: `kubectl -n lab-pods get pod -o wide` (어떤 worker에 스케줄됐는지)

---

### 실습 2 · 멀티 컨테이너 Pod (사이드카 패턴)

`writer` 컨테이너가 2초마다 로그 한 줄을 남기고, `reader` 컨테이너가 같은 파일을 `tail -F` 로 따라간다.

```bash
kubectl apply -f 02-multi-container.yaml

# reader가 보는 로그 (writer가 쓴 게 보여야 정상)
kubectl -n lab-pods logs sidecar-demo -c reader -f

# 두 컨테이너가 같은 Pod에 들어있음을 확인
kubectl -n lab-pods get pod sidecar-demo -o jsonpath='{.spec.containers[*].name}'
# → writer reader
```

**핵심 포인트**

- 두 컨테이너가 `emptyDir` 볼륨을 같이 마운트해서 **파일을 공유**한다.
- `emptyDir` 은 Pod이 죽으면 같이 사라진다. 진짜 영구 저장은 Phase 5에서.
- 사이드카 패턴은 *로그 수집 / 인증 프록시 / 메트릭 노출* 등에서 흔히 쓰인다.

---

### 실습 3 · Init Container

메인 컨테이너가 의존하는 무언가(DB, 마이그레이션, 권한 세팅 등)를 먼저 준비할 때 쓴다.

```bash
kubectl apply -f 03-init.yaml

# 처음엔 Init:0/1, 5초 뒤 Running 으로 바뀐다
kubectl -n lab-pods get pod init-demo -w

# init 컨테이너의 로그
kubectl -n lab-pods logs init-demo -c wait-for-db
```

**핵심 포인트**

- Init Container는 **순서대로**, **하나씩**, **성공할 때까지 재시도**된다.
- 모든 Init Container가 성공해야 메인 컨테이너가 시작된다.
- 실패 사유는 `kubectl describe` 의 *Init Containers* 섹션에 나온다.

---

### 실습 4 · Probe 3종 세트

```bash
kubectl apply -f 04-probes.yaml
kubectl -n lab-pods describe pod probes-demo | grep -A2 -E '(Liveness|Readiness|Startup)'

# 일부러 깨보기: nginx의 index를 지워서 readiness를 실패시키자.
kubectl -n lab-pods exec probes-demo -- rm /usr/share/nginx/html/index.html

# 잠시 뒤 READY 가 0/1 이 됨 (= 트래픽에서 제외)
kubectl -n lab-pods get pod probes-demo -w
```

> 다시 살리고 싶으면 Pod 삭제 후 재생성:
> `kubectl -n lab-pods delete pod probes-demo && kubectl apply -f 04-probes.yaml`

**세 Probe의 역할**

| Probe | 실패하면? | 언제 쓰나 |
|-------|-----------|-----------|
| `startupProbe` | 재시작 카운트는 늘지 않고 한 번 더 시도 (`failureThreshold` 까지) | 부팅이 오래 걸리는 앱. 통과 전엔 liveness/readiness가 멈춰 있어서 "느린 부팅" 때문에 죽이지 않음 |
| `readinessProbe` | Service 엔드포인트에서 제외 (트래픽 차단) | 일시적으로 처리 불가 (예: 캐시 워밍업, 디스크 가득 참) |
| `livenessProbe` | 컨테이너 재시작 | 데드락처럼 **외부 자극으로는 회복 안 되는** 상태 |

> 흔한 실수: liveness를 너무 공격적으로 잡으면 부팅 중인 앱을 계속 죽인다. **liveness보다 readiness가 더 자주 필요하다**. liveness는 보수적으로.

---

## 리소스 요청과 제한 (`requests` / `limits`)

지금까지 yaml 에 적힌 `resources` 의 의미:

```yaml
resources:
  requests:    # 스케줄러가 보장해줘야 하는 최소량
    cpu: "50m"
    memory: "64Mi"
  limits:      # 이 이상 쓰면 안 됨 (CPU는 throttle, Memory는 OOMKill)
    cpu: "200m"
    memory: "256Mi"
```

- `cpu: 50m` → 1 vCPU 의 5%
- `memory: 64Mi` → 64 메비바이트 (Mi vs M 주의)
- **requests** 합계가 노드 capacity를 넘으면 새 Pod이 스케줄되지 않는다.
- **limits** 를 넘기면: CPU는 throttling, Memory는 즉시 `OOMKilled` 후 재시작.

---

## 정리 질문

1. Pod 안에 컨테이너가 두 개 있을 때 둘이 어떻게 통신할 수 있는가? (힌트: IP)
2. Init Container 가 실패하면 어떻게 되는가?
3. Liveness 와 Readiness 의 가장 큰 차이는?
4. `requests` 와 `limits` 중 스케줄링에 영향을 주는 것은?
5. `kubectl describe pod` 의 *Events* 섹션에서 알 수 있는 것 3가지를 들어보자.

---

## 정리

```bash
kubectl delete ns lab-pods
```

---

## 다음

→ [Phase 2: Workloads](../02-workloads/README.md) — 직접 만든 Pod을 컨트롤러에게 맡기자.
