# 2026-07-21 첫 kind 클러스터와 nginx Pod 실습

## 목표

- kind로 로컬 Kubernetes 클러스터 생성
- control-plane/worker Node 상태 확인
- nginx Pod 배포
- Pod 상태, 상세 정보, 로그 확인
- port-forward로 실제 HTTP 응답 확인

## 실습 구조

```text
Windows
└─ Docker Desktop
   └─ kind cluster: k8s-study
      ├─ k8s-study-control-plane
      └─ k8s-study-worker
         └─ Pod: nginx-basic
            └─ container: nginx:1.27
```

## 실행 명령

```bash
kind create cluster --config clusters/kind-k8s-study.yaml
kubectl get nodes -o wide
```

```bash
kubectl apply -f manifests/01-nginx-pod.yaml
kubectl get pod nginx-basic -o wide
kubectl describe pod nginx-basic
kubectl logs nginx-basic
```

```bash
kubectl port-forward pod/nginx-basic 8080:80
curl -I http://127.0.0.1:8080
```

## 확인 결과

- 클러스터 생성 성공
- Node 2개 Ready 확인
  - `k8s-study-control-plane`
  - `k8s-study-worker`
- `nginx-basic` Pod Running 확인
- Pod는 worker node에 스케줄링됨
- Pod IP: `10.244.1.2`
- 컨테이너 이미지: `nginx:1.27`
- HTTP 응답 확인: `HTTP/1.1 200 OK`

## 배운 점

- kind는 Docker 컨테이너를 Kubernetes Node처럼 사용합니다.
- Pod는 Kubernetes에서 실행되는 최소 배포 단위입니다.
- Pod가 생성되면 scheduler가 적절한 Node에 배치합니다.
- `kubectl describe pod`는 Pod 배치 위치, 상태, 이벤트 확인에 유용합니다.
- `kubectl logs`는 컨테이너 표준출력 로그를 확인합니다.
- `kubectl port-forward`는 Service 없이도 로컬에서 Pod 포트에 임시 접근할 수 있게 해줍니다.

## 블로그 후보성

- Tistory에 적기 좋음: 로컬 실습 환경 구성 + 첫 Pod 배포까지 자연스러운 입문 글 가능
- LinkedIn은 아직 보류: 기초 실습 단계라 운영 관점/트러블슈팅이 더 쌓인 뒤 요약하는 것이 좋음
