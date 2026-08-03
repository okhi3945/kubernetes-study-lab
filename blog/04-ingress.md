# 04. Ingress로 외부 요청을 Service/Pod까지 연결하기

> 상태: 초안
> 게시 후보: Tistory
> LinkedIn: Service/Ingress 트래픽 흐름과 장애 확인 순서까지 정리하면 게시 후보

## 글의 목적

Service가 클러스터 내부 접근 지점이라면, Ingress는 외부 HTTP 요청을 Service로 연결하는 라우팅 규칙입니다. 이번 글에서는 kind 환경에서 ingress-nginx controller를 설치하고, Ingress → Service → Pod 흐름을 확인합니다.

## 핵심 메시지

- Ingress는 HTTP/HTTPS 요청의 라우팅 규칙입니다.
- Ingress 리소스만으로는 동작하지 않고 Ingress Controller가 필요합니다.
- Ingress는 직접 Pod로 가지 않고 Service를 backend로 사용합니다.
- 최종 흐름은 Ingress → Service → EndpointSlice → Pod입니다.

## 구조 그림

```text
curl / browser
  ↓ nginx.localtest.me
Ingress: nginx-ingress
  ↓ backend service: nginx-svc:80
Service: nginx-svc
  ↓ EndpointSlice
Pod 1 / Pod 2 / Pod 3
```

## 실습 흐름

1. Deployment/Service 확인
2. ingress-nginx controller 설치
3. Ingress 생성
4. `curl http://nginx.localtest.me`로 접근 확인
5. 장애 시 Ingress Controller, Ingress rule, Service, EndpointSlice 순서로 점검

## 운영 관점 확인 순서

```text
Ingress Controller Pod Running?
↓
Ingress rule host/path 맞음?
↓
backend Service 이름/포트 맞음?
↓
Service selector와 Pod label 맞음?
↓
EndpointSlice에 Pod IP 있음?
↓
Pod Ready 상태 정상?
```

## 실습 후 채울 결과

- Ingress Controller 상태:
- Ingress host:
- Backend Service:
- curl 결과:
- 장애/오류:
