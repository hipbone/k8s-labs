# Phase 8 · Extensions (→ Crossplane 준비)

> Kubernetes 는 패키지 매니저(Helm)와 **자체 확장 메커니즘(CRD + Controller)** 을 가진 플랫폼이다.
> 이 단계가 Crossplane 학습으로 가는 다리.

## 이 단계에서 배울 것

- **Helm**: 차트(Chart), 릴리스(Release), values 분리. `helm install` 의 실제 동작.
- 공개 차트 사용 예: `bitnami/postgresql`, `ingress-nginx`.
- **CRD (CustomResourceDefinition)**: 내 도메인 객체를 k8s 리소스로 등록.
- **Operator / Controller 패턴**: "원하는 상태" 를 보고 실제 상태를 그쪽으로 끌고 가는 컨트롤 루프.
- (실습 후) Crossplane 이 이 패턴 위에 어떻게 *클라우드 리소스 프로비저닝* 을 얹는지 그림 그리기.

## 핵심 질문

- Helm 의 `template` 은 **클러스터 안에서** 동작하는가? (아니다)
- `kubectl apply` vs `helm install` 가 만들어내는 결과의 차이는?
- CRD 만 등록하고 Controller 없이 리소스를 만들면 어떻게 되는가? (예측해보자)
- Crossplane 이 "Kubernetes API 로 클라우드를 관리한다" 는 말의 의미는?

> ⏳ 학습 시작 시 README 와 yaml 들을 채워나간다.
