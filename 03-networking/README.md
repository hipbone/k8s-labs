# Phase 3 · Networking

> Pod IP 는 잠깐 살다 죽는다. 안정적인 주소(=Service)와 외부 진입(=Ingress)이 필요한 이유.

## 이 단계에서 배울 것

- **ClusterIP**: 클러스터 내부 전용. 기본형.
- **NodePort**: 모든 노드의 같은 포트로 노출. 개발/실습에 편함.
- **LoadBalancer**: 클라우드/`cloud-provider-kind` 가 외부 IP를 붙여줌.
- **Headless Service**: ClusterIP 없이 DNS 만. StatefulSet 과 짝.
- **Ingress + Ingress Controller**: HTTP(S) 진입점. 한 IP로 호스트/경로별 라우팅.
- **DNS**: `<svc>.<ns>.svc.cluster.local` 규칙.

## 핵심 질문

- Service 의 `selector` 와 Pod의 `labels` 는 어떻게 매칭되는가?
- Endpoints 리소스는 왜 자동으로 생기는가?
- kind 에서 LoadBalancer 를 쓰려면 무엇이 필요한가?
- Ingress 와 Ingress Controller 는 무엇이 다른가?

## 준비물

- Phase 0의 `kind-cluster.yaml` 이 `extraPortMappings` 로 80/443/30080 을 열고 있어야 한다.
- Ingress 실습에서는 `ingress-nginx` 설치가 필요하다.

> ⏳ 학습 시작 시 README 와 yaml 들을 채워나간다.
