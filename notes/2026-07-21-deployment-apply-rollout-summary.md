# Deployment apply와 rollout 정리

## 핵심 요약

Deployment는 같은 이름으로 `apply`할 때마다 무조건 새 버전을 만드는 것이 아닙니다.

```text
같은 Deployment 이름 + 변경 없음
→ unchanged
→ 새 ReplicaSet/Revision 없음

같은 Deployment 이름 + Pod template 변경
→ rollout 발생
→ 새 ReplicaSet/Revision 생성

다른 Deployment 이름
→ 완전히 별도 Deployment 생성
```

## apply는 무엇을 하나

`kubectl apply`는 YAML에 적힌 원하는 상태를 Kubernetes에 반영합니다.

```bash
kubectl apply -f manifests/deployment/01-nginx-deployment.yaml
```

의미:

```text
nginx-deploy Deployment가 이 YAML 상태가 되도록 맞춰줘
```

이미 같은 이름의 Deployment가 있으면 새로 만드는 것이 아니라 기존 Deployment를 업데이트합니다.

## 언제 새 ReplicaSet/Revision이 생기나

Deployment의 `spec.template`이 바뀌면 새 rollout이 발생합니다.

예:

```yaml
spec:
  template:
    spec:
      containers:
        - image: nginx:1.28
```

`spec.template`은 실제 Pod 설계도입니다. 이 설계도가 바뀌면 Kubernetes는 기존 Pod를 새 Pod로 교체해야 하므로 새 ReplicaSet을 만듭니다.

## 새 ReplicaSet을 만드는 변경 예시

- container image 변경
- container command/args 변경
- env 변경
- port 변경
- resource requests/limits 변경
- liveness/readiness probe 변경
- template metadata labels/annotations 변경

즉, Pod 자체의 모양이 바뀌면 rollout 대상입니다.

## 새 ReplicaSet을 만들지 않는 변경 예시

- replicas 개수 변경
- Deployment metadata annotation 변경
- Deployment metadata label 변경

예를 들어 `replicas: 3`에서 `replicas: 5`로 바꾸면 Pod 개수만 늘어납니다. 새 버전 배포라기보다 scale 조정입니다.

## 이름이 달라지면 어떻게 되나

기존:

```yaml
metadata:
  name: nginx-deploy
```

새 YAML:

```yaml
metadata:
  name: nginx-deploy-v2
```

이렇게 이름이 달라지면 기존 Deployment를 업데이트하는 것이 아니라 완전히 다른 Deployment가 새로 생성됩니다.

```text
nginx-deploy     기존 Deployment
nginx-deploy-v2  새 Deployment
```

## selector는 주의

Deployment의 `spec.selector`는 한 번 생성하면 보통 변경할 수 없습니다.

```yaml
selector:
  matchLabels:
    app: nginx-deploy
```

이 값은 어떤 Pod를 관리할지 정하는 핵심 조건이라서, 운영 중 변경하면 충돌/오류가 날 수 있습니다.

## rollout 명령 역할

```bash
kubectl rollout status deploy/nginx-deploy
kubectl rollout history deploy/nginx-deploy
kubectl rollout undo deploy/nginx-deploy
```

- `status`: 현재 배포가 완료됐는지 확인
- `history`: revision 이력 확인
- `undo`: 이전 revision으로 Deployment template 되돌림

## 한 줄 정리

```text
Deployment apply는 같은 이름의 Deployment를 원하는 상태로 맞추는 명령이고,
Pod template이 바뀔 때만 rollout/새 ReplicaSet/새 revision이 생깁니다.
```
