# 04. Ingress 실습

## 목표

Service까지는 클러스터 내부 접근 지점입니다. Ingress는 외부 HTTP/HTTPS 요청을 Service로 라우팅하는 리소스입니다.

## 이번에 이해할 그림

```text
Browser / curl
  ↓ host: nginx.localtest.me
Ingress
  ↓ rule: / → nginx-svc:80
Service: nginx-svc
  ↓ EndpointSlice
Pod 1 / Pod 2 / Pod 3
```

## 핵심 개념

- Ingress는 HTTP/HTTPS 라우팅 규칙입니다.
- Ingress만 만든다고 동작하지 않습니다.
- 반드시 Ingress Controller가 클러스터에 설치되어 있어야 합니다.
- kind에서는 보통 ingress-nginx controller를 설치해서 실습합니다.
- Ingress는 Service로 트래픽을 보내고, Service는 Pod로 트래픽을 보냅니다.

## 0. 현재 상태 확인

Deployment/Service가 있는지 확인합니다.

```bash
kubectl get deploy,svc,pod -o wide
```

없으면 생성합니다.

```bash
kubectl apply -f manifests/deployment/01-nginx-deployment.yaml
kubectl apply -f manifests/service/01-nginx-clusterip-service.yaml
```

## 1. Ingress Controller 설치

kind에서는 Ingress 리소스만으로는 외부 접속이 되지 않습니다.
먼저 ingress-nginx controller를 설치합니다.

```bash
kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/main/deploy/static/provider/kind/deploy.yaml
```

준비 상태 확인:

```bash
kubectl wait --namespace ingress-nginx \
  --for=condition=ready pod \
  --selector=app.kubernetes.io/component=controller \
  --timeout=180s
```

## 2. Ingress 생성

```bash
kubectl apply -f manifests/ingress/01-nginx-ingress.yaml
```

확인:

```bash
kubectl get ingress
kubectl describe ingress nginx-ingress
```

## 3. 로컬에서 접근 확인

`localtest.me`는 127.0.0.1로 해석되는 테스트용 도메인입니다.

```bash
curl -I http://nginx.localtest.me
```

기대 결과:

```text
HTTP/1.1 200 OK
```

## 4. 장애 확인 포인트

Ingress 접속이 안 되면 아래 순서로 봅니다.

```bash
kubectl get pods -n ingress-nginx
kubectl get ingress
kubectl describe ingress nginx-ingress
kubectl get svc nginx-svc
kubectl get endpointslice -l kubernetes.io/service-name=nginx-svc -o wide
```

확인 순서:

```text
Ingress Controller 실행 여부
↓
Ingress rule host/path 확인
↓
backend Service 이름/포트 확인
↓
Service selector 확인
↓
EndpointSlice 대상 Pod 확인
```

## 5. 정리

Ingress만 삭제:

```bash
kubectl delete ingress nginx-ingress
```

Ingress Controller까지 삭제:

```bash
kubectl delete -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/main/deploy/static/provider/kind/deploy.yaml
```

## 이번 실습 후 말할 수 있어야 하는 것

- Ingress는 외부 HTTP/HTTPS 요청을 Service로 보내는 라우팅 규칙입니다.
- Ingress Controller가 있어야 Ingress가 실제로 동작합니다.
- 외부 요청 흐름은 Ingress → Service → EndpointSlice → Pod 순서입니다.
- Service selector가 틀리면 Ingress도 최종적으로 Pod에 도달하지 못합니다.

## 블로그 후보

- Tistory: 좋음. `Ingress로 외부 요청을 Service/Pod까지 연결하기`
- LinkedIn: Service/Ingress 트래픽 흐름을 운영 관점으로 묶으면 게시 후보.
