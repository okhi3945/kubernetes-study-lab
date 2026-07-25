# 02. Deployment / ReplicaSet / Pod 실습

## 목표

이번 실습에서는 Pod를 직접 1개 만드는 방식에서 벗어나, 운영에서 일반적으로 사용하는 Deployment를 배웁니다.

## 이번에 이해할 그림

```text
Deployment: nginx-deploy
└─ ReplicaSet
   ├─ Pod: nginx-deploy-xxxxx
   ├─ Pod: nginx-deploy-yyyyy
   └─ Pod: nginx-deploy-zzzzz
```

## 핵심 개념

- Pod를 직접 만들면 죽었을 때 자동 복구가 약합니다.
- Deployment는 원하는 Pod 개수와 버전을 선언합니다.
- ReplicaSet은 실제 Pod 개수를 맞춥니다.
- Deployment를 업데이트하면 새 ReplicaSet이 만들어지고 Pod가 교체됩니다.
- 문제가 있으면 rollout undo로 이전 버전으로 되돌릴 수 있습니다.

## 0. 이전 Pod 정리

지난 실습에서 만든 단일 Pod가 있으면 삭제합니다.

```bash
kubectl delete pod nginx-basic
```

없다고 나와도 괜찮습니다.

## 1. Deployment 생성

```bash
kubectl apply -f manifests/deployment/01-nginx-deployment.yaml
```

확인:

```bash
kubectl get deploy,rs,pod -o wide
```

봐야 할 것:

- Deployment 1개
- ReplicaSet 1개
- Pod 3개
- Pod들이 worker node에 배치되는지

## 2. Deployment 상세 확인

```bash
kubectl describe deploy nginx-deploy
```

봐야 할 것:

- Replicas: 3 desired / 3 available
- Selector: app=nginx-deploy
- Pod Template
- Events

## 3. Pod 하나 삭제해보기

```bash
kubectl get pods
kubectl delete pod <Pod이름>
kubectl get pods -w
```

이때 느껴야 할 점:

```text
Pod 하나를 삭제해도 ReplicaSet이 다시 3개를 맞춘다.
```

즉, 운영에서는 Pod를 직접 지워도 Deployment/ReplicaSet이 원하는 상태를 복구합니다.

## 4. 이미지 업데이트

```bash
kubectl apply -f manifests/deployment/02-nginx-deployment-update.yaml
kubectl rollout status deploy/nginx-deploy
```

확인:

```bash
kubectl get rs,pod -o wide
kubectl rollout history deploy/nginx-deploy
```

봐야 할 것:

- 새 ReplicaSet 생성
- 기존 Pod가 새 Pod로 교체
- rollout history에 변경 이력 생성

## 5. 일부러 잘못된 이미지 배포

```bash
kubectl apply -f manifests/deployment/03-nginx-bad-image.yaml
kubectl rollout status deploy/nginx-deploy
```

상태 확인:

```bash
kubectl get pods -o wide
kubectl describe pod <문제 Pod이름>
kubectl get events --sort-by=.lastTimestamp
```

예상 상태:

```text
ErrImagePull
ImagePullBackOff
```

## 6. 롤백

```bash
kubectl rollout undo deploy/nginx-deploy
kubectl rollout status deploy/nginx-deploy
kubectl get pods -o wide
```

여기서 이해할 것:

```text
Deployment는 배포 실패 시 이전 ReplicaSet 기준으로 롤백할 수 있다.
```

## 7. 정리

```bash
kubectl delete deploy nginx-deploy
```

## 이번 실습 후 말할 수 있어야 하는 것

- Deployment는 Pod를 직접 관리하기보다 상위에서 원하는 상태를 관리하는 리소스다.
- ReplicaSet은 Deployment 아래에서 Pod 개수를 맞춘다.
- Pod가 죽어도 replicas 수만큼 다시 생성된다.
- 이미지 변경은 rollout으로 처리된다.
- 잘못된 이미지 배포는 ImagePullBackOff로 관찰할 수 있고 rollback 가능하다.

## 블로그 후보

- Tistory: 좋음. `Deployment/ReplicaSet/Pod 관계를 실습으로 이해하기`
- LinkedIn: 단독 기초 글보다는, 나중에 `배포 실패와 롤백 실습`까지 묶으면 좋음.
