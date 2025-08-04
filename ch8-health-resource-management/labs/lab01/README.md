# Lab 01: `todo-list` API 서버에 Probes 추가하기

### 🎯 목표
`todo-list` 애플리케이션의 백엔드(API 서버) `Deployment`에 아래 요구사항에 맞는 `livenessProbe`와 `readinessProbe`를 구현합니다.

### 🎯 미션 (Your Mission)

**`todo-backend-deployment.yaml` 파일 수정:**
   * **Liveness Probe 요구사항:**
        * 컨테이너의 프로세스가 응답 불능 상태인지 확인해야 합니다.
        * **구현 조건:** 컨테이너 시작 후 **15초** 뒤부터 검사를 시작하며, **20초**마다 컨테이너의 `8080` 포트로 TCP 연결을 시도하세요. 연결이 실패하면 Pod는 비정상으로 간주되어 재시작되어야 합니다.

   * **Readiness Probe 요구사항:**
        * 이 Pod가 실제 서비스 트래픽을 처리할 준비가 되었는지(특히 DB 연결 여부) 확인해야 합니다.
        * **구현 조건:** 컨테이너 시작 후 **5초** 뒤부터, **10초**마다 `/api/todos` 경로로 HTTP GET 요청을 보내세요. 이 요청이 성공해야만 Pod는 트래픽을 받을 수 있는 `READY` 상태가 되어야 합니다.

### ✅ 확인 방법

1.  수정한 `todo-backend-deployment.yaml`을 배포합니다.
2.  `kubectl describe pod <todo-pod-name>` 명령어를 실행하여, 여러분이 설정한 Probe의 상세 조건(`delay`, `period` 등)이 올바르게 적용되었는지 확인합니다.
3.  Pod가 시작될 때 `READY` 상태가 `0/1`이었다가 잠시 후 `1/1`로 바뀌는 것을 관찰하세요.
