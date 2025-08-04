# 실습 1: 내 애플리케이션, 살아있니? (Liveness Probe의 필요성)

### 🎯 학습 목표

1. `livenessProbe`가 없는 애플리케이션의 문제점을 직접 확인한다.
2. 애플리케이션에 버그가 발생하여 응답 불능 상태가 되었을 때, `livenessProbe`가 어떻게 문제를 감지하고 Pod를 자동으로 복구하는지 이해한다.

### 📜 실습 시나리오

1.  **5번 요청마다 30초간 멈추는 버그**가 있는 간단한 웹 서버를 만든다.
2.  **Probe가 없는 상태**로 Pod를 배포하고 5번 이상 접속하여 서버가 멈추는 현상을 관찰한다.
    - `kubectl get pod`의 `STATUS`는 `Running`이지만, 실제 서비스는 먹통이다.
3.  **httpGet 방식의 livenessProbe**를 추가하여 다시 배포한다.
4.  동일하게 5번 이상 접속하여 서버가 멈추자, 쿠버네티스가 Pod의 `RESTARTS` 횟수를 늘리며 **자동으로 재시작**시켜 서비스가 복구되는 것을 확인한다.

### ✅ 확인 포인트

- Probe가 없을 때: Pod는 `Running` 상태를 유지하지만 `curl` 명령어가 응답 없는 상태.
- Probe가 있을 때: `kubectl describe pod` 명령어로 `Liveness probe failed` 이벤트를 확인하고, `kubectl get pod`의 `RESTARTS` 카운트가 `0`에서 `1`로 증가하는 것을 관찰.
