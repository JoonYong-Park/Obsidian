# 1. 리전(Region) 선택하기

![image.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/b2e2ba9e-b66a-46ed-bf3b-3a13d38ed76c/5513ac4e-d9a7-4e18-99c7-7d6c70e82ab0/image.png)

AWS EC2 인스턴스를 생성하기 전에 가장 먼저 해야 할 작업은 **리전(Region) 선택**입니다. 리전은 AWS가 전 세계적으로 구축한 데이터 센터 위치를 의미하며, 주 사용자 위치에 맞는 리전을 선택해야 네트워크 지연을 최소화할 수 있습니다.

> 한국 사용자라면 아시아 태평양(서울) 리전을 선택하는 것이 적합합니다.

# 2. EC2 인스턴스 설정하기

### (1) 이름 및 태그 설정

![image.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/b2e2ba9e-b66a-46ed-bf3b-3a13d38ed76c/3d753cde-24b6-4f96-9c11-5414784f4fb3/image.png)

인스턴스의 이름은 해당 서버의 역할을 쉽게 알아볼 수 있도록 설정하세요. 예를 들어, `web-server`, `database-server`와 같은 이름이 좋습니다.

### (2) 운영체제 선택

![image.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/b2e2ba9e-b66a-46ed-bf3b-3a13d38ed76c/39eca6fc-0862-4589-bcb8-83b05a38de7e/image.png)

OS(운영체제)란 Mac, Windows 7, Windows 10, Windows 11 같은 것들이 OS이다. 하지만 Windows나 Mac OS는 생각보다 용량도 많이 차지하고 성능도 많이 잡아먹는다. 그래서 서버를 배포할 컴퓨터의 OS는 훨씬 가벼운 **Ubuntu**를 많이 사용합니다.

EC2 인스턴스에 사용할 운영체제로는 **Ubuntu 22.04 LTS**를 추천합니다. 가벼운 리눅스 배포판으로 서버 성능을 최적화할 수 있습니다.

### (3) 인스턴스 유형 선택

![image.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/b2e2ba9e-b66a-46ed-bf3b-3a13d38ed76c/206d4054-2ce1-469f-877f-8d2deec47f42/image.png)

인스턴스란 EC2에서 빌리는 컴퓨터 한대를 의미하는 하나의 단위입니다.

**인스턴스 유형**은 서버 사양을 결정하는 항목입니다. 기본적으로 학습과 테스트 용도로는 **t2.micro** 인스턴스를 선택합니다. 이는 프리 티어로 제공되며, 소규모 서비스에도 충분히 사용할 수 있습니다.

### (4) 키 페어 설정

![image.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/b2e2ba9e-b66a-46ed-bf3b-3a13d38ed76c/0862f718-8dc3-4fc5-bd58-66e9b7a45cae/image.png)

- *키 페어(Key Pair)**는 EC2 인스턴스에 접근할 때 사용하는 비밀번호 역할을 합니다. 키 페어를 생성하면 **.pem 파일**을 다운로드 받게 되며, 이 파일을 분실하지 않도록 안전하게 보관해야 합니다.

# 3. 보안 그룹(Security Group) 설정

![image.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/b2e2ba9e-b66a-46ed-bf3b-3a13d38ed76c/7630f45f-159e-4ccf-b7cc-e51b430e2076/image.png)

## (1) 보안 그룹이란?

**보안 그룹(Security Group)**이란 **AWS 클라우드에서의 네트워크 보안**을 의미합니다.

