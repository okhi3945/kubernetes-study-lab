# 01. kind로 로컬 Kubernetes 실습 환경 구성하기

> 상태: 초안 v2
> 게시 후보: Tistory
> LinkedIn: 아직 게시하지 않음. 추후 운영 관점/장애 분석 글로 묶어서 게시 추천.

## 글의 목적

Kubernetes를 운영 관점에서 학습하기 전에, 로컬 PC에서 안전하게 실습할 수 있는 환경을 구성한다. 운영 클러스터를 직접 건드리지 않고도 Pod, Node, kubectl 기본 흐름을 확인하는 것이 목표다.

## 왜 Docker Desktop + kind인가

- 로컬 PC에서 Kubernetes 클러스터를 빠르게 만들 수 있다.
- 실습 중 환경이 꼬이면 클러스터를 삭제하고 다시 만들 수 있다.
- 실제 운영 클러스터와 분리되어 안전하다.
- Pod, Deployment, Service 같은 기본 리소스 학습에 충분하다.

## 실습 환경

- Windows
- Docker Desktop
- kubectl v1.36.2
- kind v0.32.0
- VSCode

## 구조 그림

```text
Windows
└─ Docker Desktop
   └─ kind cluster: k8s-study
      ├─ k8s-study-control-plane
      └─ k8s-study-worker
         └─ Pod: nginx-basic
            └─ container: nginx:1.27
```

## kind 클러스터 생성

```bash
kind create cluster --config clusters/kind-k8s-study.yaml
kubectl get nodes -o wide
```

확인 결과 Node 2개가 생성되었다.

```text
k8s-study-control-plane   Ready
k8s-study-worker          Ready
```

클러스터 생성 직후에는 잠깐 `NotReady`로 보일 수 있었다. 이는 CNI 설치, kubelet 상태 반영, worker join이 끝나기 전의 초기화 과정으로 보인다. 잠시 후 다시 확인하니 `Ready`로 바뀌었다.

## 첫 Pod 배포

```bash
kubectl apply -f manifests/01-nginx-pod.yaml
kubectl get pod nginx-basic -o wide
```

확인 결과:

```text
nginx-basic   1/1   Running   0   10.244.1.2   k8s-study-worker
```

여기서 볼 수 있는 점:

- Pod는 worker node에 배치되었다.
- Pod IP는 `10.244.1.2`로 할당되었다.
- 컨테이너는 `nginx:1.27` 이미지로 실행되었다.

## 상세 확인

```bash
kubectl describe pod nginx-basic
kubectl logs nginx-basic
```

`describe`에서는 Pod가 어느 Node에 배치되었는지, 컨테이너가 Ready인지, 이벤트가 있는지 확인할 수 있다.  
`logs`에서는 nginx 컨테이너의 시작 로그를 확인할 수 있었다.

## 로컬에서 HTTP 응답 확인

Service를 만들기 전이므로 `port-forward`로 임시 접근했다.

```bash
kubectl port-forward pod/nginx-basic 8080:80
curl -I http://127.0.0.1:8080
```

결과:

```text
HTTP/1.1 200 OK
Server: nginx/1.27.5
```

## 이번 실습에서 정리한 개념

- kind는 Docker 컨테이너를 Kubernetes Node처럼 사용한다.
- Pod는 Kubernetes에서 컨테이너를 실행하는 최소 배포 단위다.
- scheduler는 Pod를 실행할 Node를 결정한다.
- `kubectl get`은 상태 요약 확인에 좋다.
- `kubectl describe`는 상세 상태와 이벤트 확인에 좋다.
- `kubectl logs`는 컨테이너 로그 확인에 사용한다.
- `kubectl port-forward`는 Service 없이도 로컬에서 Pod에 임시 접근할 수 있게 해준다.

## 다음 실습 후보

- Pod를 직접 배포하며 이해하는 Kubernetes 최소 실행 단위
- Deployment/ReplicaSet/Pod 관계 실습
- Service가 필요한 이유: Pod IP 변경과 안정적인 접근
- ImagePullBackOff / CrashLoopBackOff 직접 재현하기
