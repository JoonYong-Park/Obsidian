![image.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/b2e2ba9e-b66a-46ed-bf3b-3a13d38ed76c/f0b31cfb-536a-411c-abc3-88aed9ff1b90/image.png)

AWS의 **ELB(Elastic Load Balancer)**와 **HTTPS**, 그리고 이를 활용한 아키텍처 구성을 살펴보겠습니다.

특히, ELB의 **트래픽 분산 기능**과 **SSL/TLS 인증서를 통한 HTTPS 적용 방법**에 대해 다뤄보겠습니다.

# 1. ELB(Elastic Load Balancer)란?

![https://prod-files-secure.s3.us-west-2.amazonaws.com/b2e2ba9e-b66a-46ed-bf3b-3a13d38ed76c/40ef5802-fa7e-4158-90f4-0e683b8b57e0/Untitled.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/b2e2ba9e-b66a-46ed-bf3b-3a13d38ed76c/40ef5802-fa7e-4158-90f4-0e683b8b57e0/Untitled.png)

🔍 **한 줄 요약**: **ELB는 트래픽(부하)을 적절하게 분배해주는 장치**입니다.

- *로드밸런서(Load Balancer)**라고도 불리는 ELB는 **서버에 가해지는 트래픽을 여러 대의 서버로 분산**하여 시스템의 성능과 안정성을 높입니다. 주로 **서버를 2대 이상 사용할 때 ELB를 도입**하여 트래픽을 효율적으로 관리하게 됩니다.

# 2. SSL/TLS란?

![image.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/b2e2ba9e-b66a-46ed-bf3b-3a13d38ed76c/c2eb2d0d-c152-4617-b206-951c346a5da3/image.png)

🔐 **SSL/TLS**는 **HTTP를 HTTPS로 바꿔주는 인증서**로, 웹사이트와 서버 간의 통신을 **암호화**하는 데 사용됩니다. **ELB는 SSL/TLS 기능**을 제공하여, **SSL/TLS 인증서를 통해 HTTP를 HTTPS로 전환**하고 안전한 통신을 보장합니다.

### SSL과 TLS의 차이

- **SSL(Secure Sockets Layer)**: 초기의 보안 프로토콜로, 현재는 **TLS(Transport Layer Security)**가 SSL을 대체하고 있습니다.
- **TLS**는 **SSL의 개선된 버전**으로, 더 안전한 암호화와 인증을 제공합니다. 오늘날 대부분의 HTTPS 연결은 TLS를 사용합니다.

# 3. HTTPS란?

🔒 **HTTPS**는 **HTTP에 보안을 추가한 프로토콜**로, 데이터를 서버와 주고받을 때 **암호화된 통신**을 제공합니다. HTTPS를 적용하는 주요 이유는 다음과 같습니다:

1. **보안성**:
    - 데이터를 **암호화**하여 중간에서 가로채더라도 내용을 볼 수 없게 합니다.
    - 암호화를 통해 **해킹과 데이터 유출**로부터 보호할 수 있습니다.
2. **사용자 신뢰도**:
    - **HTTPS가 적용되지 않은 웹사이트**는 사용자에게 보안 경고 메시지가 표시되며, 이로 인해 **사용자가 이탈할 가능성**이 높아집니다.
    - HTTPS를 적용하면, **브라우저에 자물쇠 아이콘이 표시**되어 신뢰할 수 있는 사이트임을 알릴 수 있습니다.

# 4. 현업에서의 HTTPS 적용

✅ **대부분의 웹사이트는 HTTPS를 필수적으로 적용**합니다. HTTPS 인증을 받은 웹사이트가 **백엔드 서버와 통신**하려면, **백엔드 서버의 주소도 HTTPS 인증을 받아야 합니다**. 따라서, IP 주소 대신 **HTTPS 인증을 받은 도메인 주소**로 백엔드 서버와 통신하게 됩니다.

## 도메인 구성 예시

- **웹사이트 주소**: `https://joonyong.co.kr`
- **백엔드 API 서버 주소**: `https://api.joonyong.co.kr`

# 5. ELB를 활용한 아키텍처 구성

ELB 도입 전과 후의 아키텍처를 비교해 보겠습니다.

## ELB 도입 전 아키텍처

사용자들은 **EC2 인스턴스의 IP 주소 또는 도메인**에 직접 요청을 보내는 구조였습니다.

![https://prod-files-secure.s3.us-west-2.amazonaws.com/b2e2ba9e-b66a-46ed-bf3b-3a13d38ed76c/708b8149-5ec9-401f-a124-b6a4a775fc74/Untitled.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/b2e2ba9e-b66a-46ed-bf3b-3a13d38ed76c/708b8149-5ec9-401f-a124-b6a4a775fc74/Untitled.png)

## ELB 도입 후 아키텍처

ELB를 도입한 후에는 사용자들이 **EC2에 직접 요청을 보내지 않고**, **ELB를 통해 요청**을 보내도록 구성합니다. **도메인도 ELB에 연결**하고, **HTTPS도 ELB에서 처리**하도록 설정합니다.

![https://prod-files-secure.s3.us-west-2.amazonaws.com/b2e2ba9e-b66a-46ed-bf3b-3a13d38ed76c/1c20bad6-8b9d-4286-ac80-4fd3531f21de/Untitled.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/b2e2ba9e-b66a-46ed-bf3b-3a13d38ed76c/1c20bad6-8b9d-4286-ac80-4fd3531f21de/Untitled.png)

이런 구성은 **서버 부하 분산**, **보안 강화**, **높은 가용성**을 제공하는 웹 애플리케이션을 구축할 때 필수적입니다.

# 6. 결론

ELB(Elastic Load Balancer)는 **트래픽 분산**과 **SSL/TLS를 통한 HTTPS 적용**이라는 두 가지 중요한 역할을 수행합니다. 이를 통해 웹 애플리케이션의 **안정성과 보안성을 높일 수 있으며**, 현대적인 클라우드 기반 아키텍처에서 필수적인 요소로 자리 잡고 있습니다.

---

![image.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/b2e2ba9e-b66a-46ed-bf3b-3a13d38ed76c/bfa5ad1a-e857-46ac-9190-57fc374105b8/image.png)

![image.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/b2e2ba9e-b66a-46ed-bf3b-3a13d38ed76c/7d928ff3-6922-4136-b3e3-d808a950badb/image.png)

![image.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/b2e2ba9e-b66a-46ed-bf3b-3a13d38ed76c/380b9df4-6d1c-412c-828f-d6de75baa850/image.png)

이번엔 ELB ip로 접속

![image.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/b2e2ba9e-b66a-46ed-bf3b-3a13d38ed76c/add76cc4-91c9-4839-8d83-1d44cfa55180/image.png)

![image.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/b2e2ba9e-b66a-46ed-bf3b-3a13d38ed76c/bc98e974-43e2-44e3-be33-d8308243027a/image.png)

성공