# CLAUDE.md

> 이 저장소에서 작업하는 AI 어시스턴트(Claude Code 등)를 위한 컨텍스트.
> 사람이 읽는 안내는 [`README.md`](./README.md) 를 참고하세요.

---

## 프로젝트 정체성

- **목적**: Kubernetes 를 단계별로 학습하기 위한 lab 저장소. 학습 후 **Crossplane** 학습으로 이어진다.
- **사용자 상태**: Kubernetes 입문~기초. 책 없이 이 저장소의 README 와 yaml 로 학습한다.
- **환경**: Windows + WSL2(Ubuntu), Docker, kind, kubectl, helm.
- **언어**: 모든 설명/주석/문서는 **한국어** 기본.

---

## 저장소 구조 (절대 규칙)

```
00-setup/           # kind cluster 정의, 도구 설치 가이드
01-pods/  …  08-extensions/   # Phase 별 학습 모듈
99-toy-voting/      # 캡스톤 토이 프로젝트
```

- Phase 폴더는 **숫자 prefix 로 순서가 명시**되어 있다. 임의로 순서를 바꾸지 말 것.
- 각 Phase 폴더는 항상 `README.md` 와 번호 매겨진 yaml 들 (`01-...yaml`, `02-...yaml`, ...) 로 구성.
- `README.md` 가 "책" 역할이다. 개념 설명, 실습 명령, 일부러 깨보기, 정리 질문까지 모두 포함되어야 한다.
- 새 Phase 가 필요해지면 기존 번호 사이의 빈 자리(`01a-`, `015-` 등)나 끝(`09-`)을 쓴다. **재번호 부여 금지**.

---

## 작성 원칙

### yaml 스타일
- 모든 리소스에 `resources.requests/limits` 를 학습용 작은 값으로 명시 (cpu `50m~200m`, memory `64Mi~256Mi`).
- 이미지 태그는 **반드시 고정** (`nginx:1.25-alpine`). `:latest` 금지.
- 네임스페이스는 Phase 별로 분리 (`lab-pods`, `lab-workloads`, …). 첫 yaml 에서 같은 네임스페이스를 생성.
- 한 파일에 여러 리소스 → `---` 로 구분.
- 주석은 **왜 이 설정이 필요한지**만 한국어로 짧게. *무엇을* 설명하는 주석은 쓰지 않는다.

### README 스타일 (Phase 별)
각 Phase 의 README 는 가능한 한 다음 섹션을 포함:

1. **이번 단계 목표** (1~2줄)
2. **핵심 개념** — *왜 필요한가* 부터. 비유와 흔한 오해를 짚는다.
3. **실습** — `kubectl apply` → 확인 명령 → "일부러 깨보기" 의 흐름.
4. **정리 질문** — 자가 점검용 3~5개.
5. **다음** — 다음 Phase 로 가는 링크.

> 톤: 입문자에게 친근하지만 정확. "쉬워요!" 같은 빈말 대신 한 줄짜리 인사이트를 준다.

### 학습 흐름을 깨지 않는다
- 사용자가 명시적으로 요청하지 않으면 **Phase 순서를 건너뛰지 않는다**. 예: "Operator 부터 보고 싶다" → 선행 개념을 짧게 짚고 진행 여부를 묻는다.
- 토이 프로젝트(`99-toy-voting/`) 는 Phase 1~7 이 끝난 뒤에만 실제 yaml 을 만들기 시작. 그 전까지는 README 의 윤곽만 유지.

### 만들지 말 것
- 사용자가 요청하지 않은 Makefile, install.sh, CI 워크플로, 거대한 helper 스크립트.
- "혹시 모르니" 식으로 미리 만드는 placeholder yaml.
- 개념 설명 없이 yaml 만 늘어놓은 폴더.

---

## WSL / kind 특화 주의

- `hostPath` 는 WSL 내 경로(`/mnt/data` 등) 사용. Windows 마운트(`/mnt/c/...`) 금지.
- kind 노드는 컨테이너이므로 `hostPath` 의 실체는 노드 컨테이너 내부. 진짜 호스트에 노출하려면 cluster 정의에 `extraMounts` 필요 — 트레이드오프 설명 후 사용자 결정.
- `LoadBalancer` 는 kind 에서 기본 동작하지 않음. Phase 3 에서 `cloud-provider-kind` 또는 MetalLB 로 해결.
- Ingress 실습은 `00-setup/kind-cluster.yaml` 의 `extraPortMappings (80, 443)` 와 `ingress-ready=true` 라벨에 의존한다. 이 cluster 로 만들어진 환경을 전제.

---

## 현재 진행 상태

| Phase | 폴더 | README | yaml | 상태 |
|:----:|---|:---:|:---:|---|
| 0 | `00-setup/` | ✅ | ✅ kind-cluster.yaml | 완료 |
| 1 | `01-pods/` | ✅ | ✅ 01~04 | 완료 |
| 2 | `02-workloads/` | placeholder | — | 대기 |
| 3 | `03-networking/` | placeholder | — | 대기 |
| 4 | `04-config/` | placeholder | — | 대기 |
| 5 | `05-storage/` | placeholder | — | 대기 |
| 6 | `06-scheduling/` | placeholder | — | 대기 |
| 7 | `07-security/` | placeholder | — | 대기 |
| 8 | `08-extensions/` | placeholder | — | 대기 |
| 99 | `99-toy-voting/` | placeholder | — | 대기 |

> 이 표는 손으로 관리. 새 실습이 추가될 때마다 함께 갱신할 것. 루트 `README.md` 의 로드맵 표도 같은 의미.

---

## 응대 톤

- 친절하지만 **간결하게**. 한 줄로 끝낼 수 있으면 한 줄로.
- yaml/명령을 만들었으면 **확인 명령**(`kubectl get …` 등)을 함께 안내.
- 단계를 건너뛰는 질문이 오면 선행 개념을 짚고 진행 여부를 묻는다.
- 사용자가 막혔다고 하면 즉시 답을 주지 말고, *지금 본 출력* 을 묻고 디버깅 흐름을 같이 따라간다.
