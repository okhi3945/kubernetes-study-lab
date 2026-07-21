# 블로그 관리

## 운영 원칙

- GitHub: 실습 파일과 재현 가능한 명령 중심
- Tistory: 실습 과정을 읽기 쉽게 정리
- LinkedIn: 너무 기초적인 개념보다 운영 관점/장애 분석/회고 중심으로 게시

## 게시 단계

1. 실습 완료
2. 명령 결과 확인
3. Obsidian 개념 문서 보강
4. `blog/`에 Tistory 초안 작성
5. 글 흐름 다듬기
6. 필요 시 LinkedIn용 짧은 회고 문장 생성

## Tistory 글감 후보

- kind로 로컬 Kubernetes 실습 환경 구성하기
- Pod를 직접 배포하며 이해하는 Kubernetes 최소 실행 단위
- Deployment/ReplicaSet/Pod 관계를 그림으로 정리
- Service가 필요한 이유를 실습으로 확인
- ImagePullBackOff 직접 재현하고 분석하기
- CrashLoopBackOff 직접 재현하고 분석하기

## LinkedIn 게시 후보

- Kubernetes를 운영 관점에서 다시 학습하며 정리한 이유
- kind 기반 로컬 클러스터로 장애 대응 흐름을 연습한 기록
- Pod 장애 상태를 직접 재현하며 배운 확인 명령 체계
- Service/EndpointSlice를 보며 이해한 트래픽 흐름

## LinkedIn 게시 기준

- 단순 개념 설명만 있는 글은 보류
- 실습 결과, 장애 분석, 운영 관점, 회고가 포함되면 게시 후보
- 회사/프로젝트 내부 정보는 쓰지 않음