![Untitled (7).png](https://prod-files-secure.s3.us-west-2.amazonaws.com/b2e2ba9e-b66a-46ed-bf3b-3a13d38ed76c/955eda2a-fff9-43d4-ac52-bc24707e23e5/Untitled_(7).png)

**EC2 인스턴스**를 **집**이라고 생각한다면, **보안 그룹**은 **집 바깥 쪽에 쳐져있는 울타리와 대문**이라고 생각하면 됩니다. 집에 접근할 때 울타리의 대문에서 접근해도 되는 요청인지 보안 요원이 검사를 하는 것과 비슷하다.

**인바운드 트래픽(즉, 외부에서 EC2 인스턴스로 보내는 트래픽)**에서 어떤 트래픽만 허용할 지 설정

**아웃바운드 트래픽(즉, EC2 인스턴스에서 외부로 나가는 트래픽)**에서 어떤 트래픽만 허용할 지 설정

## (2) 보안그룹 설정하기

![image.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/b2e2ba9e-b66a-46ed-bf3b-3a13d38ed76c/43eba284-e9e8-4642-8650-b309fb253931/image.png)

보안 그룹은 EC2 인스턴스로 들어오는 트래픽을 제어하는 방화벽 역할을 합니다. **22번 포트(SSH)**와 **80번 포트(HTTP)**를 허용하여 서버에 원격 접속하고, 웹 서버로의 요청을 처리할 수 있게 설정합니다.

> 22번 포트는 SSH 원격 접속을 위해, 80번 포트는 HTTP 요청을 처리하기 위해 열어둡니다.

## (3) well-known port(잘 알려진 포트)

포트 번호는 **0 ~ 65,535번**까지 사용할 수 있다. 그 중에서 **0 ~ 1023번**까지의 포트 번호는 주요 통신을 위한 규약에 따라 이미 정해져 있다. 이렇게 **규약을 통해 역할이 정해져있는 포트 번호**를 보고 **잘 알려진 포트(well-known port)**라고 합니다.

규약으로 정해져 있는 포트 번호 중 자주 사용되는 포트 번호에 대해서만 알아보

- **22번 (SSH, Secure Shell Protocol)** : 원격 접속을 위한 포트 번호
    - EC2 인스턴스에 연결할 때 22번 포트를 사용한다.
- **80번 (HTTP)** : HTTP로 통신을 할 때 사용
- **443번 (HTTPS)** : HTTPS로 통신을 할 때 사용

# 4. 스토리지 구성

![image.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/b2e2ba9e-b66a-46ed-bf3b-3a13d38ed76c/0935ffe8-b84e-4638-b439-847f21114f3e/image.png)

EC2 인스턴스의 저장 공간으로는 **EBS(Elastic Block Storage)**를 사용합니다. 실습에서는 프리 티어에서 제공하는 **gp3** 유형의 스토리지를 선택하고, 30GiB의 용량을 설정합니다. 추후에 용량을 늘릴 수 있으므로 초기에는 과도한 설정이 필요 없습니다.

![image.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/b2e2ba9e-b66a-46ed-bf3b-3a13d38ed76c/594b5e47-ee83-4264-a232-3061071a69f3/image.png)

실습을 위한 인스턴스 설정은 끝났습니다. 이제 인스턴스 시작을 하시면 됩니다.

# 5. 탄력적 IP 연결

EC2 인스턴스를 중지했다가 다시 시작할 때마다 IP가 변경되는 것을 방지하기 위해 **탄력적 IP(Elastic IP)**를 설정합니다. 탄력적 IP는 고정 IP를 제공하여, 중지 후에도 같은 IP로 인스턴스를 운영할 수 있게 해줍니다.

![image.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/b2e2ba9e-b66a-46ed-bf3b-3a13d38ed76c/5f8744e6-5cec-40ca-b291-a0e5b813b479/image.png)

![image.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/b2e2ba9e-b66a-46ed-bf3b-3a13d38ed76c/0fde2909-fbf6-4494-ab0c-5af28a59ad56/image.png)

![image.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/b2e2ba9e-b66a-46ed-bf3b-3a13d38ed76c/8581d91d-1b2b-43df-ad37-8ece7e280dc0/image.png)

작업 > 탄력적 ip 연결

![image.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/b2e2ba9e-b66a-46ed-bf3b-3a13d38ed76c/21f45b7f-aa0c-45b3-92e8-c4aa5167e445/image.png)

ip 선택 > 연결

![image.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/b2e2ba9e-b66a-46ed-bf3b-3a13d38ed76c/d22554f8-ed2b-4496-9137-6895e8a47b94/image.png)

연결이 완료 되었습니다.

# 6. EC2 인스턴스 접속

![image.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/b2e2ba9e-b66a-46ed-bf3b-3a13d38ed76c/952ae0d5-84e9-4245-8893-a089fb3247d0/image.png)

인스턴스를 생성한 후에는 퍼블릭 IP 주소를 통해 원격으로 접속할 수 있습니다. 실습이 끝나면 EC2 인스턴스를 **중지**하거나 **종료(삭제)**하여 불필요한 비용 발생을 막을 수 있습니다.