# Kubernetes 학습 로드맵과 최종 실습 목표

## 학습 기준

공식문서 기준으로 아래 축을 따라갑니다.

- Kubernetes Basics Tutorial
- Concepts Overview
- Workloads
- Services, Load Balancing, and Networking
- Storage
- Configuration
- Security/RBAC
- Monitoring/운영 점검

## 최종적으로 만들어볼 것

단순히 Pod 하나 띄우는 수준이 아니라, 아래 정도를 로컬 kind 환경에서 구성하는 것이 목표입니다.

```text
사용자 요청
↓
Ingress
↓
Service
↓
Deployment
├─ Pod 1
├─ Pod 2
└─ Pod 3
↓
ConfigMap / Secret
↓
PVC 또는 임시 Volume
↓
장애 재현/롤백/로그 확인
```

## 최종 산출물 후보

### 1. 기본 웹 애플리케이션 배포 구조

- Deployment replicas 3
- Service ClusterIP
- Ingress HTTP 라우팅
- ConfigMap으로 nginx index 또는 설정 주입
- readiness/liveness probe 적용
- requests/limits 적용

### 2. 운영 관점 장애 대응 실습 세트

- ImagePullBackOff
- CrashLoopBackOff
- Pending
- Service selector 불일치
- readiness probe 실패
- PVC Pending

각 장애마다 아래 형식으로 기록합니다.

```text
증상 → 확인 명령 → 원인 → 조치 → 재발 방지
```

### 3. GitHub/블로그 산출물

- GitHub: 재현 가능한 YAML과 명령
- Tistory: 실습 흐름 정리
- LinkedIn: 운영 관점에서 얻은 인사이트와 장애 분석 회고

## 추천 학습 단계

### Phase 1. 로컬 실습 환경과 기본 객체

목표: Kubernetes가 어떤 단위로 동작하는지 이해

- kind 클러스터
- Node
- Namespace
- Pod
- kubectl get/describe/logs/events

완료 기준:

- kind 클러스터를 만들고 삭제할 수 있음
- Pod가 어느 Node에 배치됐는지 설명 가능
- describe/logs/events를 구분해서 사용 가능

### Phase 2. Workloads

목표: 운영 배포 기본 단위 이해

- Deployment
- ReplicaSet
- RollingUpdate
- Rollback
- DaemonSet/StatefulSet 개념 맛보기

완료 기준:

- Pod를 직접 만들기보다 Deployment를 쓰는 이유 설명 가능
- replicas를 늘리고 줄일 수 있음
- 잘못된 이미지 배포 후 롤백 가능

### Phase 3. Networking

목표: Pod에 안정적으로 접근하는 방식 이해

- Service ClusterIP
- NodePort
- EndpointSlice
- DNS
- Ingress
- Ingress Controller

완료 기준:

- Pod IP가 바뀌어도 Service로 접근하는 이유 설명 가능
- Service selector 문제를 진단 가능
- Ingress → Service → Pod 흐름 설명 가능

### Phase 4. Configuration & Storage

목표: 설정과 데이터를 컨테이너 이미지에서 분리

- ConfigMap
- Secret
- Volume
- PVC
- StorageClass

완료 기준:

- 환경변수/파일 마운트 방식 설명 가능
- Secret과 ConfigMap 차이 설명 가능
- PVC Pending 원인 후보 설명 가능

### Phase 5. 운영 안정성

목표: 운영에서 자주 보는 설정 이해

- requests/limits
- liveness/readiness/startup probe
- HPA 개념
- rollout 전략
- 이벤트/로그 기반 장애 분석

완료 기준:

- Probe 실패와 트래픽 제외 관계 설명 가능
- 리소스 부족으로 Pending 되는 흐름 설명 가능
- 장애 대응 템플릿으로 원인 후보 정리 가능

### Phase 6. Helm과 운영 패키지

목표: 실제 운영 도구 설치 방식 이해

- Helm Chart
- values.yaml
- ingress-nginx 설치
- metrics-server 설치
- kube-prometheus-stack 개념

완료 기준:

- Helm install/upgrade/rollback 기본 사용 가능
- values.yaml로 설정 변경 가능

### Phase 7. 포트폴리오형 미니 프로젝트

목표: “Kubernetes 공부했다”고 말할 수 있는 결과물 만들기

미니 프로젝트 예시:

```text
nginx 또는 간단한 웹앱
├─ Deployment replicas 3
├─ Service
├─ Ingress
├─ ConfigMap 기반 페이지/설정
├─ readiness/liveness probe
├─ requests/limits
├─ 장애 재현 문서 3개 이상
└─ Helm chart 또는 Kustomize 구조
```

완료 기준:

- GitHub README만 봐도 전체 구조와 실행 방법이 보임
- 장애 재현/복구 문서가 있음
- Tistory 글 3개 이상으로 정리 가능
- LinkedIn에는 운영 관점 회고 1개 게시 가능

## 블로그/LinkedIn 게시 기준

### Tistory에 좋은 주제

- 따라 할 수 있는 실습
- 명령 결과가 있는 글
- 그림으로 구조를 설명하는 글
- 실수/오류와 해결 과정이 있는 글

### LinkedIn에 좋은 주제

- 단순 개념 설명보다 운영 관점
- 장애 재현과 분석
- 배포 실패와 롤백
- Service/Ingress 트래픽 흐름
- “기초 개념을 운영 관점에서 다시 정리했다”는 회고

## 현재 다음 추천 실습

`02. Deployment / ReplicaSet / Pod 실습`

이유:

- Pod 다음 공식 흐름으로 자연스럽습니다.
- 운영 배포의 기본 단위입니다.
- replicas, self-healing, rollout, rollback까지 실습할 수 있습니다.
- 블로그/LinkedIn으로 확장하기 좋은 첫 운영 관점 주제입니다.
