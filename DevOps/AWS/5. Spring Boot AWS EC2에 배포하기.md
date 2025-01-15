AWS EC2 인스턴스에 **Spring Boot** 애플리케이션을 배포하는 과정을 설명합니다.

JDK 설치부터 애플리케이션 실행까지의 과정을 차근차근 알아보겠습니다.

## 1. JDK 설치

Spring Boot 애플리케이션을 실행하려면 EC2 인스턴스에 **JDK**가 설치되어 있어야 합니다. 아래 명령어로 JDK 17을 설치합니다.

```bash
$ sudo apt update && /
$ sudo apt install openjdk-17-jdk -y
```

설치 후 JDK가 정상적으로 설치되었는지 확인하려면 다음 명령어를 실행합니다.

```bash
$ java -version
```

![image.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/b2e2ba9e-b66a-46ed-bf3b-3a13d38ed76c/a7c519d2-0f89-444a-b973-5c5f90e5834e/image.png)

## 2. Spring Boot 프로젝트 클론

GitHub에서 Spring Boot 프로젝트를 EC2로 가져오기 위해 아래 명령어를 사용합니다.

```bash
$ git clone <https://github.com/>(깃헙 이름)/(레포 이름).git
$ cd (레포 이름)

```

## 3. `application.yml` 파일 직접 만들기

Spring Boot 프로젝트에서 환경 설정 파일인 `application.yml`은 민감한 정보를 포함할 수 있으므로, 직접 EC2 인스턴스에 생성하는 것이 일반적입니다. 다음과 같이 `application.yml` 파일을 작성합니다.

```yaml
$ cd src/main/resources
$ vi application.yml

server:
  port: 80
```

## 4. Spring Boot 애플리케이션 빌드 및 실행

![image.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/b2e2ba9e-b66a-46ed-bf3b-3a13d38ed76c/675cf991-e4a9-458e-b32d-c393b491953f/image.png)

애플리케이션을 빌드하고 실행하려면 아래 명령어를 사용합니다.

```bash
$ ./gradlew clean build
$ cd ~/ec2-spring-boot-sample/build/libs
$ sudo java -jar ec2-spring-boot-sample-0.0.1-SNAPSHOT.jar
```

애플리케이션을 백그라운드에서 실행하려면 `nohup` 명령어를 사용합니다.

```bash
$ sudo nohup java -jar ec2-spring-boot-sample-0.0.1-SNAPSHOT.jar
```

## 5. 서버 상태 확인

![image.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/b2e2ba9e-b66a-46ed-bf3b-3a13d38ed76c/fe7e8ca4-fb5f-4250-a6dc-aee7d3b37571/image.png)

Spring Boot 애플리케이션이 정상적으로 실행되었는지 확인하려면, 브라우저에서 EC2 인스턴스의 **퍼블릭 IP 주소**를 입력하여 결과 페이지가 잘 표시되는지 확인합니다.

## 6. EC2 인스턴스 종료

![image.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/b2e2ba9e-b66a-46ed-bf3b-3a13d38ed76c/a98b4a13-628a-4ea9-825a-d75772b036ff/image.png)

실습이 끝난 후 EC2 인스턴스를 종료하여 비용 발생을 방지합니다.

![image.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/b2e2ba9e-b66a-46ed-bf3b-3a13d38ed76c/88f08a6b-b36c-4cae-a40b-e864b1d757d6/image.png)

![image.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/b2e2ba9e-b66a-46ed-bf3b-3a13d38ed76c/2b37b2f8-da7c-49ca-8016-2e629867b7c2/image.png)

사용하지 않는 **탄력적 IP**를 릴리스하는 것도 잊지 마세요.