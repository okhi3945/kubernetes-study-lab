# 초기 Node NotReady / Pod ContainerCreating 관찰

## 상황

kind 클러스터 생성 직후 `kubectl get nodes`에서 Node가 잠시 `NotReady`로 보였습니다.
nginx Pod 생성 직후에도 Pod가 잠시 `ContainerCreating` 상태였습니다.

## 증상

```text
k8s-study-control-plane   NotReady
k8s-study-worker          NotReady
nginx-basic               ContainerCreating
```

## 원인 후보

- 클러스터 생성 직후 CNI 설치/초기화가 아직 끝나지 않음
- worker node join 직후 kubelet/node 상태 반영 대기
- nginx 이미지 pull 및 컨테이너 생성 진행 중

## 확인 명령

```bash
kubectl get nodes
kubectl get pod nginx-basic -o wide
kubectl describe pod nginx-basic
kubectl get events --sort-by=.lastTimestamp
```

## 실제 결과

- 약간 대기 후 Node 2개 모두 `Ready`
- Pod도 `Running`, `READY 1/1` 상태로 전환
- 최종 HTTP 확인 결과 `HTTP/1.1 200 OK`

## 운영 관점 메모

- 클러스터/Pod 생성 직후의 일시적인 `NotReady`, `ContainerCreating`은 정상 초기화 과정일 수 있습니다.
- 단, 시간이 지나도 지속되면 아래를 봐야 합니다.
  - CNI Pod 상태
  - Node 이벤트
  - 이미지 pull 실패
  - 볼륨 attach/mount 실패
  - kubelet 상태

## 게시 후보

- Tistory: “ContainerCreating은 항상 장애일까?” 같은 짧은 트러블슈팅 글로 확장 가능
- LinkedIn: 아직 단독 게시보다는 추후 여러 장애 상태와 묶어서 게시 추천
