![image.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/b2e2ba9e-b66a-46ed-bf3b-3a13d38ed76c/14dea3bf-df86-447f-9e43-bb711e5f8d08/b4d9fd70-ddae-4b7b-8111-a28afe3baf74.png)

AWS에서 제공하는 **Route 53**과 그 핵심 개념인 **DNS(Domain Name System)**에 대해 알아보겠습니다. 또한, 현업에서 Route 53이 어떻게 사용되는지도 함께 살펴보겠습니다.

# 1. Route 53이란?

**Route 53**은 AWS에서 제공하는 **도메인 관리 및 DNS 서비스**입니다. 즉, **도메인을 발급**하고 **관리**할 수 있는 기능을 제공해, 특정 웹사이트나 서버에 대한 접근을 쉽게 해줍니다.

# 2. 도메인이란?

도메인은 [www.naver.com](http://www.naver.com/), [youtube.com](http://youtube.com/)처럼 인터넷에서 특정 사이트나 서버에 접속하기 위해 사용하는 **문자 형태의 주소**입니다. 컴퓨터는 숫자로 이루어진 IP 주소를 사용해 통신하지만, 사람들에게 더 친숙한 형태의 주소가 **도메인**입니다.

## 2.1) 전문 용어로 설명하자면...

Route 53은 전문적으로 **DNS(Domain Name System) 서비스**입니다. **DNS**는 우리가 친숙한 도메인 이름을 **IP 주소**로 변환해주는 시스템입니다.

💡 **Route 53**이라는 이름은 **DNS(Domain Name System)**에서 사용하는 **53번 포트**에서 유래했습니다.

# 3. DNS(Domain Name System)란?

과거에는 웹사이트나 서버에 접속하기 위해 **IP 주소**(예: `192.168.0.1`)를 사용해야 했습니다. 하지만 숫자로 이루어진 IP 주소는 기억하기 어려워, **문자로 된 주소**인 도메인을 통해 접근할 수 있도록 DNS가 개발되었습니다.

## 3.1) DNS의 역할

DNS는 사람이 이해하기 쉬운 **도메인 이름을 IP 주소로 변환**해주는 시스템입니다. 이를 통해 사용자는 IP 주소를 외울 필요 없이 도메인을 통해 쉽게 웹사이트에 접속할 수 있습니다. 예를 들어, 사용자가 [**www.naver.com**](http://www.naver.com/)에 접속하려고 하면, DNS가 이를 **Naver의 서버 IP 주소**로 변환해 접속을 가능하게 만듭니다.

![https://prod-files-secure.s3.us-west-2.amazonaws.com/b2e2ba9e-b66a-46ed-bf3b-3a13d38ed76c/6bacacba-ccee-4e0b-8b52-686244b31049/Untitled.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/b2e2ba9e-b66a-46ed-bf3b-3a13d38ed76c/6bacacba-ccee-4e0b-8b52-686244b31049/Untitled.png)

# 4. 현업에서의 Route 53 활용 여부

일반적으로 **프론트엔드 웹 페이지**든 **백엔드 서버**든, IP 주소보다는 **도메인**을 기반으로 통신합니다. 그 이유 중 하나는 바로 **HTTPS** 적용 때문입니다.

## 4.1 왜 도메인을 사용하는가?

- **HTTPS 적용**: IP 주소로는 **HTTPS 인증서**를 적용하기 어렵습니다. HTTPS는 보안이 필요한 모든 웹사이트에서 필수적입니다. HTTPS를 적용하려면 **도메인**이 필요합니다.
- **사용자 편의성**: 숫자로 된 IP 주소 대신 도메인을 사용하면 사용자가 더 쉽게 기억하고 접근할 수 있습니다.

# 5. DNS 서비스는 Route 53만 있는가?

DNS 서비스를 제공하는 플랫폼은 AWS의 Route 53 외에도 많습니다. 예를 들어, 국내의 **가비아(gabia)**, **후이즈(whois)** 같은 업체들도 도메인 등록과 DNS 관리 서비스를 제공합니다.

따라서 **현업에서는 무조건 Route 53을 고집하지는 않습니다**. 각 서비스마다 제공하는 도메인의 종류가 다르기 때문에, 회사나 개인은 원하는 도메인을 제공하는 DNS 서비스를 사용하게 됩니다.

예를 들어, 특정 도메인을 AWS Route 53에서 찾을 수 없지만, 가비아에서는 원하는 도메인이 있을 수 있습니다. 이 경우, 가비아에서 도메인을 구매하고 관리할 수 있습니다.

## 5.1) 현업에서의 Route 53 활용

AWS 환경을 주로 사용하는 회사에서는 **Route 53**이 통합 관리가 가능하므로 자주 사용됩니다. AWS의 다른 서비스(예: EC2, S3)와 자연스럽게 연동되기 때문입니다. 또한, Route 53은 강력한 **DNS 라우팅** 기능과 **고가용성**, **자동 복구** 기능을 제공하여 대규모 트래픽 처리에도 적합합니다.

---

## 6. 결론

**Route 53**은 AWS가 제공하는 **DNS 및 도메인 관리 서비스**로, 도메인 설정부터 IP 주소 매핑, HTTPS 설정까지 다양한 기능을 제공합니다. 하지만, 현업에서는 반드시 Route 53을 사용하지 않고, 다양한 도메인 등록 기관과 DNS 서비스를 선택할 수 있습니다. 각 서비스마다 제공하는 도메인의 종류와 가격이 다르기 때문에, **필요에 맞는 서비스를 선택하는 것이 중요**합니다.