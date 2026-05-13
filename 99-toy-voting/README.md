# 99 · Toy Project — voting-app

> 모든 Phase 를 마친 뒤, 지금까지 배운 리소스를 **한 번에 엮어서** 작은 앱을 배포한다.

## 무엇을 만드는가

간단한 3-tier 투표 앱.

```
                Ingress (vote.localtest.me)
                          │
                          ▼
       ┌──────────────────────────────────┐
       │       frontend (nginx)           │  ← 정적 HTML/JS, API 호출
       └──────────────┬───────────────────┘
                      │ HTTP
       ┌──────────────▼───────────────────┐
       │      api (Node.js or Python)     │  ← /vote, /results 엔드포인트
       └──────────────┬───────────────────┘
                      │ TCP 5432
       ┌──────────────▼───────────────────┐
       │      db (PostgreSQL)             │  ← StatefulSet + PVC
       └──────────────────────────────────┘
```

## 어떤 개념이 어디에 쓰이는가

| 리소스 | 사용처 |
|--------|---------|
| **Namespace** | `voting` 으로 격리 (Phase 1, 7) |
| **Deployment** | frontend, api (Phase 2) |
| **StatefulSet** | db (Phase 2, 5) |
| **Service (ClusterIP)** | 내부 통신 (Phase 3) |
| **Ingress** | 외부 진입 (Phase 3) |
| **ConfigMap** | 투표 항목, API URL 같은 비-비밀 설정 (Phase 4) |
| **Secret** | DB 접속 정보 (Phase 4) |
| **PVC + StorageClass** | DB 영속 저장소 (Phase 5) |
| **HPA** | API 자동 스케일 (Phase 6) |
| **ServiceAccount + Role** | 최소 권한 (Phase 7) |
| **(옵션) Helm Chart** | 위 전체를 차트 한 개로 패키징 (Phase 8) |

## 진행 순서 (예정)

1. db StatefulSet + Headless Service + PVC
2. api Deployment + Service + Secret/ConfigMap
3. frontend Deployment + Service
4. Ingress 로 묶기
5. HPA 붙이고 부하 테스트
6. (옵션) Helm Chart 로 묶기

> ⏳ Phase 7 까지 끝난 뒤 시작.
