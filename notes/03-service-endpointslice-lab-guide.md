# 03. Service / EndpointSlice 실습

## 목표

Deployment로 생성된 여러 Pod 앞에 Service를 붙여서, Pod IP가 바뀌어도 안정적으로 접근하는 방식을 이해합니다.

## 이번에 이해할 그림

```text
Client
  ↓
Service: nginx-svc
  ↓ selector: app=nginx-deploy
EndpointSlice
  ↓
Pod 1: app=nginx-deploy
Pod 2: app=nginx-deploy
Pod 3: app=nginx-deploy
```

## 핵심 개념

- Pod IP는 Pod가 재생성되면 바뀔 수 있습니다.
- Service는 변하지 않는 고정 접근 지점입니다.
- Service는 selector로 대상 Pod를 찾습니다.
- EndpointSlice는 Service가 실제로 연결할 Pod IP 목록입니다.
- EndpointSlice는 Service 생성 시 Kubernetes가 자동으로 만들고, Service/Pod 상태를 보고 자동 갱신합니다.
- ClusterIP는 클러스터 내부 통신용 IP이므로 외부에서 직접 접근하지 못합니다.
- Service IP/DNS로 접근하면 EndpointSlice에 있는 Pod IP들로 연결되고, 최종적으로 실제 Pod에 도달합니다.
- Pod를 삭제하고 새 Pod가 생성되면 새 Pod IP가 발급되고 EndpointSlice도 자동 갱신됩니다.
- 이때 Service 이름과 Service IP는 그대로 유지됩니다.
- selector와 Pod label이 맞지 않으면 Service는 생성돼도 트래픽을 보낼 대상이 없습니다.

## 0. Deployment 상태 확인

지난 실습의 Deployment가 살아있는지 확인합니다.

```bash
kubectl get deploy,rs,pod -o wide
```

없으면 다시 생성합니다.

```bash
kubectl apply -f manifests/deployment/01-nginx-deployment.yaml
kubectl get pods --show-labels
```

## 1. Service YAML 보기

파일:

```text
manifests/service/01-nginx-clusterip-service.yaml
```

핵심:

```yaml
kind: Service
metadata:
  name: nginx-svc
spec:
  type: ClusterIP
  selector:
    app: nginx-deploy
  ports:
    - port: 80
      targetPort: 80
```

의미:

```text
app=nginx-deploy 라벨을 가진 Pod들의 80번 포트로 트래픽을 보내는 Service를 만든다.
```

## 2. Service 생성

```bash
kubectl apply -f manifests/service/01-nginx-clusterip-service.yaml
```

확인:

```bash
kubectl get svc nginx-svc
kubectl describe svc nginx-svc
```

봐야 할 것:

- Type: ClusterIP
- IP: Service IP
- Port: 80/TCP
- Endpoints 또는 EndpointSlice 대상

## 3. EndpointSlice 확인

```bash
kubectl get endpointslice -l kubernetes.io/service-name=nginx-svc -o wide
```

또는:

```bash
kubectl describe endpointslice -l kubernetes.io/service-name=nginx-svc
```

봐야 할 것:

```text
Service가 실제 Pod IP들을 알고 있는지
```

## 4. 임시 curl Pod로 Service 접근

ClusterIP는 클러스터 내부 IP라서 Windows에서 바로 접근하지 않습니다.
클러스터 안에 임시 Pod를 띄워서 접근합니다.

```bash
kubectl run curl-test --image=curlimages/curl:8.10.1 -it --rm --restart=Never -- curl -I http://nginx-svc
```

기대 결과:

```text
HTTP/1.1 200 OK
Server: nginx
```

## 5. Pod 하나 삭제 후 Service 유지 확인

Pod 하나 이름 확인:

```bash
kubectl get pods -o wide
```

Pod 하나 삭제:

```bash
kubectl delete pod <Pod이름>
```

복구 확인:

```bash
kubectl get pods -o wide
kubectl get endpointslice -l kubernetes.io/service-name=nginx-svc -o wide
```

느껴야 할 것:

```text
Pod는 바뀌어도 Service 이름 nginx-svc는 그대로다.
```

## 6. 일부러 selector가 틀린 Service 만들기

```bash
kubectl apply -f manifests/service/02-nginx-bad-selector-service.yaml
kubectl describe svc nginx-svc-bad
kubectl get endpointslice -l kubernetes.io/service-name=nginx-svc-bad -o wide
```

예상:

```text
Service는 존재하지만 연결된 Endpoint가 없음
```

테스트:

```bash
kubectl run curl-test --image=curlimages/curl:8.10.1 -it --rm --restart=Never --max-time=5 http://nginx-svc-bad
```

실패하거나 응답이 없습니다.

## 7. 정리

```bash
kubectl delete svc nginx-svc nginx-svc-bad
```

Deployment는 다음 Ingress 실습에서 재사용할 수 있으므로 남겨둬도 됩니다.

## 이번 실습 후 말할 수 있어야 하는 것

- Service는 Pod 앞의 안정적인 접근 지점입니다.
- Service는 selector로 Pod label을 매칭합니다.
- EndpointSlice는 실제 트래픽 대상 Pod IP 목록입니다.
- Service가 있는데 접속이 안 되면 selector/label/endpoints를 먼저 확인합니다.

## 블로그 후보

- Tistory: 매우 좋음. `Service가 필요한 이유: Pod IP가 바뀌어도 접근하는 방법`
- LinkedIn: 아직은 보류. Ingress까지 연결하면 트래픽 흐름 글로 좋음.
