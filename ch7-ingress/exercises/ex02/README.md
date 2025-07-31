# 실습 2: Ingress Controller 설치하기

이전 실습에서 우리는 여러 서비스를 외부에 노출시키기 위해 `LoadBalancer`를 사용하는 것의 한계를 명확히 인지했습니다. 그 해결책이 바로 **Ingress**라는 것도 알게 되었죠.

Ingress가 실제로 동작하려면 두 가지 핵심 요소가 필요합니다.

1.  **Ingress 리소스 (Resource):** 트래픽을 어떻게 처리할지에 대한 **규칙이 담긴 설계도** (YAML 파일).
2.  **Ingress 컨트롤러 (Controller):** 클러스터 안에서 실행되면서, 이 설계도를 읽고 규칙에 따라 트래픽을 실제로 전달해주는 **똑똑한 실행자(로봇)**.

설계도(`Ingress` 리소스)를 만들기 전에, 그 설계도를 읽고 실행할 로봇(`Ingress Controller`)을 먼저 클러스터에 배치해야 합니다. 이번 실습에서는 바로 그 Ingress Controller를 설치하는 과정을 진행합니다.

---

### 🎯 학습 목표

1.  Ingress 리소스와 Ingress Controller의 관계를 이해한다.
2.  `minikube` 환경에서 `addon` 기능을 사용하여 NGINX Ingress Controller를 손쉽게 활성화할 수 있다.
3.  Ingress Controller가 정상적으로 실행 중인지 Pod 상태를 통해 확인할 수 있다.
4.  외부에서 Ingress Controller에 접근할 수 있는 주소를 확인할 수 있다.

---

## 1\. Step 1: Ingress Controller 활성화하기

다행히도 `minikube`는 가장 널리 쓰이는 **NGINX Ingress Controller**를 애드온(addon) 형태로 내장하고 있어, 명령어 한 줄로 매우 간단하게 설치할 수 있습니다.

터미널에서 아래 명령어를 실행하세요.

```bash
minikube addons enable ingress
```

잠시 기다리면 다음과 같은 성공 메시지를 볼 수 있습니다.

```
🌟 The 'ingress' addon is enabled
```

이 명령어는 여러분의 클러스터에 Ingress Controller의 역할을 수행할 Nginx Pod과 관련 서비스 등을 자동으로 설치합니다.

## 2\. Step 2: 설치 확인하기

정말로 '실행자 로봇'이 잘 배치되었는지 확인해 봅시다. Ingress Controller는 보통 `ingress-nginx` 라는 별도의 작업 공간(네임스페이스)에 설치됩니다.

아래 명령어로 해당 네임스페이스의 Pod들을 확인합니다.

```bash
kubectl get pods -n ingress-nginx
```

**예상 출력:**

```
NAME                                        READY   STATUS      RESTARTS   AGE
ingress-nginx-admission-create-xxxxx        0/1     Completed   0          60s
ingress-nginx-admission-patch-xxxxx         0/1     Completed   0          60s
ingress-nginx-controller-xxxxxxxxxx-xxxxx   1/1     Running     0          60s
```

`ingress-nginx-controller-...` 라는 이름의 Pod이 `Running` 상태로 보인다면 성공적으로 설치된 것입니다. 바로 이 Pod이 우리가 앞으로 만들 모든 Ingress 규칙들을 감시하고 실행하는 핵심적인 역할을 합니다.

## 3\. Step 3: Ingress Controller의 외부 주소 확인하기

이제 '실행자(Controller)'는 준비되었습니다. 그럼 외부의 트래픽은 어떤 주소로 이 실행자에게 찾아와야 할까요? Ingress Controller 자체도 하나의 `Service`를 통해 외부에 노출됩니다.

`minikube` 환경에서는 아래의 편리한 명령어로 Ingress Controller의 외부 접속 주소를 바로 확인할 수 있습니다.

```bash
minikube service ingress-nginx-controller --url -n ingress-nginx
```

**예상 출력:**

```
http://192.168.49.2:31180
```

_(IP주소와 포트 번호는 여러분의 환경에 따라 다를 수 있습니다.)_

이 주소가 바로 우리 클러스터의 **단일 진입점(Single Entrypoint)**, 즉 '정문' 주소가 됩니다. 앞으로 우리는 이 주소 하나만 기억하면 됩니다.

---

### ⭐ 핵심 정리

- \*\*Ingress 리소스(설계도)\*\*는 \*\*Ingress Controller(실행자)\*\*가 있어야만 동작한다.
- Minikube 환경에서는 `minikube addons enable ingress` 명령어로 NGINX Ingress Controller를 손쉽게 설치할 수 있다.
- 설치된 Ingress Controller는 `ingress-nginx` 네임스페이스에서 `Running` 상태의 Pod으로 확인할 수 있다.
- `minikube service --url` 명령어로 Ingress Controller의 외부 접속 주소(정문 주소)를 확인할 수 있다.

이제 우리 클러스터의 '정문'이 세워졌습니다. 다음 실습에서는 이 정문을 통과하는 트래픽을 어떻게 분배할지 첫 번째 '안내 규칙(Ingress 리소스)'을 만들어 보겠습니다.
