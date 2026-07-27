# 03. Service가 필요한 이유: Pod IP가 바뀌어도 접근하기

> 상태: 초안
> 게시 후보: Tistory
> LinkedIn: Ingress 실습까지 연결 후 트래픽 흐름 글로 재가공 추천

## 글의 목적

Deployment로 여러 Pod를 띄운 뒤, Pod IP가 바뀌어도 안정적으로 접근하기 위해 Service가 왜 필요한지 실습으로 확인한다.

## 핵심 메시지

- Pod IP는 고정된 접근 주소로 쓰면 안 됩니다.
- Service는 Pod 앞에 붙는 고정 접근 지점입니다.
- Service는 selector로 Pod label을 찾아 트래픽을 전달합니다.
- selector가 틀리면 Service는 있어도 연결 대상이 없습니다.

## 구조 그림

```text
Client
  ↓
Service: nginx-svc
  ↓ selector: app=nginx-deploy
EndpointSlice
  ↓
Pod 1: 10.244.x.x
Pod 2: 10.244.x.x
Pod 3: 10.244.x.x
```

## 실습 흐름

1. Deployment Pod 3개 확인
2. ClusterIP Service 생성
3. EndpointSlice 확인
4. 임시 curl Pod로 Service 접근
5. Pod 하나 삭제 후 Service 접근 유지 확인
6. selector가 틀린 Service로 장애 상황 확인

## 사용할 명령

```bash
kubectl apply -f manifests/service/01-nginx-clusterip-service.yaml
kubectl get svc nginx-svc
kubectl describe svc nginx-svc
```

```bash
kubectl get endpointslice -l kubernetes.io/service-name=nginx-svc -o wide
```

```bash
kubectl run curl-test --image=curlimages/curl:8.10.1 -it --rm --restart=Never -- curl -I http://nginx-svc
```

## 운영 관점 포인트

Service 장애를 볼 때는 아래 순서가 좋습니다.

```text
Service 존재 확인
↓
selector 확인
↓
Pod label 확인
↓
EndpointSlice 확인
↓
Pod readiness/log 확인
```

## 실습 후 채울 결과

- Service IP:
- EndpointSlice에 잡힌 Pod IP:
- curl 결과:
- Pod 삭제 후 새 Pod IP:
- selector 불일치 Service 결과:
