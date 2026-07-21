# 01. kind로 로컬 Kubernetes 실습 환경 구성하기

> 상태: 초안
> 게시 후보: Tistory
> LinkedIn: 아직 게시하지 않음. 추후 운영 관점 회고 글로 요약 가능.

## 글의 목적

Kubernetes를 운영 관점에서 학습하기 전에, 로컬 PC에서 안전하게 실습할 수 있는 환경을 구성한다.

## 핵심 메시지

- 운영 클러스터를 직접 건드리지 않고 로컬에서 Kubernetes 기본 개념을 실습한다.
- Docker Desktop 위에 kind 클러스터를 구성하면 Pod/Deployment/Service 기본 실습이 가능하다.
- 실습 환경과 개념 정리 공간을 분리하면 나중에 다시 보기 쉽다.

## 실습 환경

- Windows
- Docker Desktop
- kubectl
- kind
- VSCode

## 구조 그림

```text
Windows
└─ Docker Desktop
   └─ kind cluster: k8s-study
      ├─ control-plane node
      └─ worker node
```

## 사용 명령

```bash
kind create cluster --config clusters/kind-k8s-study.yaml
kubectl get nodes -o wide
```

## 정리할 내용

- kind는 Kubernetes IN Docker의 약자
- 로컬 학습/테스트용 Kubernetes 클러스터를 빠르게 만들고 지울 수 있음
- 운영 환경과 완전히 같지는 않지만 기본 리소스 학습에는 충분함

## 다음 글 후보

- Pod를 직접 배포하며 이해하는 Kubernetes 최소 실행 단위
- Deployment/ReplicaSet/Pod 관계 실습
- Service가 필요한 이유: Pod IP 변경과 안정적인 접근
