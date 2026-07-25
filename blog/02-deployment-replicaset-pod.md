# 02. Deployment/ReplicaSet/Pod 관계를 실습으로 이해하기

> 상태: 초안
> 게시 후보: Tistory
> LinkedIn: 배포 실패/롤백 실습까지 완료 후 운영 관점 글로 재가공 추천

## 글의 목적

Pod를 직접 생성하는 방식에서 Deployment로 넘어가며, Kubernetes가 애플리케이션을 어떻게 원하는 개수로 유지하는지 실습으로 이해한다.

## 핵심 메시지

- 운영에서는 Pod를 직접 관리하기보다 Deployment를 사용한다.
- Deployment는 ReplicaSet을 만들고, ReplicaSet은 Pod 개수를 맞춘다.
- Pod가 삭제되어도 replicas 수만큼 자동 복구된다.
- 이미지 업데이트는 rollout으로 관리되고, 실패 시 rollback할 수 있다.

## 구조 그림

```text
Deployment: nginx-deploy
└─ ReplicaSet
   ├─ Pod
   ├─ Pod
   └─ Pod
```

업데이트 시:

```text
Deployment
├─ old ReplicaSet: nginx:1.27
└─ new ReplicaSet: nginx:1.28
```

## 실습 흐름

1. Deployment 생성
2. ReplicaSet/Pod 확인
3. Pod 하나 삭제 후 자동 복구 확인
4. 이미지 업데이트
5. 잘못된 이미지로 배포 실패 재현
6. rollout undo로 롤백

## 사용할 명령

```bash
kubectl apply -f manifests/deployment/01-nginx-deployment.yaml
kubectl get deploy,rs,pod -o wide
```

```bash
kubectl delete pod <Pod이름>
kubectl get pods -w
```

```bash
kubectl apply -f manifests/deployment/02-nginx-deployment-update.yaml
kubectl rollout status deploy/nginx-deploy
kubectl rollout history deploy/nginx-deploy
```

```bash
kubectl apply -f manifests/deployment/03-nginx-bad-image.yaml
kubectl get pods -o wide
kubectl describe pod <문제 Pod이름>
```

```bash
kubectl rollout undo deploy/nginx-deploy
```

## 운영 관점 포인트

- replicas는 단순 개수 설정이 아니라 원하는 상태 선언입니다.
- Pod가 사라져도 ReplicaSet이 다시 생성합니다.
- 잘못된 이미지 배포는 기존 Pod가 일부 살아있는 상태로 멈출 수 있습니다.
- rollout history와 undo는 배포 장애 대응의 기본입니다.

## 실습 후 채울 결과

- 생성된 ReplicaSet 이름:
- 생성된 Pod 개수:
- 삭제한 Pod 이름:
- 자동 복구된 Pod 이름:
- 실패 이미지 배포 시 상태:
- 롤백 후 상태:
