# kubernetes-study-lab

Kubernetes 기본 개념을 공식문서 기준으로 실습하며 정리하는 개인 학습 저장소입니다.

## 학습 목적

- KT ICIS 유지보수 프로젝트 투입 전 Kubernetes 기본기를 실습으로 정리
- 단순 개념 암기보다 `kubectl` 명령, YAML, 상태 확인, 장애 재현 흐름에 익숙해지기
- 이후 운영 관점의 장애 대응, Ingress, Storage, Helm, 모니터링으로 확장

## 현재 실습 환경

- OS: Windows
- Container runtime/lab base: Docker Desktop
- Local Kubernetes: kind
- CLI: kubectl
- Editor: VSCode

## 구조

```text
.
├─ clusters/          # kind 클러스터 설정
├─ manifests/         # Kubernetes YAML 실습 파일
├─ troubleshooting/   # 장애 재현/분석 기록
├─ diagrams/          # 구조도/흐름도
├─ blog/              # 기술 블로그 초안
├─ notes/             # 실습 메모
└─ README.md
```

## 실습 진행 현황

- [x] Docker Desktop 설치/검증
- [x] kubectl/kind 설치/검증
- [x] kind 클러스터 `k8s-study` 생성
- [x] nginx Pod `nginx-basic` 배포
- [x] `kubectl port-forward`로 HTTP 200 OK 확인
- [ ] Deployment/ReplicaSet 실습
- [ ] Service 실습
- [ ] 장애 상태 재현: ImagePullBackOff, CrashLoopBackOff

## 빠른 시작

### 1. 로컬 클러스터 생성

```bash
kind create cluster --config clusters/kind-k8s-study.yaml
kubectl get nodes -o wide
```

### 2. 첫 Pod 배포

```bash
kubectl apply -f manifests/01-nginx-pod.yaml
kubectl get pods -o wide
kubectl describe pod nginx-basic
```

### 3. 실습 클러스터 삭제

```bash
kind delete cluster --name k8s-study
```

## 학습 기록 원칙

- 한 주제는 `개념 → 그림 → YAML → 확인 명령 → 장애 포인트` 순서로 정리
- 기초 개념은 짧게, 운영 관점에서 중요한 확인 포인트를 남김
- 블로그 글은 실습 결과가 쌓인 뒤 `blog/`에 초안으로 작성

## 관련 문서

Obsidian 개념 정리 위치:

```text
C:\Users\윤영학\Desktop\업무\옵시디언\k8s
```
