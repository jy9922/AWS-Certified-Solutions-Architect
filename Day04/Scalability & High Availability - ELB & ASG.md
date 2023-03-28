# 5. Scalability & High Availability - ELB & ASG

## Scalability & High Availability

### Scalability

- 확장성은 애플리케이션 시스템이 조정을 통해 더 많은 양을 처리할 수 있다는 것을 의미한다.
- 확장성의 두 종류
    - 수직 확장성
    - 수평 확장성 ( = 탄력성 )
- 확장성과 고가용성은 다른 개념이다.

### Vertical Scalability

- **인스턴스의 크기를 확장**하는 것을 의미한다.
- EC2 인스턴스 t2.micro → t2.large
- Use Case:
    - 데이터베이스 (RDS, ElasticCache)
    - 분산되지 않은 시스템에서 사용
- 확장할 수 있는 정도에 한계가 있다. (하드웨어 제한이 있다.)
- **EC 관점 : 인스턴스 크기 늘리기**

### Horizontal Scalability

- **인스턴스나 시스템의 수를 늘리는 방법**이다.
- 분배 시스템이 있다는 것을 의미한다.
    - 웹이나 현대적 애플리케이션은 대부분 분배 시스템으로 이루어져 있다.
- 클라우드 덕에 수평적 확장이 수월해지고 있다.
- **EC 관점 :  인스턴스 수 늘리기 (Scale out / Scale in)**
    - **Auto Scaling Group**
    - **Load Balancer**

### High Availability

- 애플리케이션 또는 시스템을 적어도 둘 이상의 AWS AZ나 데이터 센터에서 가동 중을 의미한다.
- 고가용성의 목표는 데이터 센터의 손실에서 살아남는 것이다.
- 센터 하나가 멈춰도 애플리케이션은 계속 작동이 가능하게 하는 것이다.
- 고가용성도 수동적일 수 있다. (RDS 다중 AZ를 갖추고 있다면 수동형 고가용성을 확보한 것이다. )
- 고가용성은 능동적일 수 있다. (수평적 확장 경우)
- **EC 2 관점 : 동일 애플리케이션 다른 AZ에서 가동 중**
    - **다중 AZ Auto Scaling Group**
    - **다중 AZ Load Balancer**

---

## ELB (Elastic Load Balancing)

- 트래픽을 여러 개의 서버(eg. EC2 인스턴스)로 보내는 것 을 말한다.
- 인스턴스 앞에 ELB가 있고, 뒤에는 서버 세트가 있다.

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/0ae6b501-b0ad-4e31-b8f4-f53861d10050/Untitled.png)

- 세 명의 유저가 ELB로 연결되면, 각 유저의 로드가 서버 세트에 있는 인스턴스 중 하나로 연결된다.
- 많은 유저가 연결될 수록 EC2 인스턴스로 가는 부하가 분산된다.

- 각 유저는 자신이 어떤 백엔드 서버와 연결되어 있는지 모른다.
- 각 유저는 로드밸런서에 대한 하나의 엔드포인트와만 연결되어 있다.

### Load Balancer가 필요한 이유

- **부하를 다수의 인스턴스로 분산**하기 위해서이다.
- 애플리케이션에 단일 액세스 지점(DNS)을 노출한다.
- 로드 밸런서 health check 기능을 통해 장애를 원활하게 처리할 수 있다.
- SSL Termination을 제공하기 때문 웹 사이트에 암호화된 HTTPS 트래픽을 가질 수 있다.
    
    (SSL Termination : SSL로 암호화된 데이터 트래픽이 해독되는 프로세스)
    
- 쿠키로 고정 정도를 강화할 수 있다.
- 여러 영역에 걸쳐 고가용성을 가진다.
- 클라우드 내에서 Private 트래픽과 Public 트래픽을 분리할 수 있다.

### Elastic Load Balancer

- AWS가 관리하는 관리형 로드 밸런서이다.
- AWS가 업그레이드와 유지 관리 및 고가용성을 책임진다.
- 로드 밸런서 작동 방식을 수정할 수 있게 일부 구성 knobs도 제공한다.
- ELB를 사용하는 것은 매우 좋다.
    - 자체 로드 밸런서를 마련하는 것보다 저렴하다.
- 다수의 AWS 서비스와 통합되어 있다.
    - EC2 인스턴스, ASG, ECS, ACM, CloudWatch, …

### Health Checks

- ELB가 EC2 인스턴스의 작동이 올바르게 되고 있는지 여부를 확인한다.
    - 만약, 제대로 작동이 안되고 있으면 해당 인스턴스로 트래픽을 보낼 수 없다.
- Health Check는 포트와 라우터에서 이루어진다.

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/6a204d99-39c2-4209-a797-ad9bdc3b3ca2/Untitled.png)

- EC2 인스턴스가 괜찮다는 신호인 200(OK)로 응답하지 않으면, 인스턴스 상태가 좋지 않다고 기록된다.
- ELB는 그쪽으로 트래픽을 더이상 보내지 않는다.

### Type of Load Balancer on AWS (4가지)

- **Classic Load Balancer - CLB (2009)**
    - HTTP, HTTPS, TCP, SSL와 secure TCP를 지원한다.
    - 해당 로드밸런서는 AWS에서 지원하지 않는. (콘솔에도 없음)
- **Application Load Balancer (v2 - 최신) - ALB (2016)**
    - HTTP, HTTPS, WebSocket 프로토콜을 지원한다.
- **Network Load Balancer (v2 - 최신) - NLB (2017)**
    - TCP, TLS, secure TCP, UDP 프로토콜 지원한다.
- **Gateway Load Balancer - GWLB (2020)**
    - 네트워크층(3계층)에서 작동한다.
    - IP 프로토콜을 지원한다.

일부 로드 밸런서는 내부에서 설정될 수 있다. 따라서 네트워크에 개인적 접근이 가능하다.

웹 사이트와 공공 애플리케이션 모두에 사용 가능한 외부 공공 로드밸런서도 있다.

### Load Balancer Security Groups

- 유저는 HTTP 혹은 HTTPS를 사용해 어디서든 로드밸런서에 접근이 가능하다.

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/f2eef7df-1ab9-4381-88c8-c3df65b054f4/Untitled.png)

- 로드 밸런서의 SG 인바운드 규칙은 다음과 같다. (유저 → 로드 밸런서)

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/cc0881c2-f7dc-469d-8d7a-cecc494511bb/Untitled.png)

- EC2 인스턴스는 다음과 같이 인바운드 규칙을 설정할 수 있다. (로드밸런서 → EC2 인스턴스)
    - 로드 밸런서 SG와 연결해야 한다.

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/b213ce95-01f5-4a8e-83cb-ff65978583ab/Untitled.png)

---

## Application Load Balancer (v2)

- **7계층(HTTP) 전용 로드 밸런서**
- 머신 간에 다수 **HTTP 애플리케이션**의 라우팅에 사용된다.
    - 이러한 머신들은 대상 그룹(Target group)으로 묶이게 된다.
- 동일 EC2 인스턴스 상의 여러 애플리케이션에 부하를 분산한다. (ex. 컨테이너)
- **HTTP/2와 WebSocket**을 지원한다.
- **리다이렉트**를 지원한다. (HTTP → HTTPS로 트래픽 자동 리다이렉트)
- **경로 라우팅**을 지원한다.
    - **URL의 경로에 기반한 라우팅**이 가능하다.
    - **URL의 호스트 이름에 기반한 라우팅**이 가능하다.
    - **쿼리 문자열과 헤더에 기반한 라우팅**도 가능하다.
    
    위에 따라 다른 URL에 따라 다른 대상 그룹으로 라우팅이 가능하다.
    
- ALB는 **마이크로 서비스나 컨테이너 기반 애플리케이션**에 가장 좋은 로드 밸런서이다. (ex. Docker & Amazon ECS)
- **포트 매핑 기능**이 있기 때문에 ECS 인스턴스의 동적 포트로 리다이렉션이 가능하다.
- CLB는 애플리케이션 하나에 CLB 하나가 필요하면, ALB는 다수의 애플리케이션을 ALB 하나만으로 처리할 수 있다.

### Application Load Balancer (v2) HTTP Based Traffic

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/9b2712f8-b75a-4f99-b251-8b30657eb148/Untitled.png)

- 외부 애플리케이션 로드 밸런서가 있다.
- 애플리케이션 뒤에는 두 개의 대상 그룹이 있다.
    - 첫번재 대상 그룹은 /user로 라우팅된다.
    - 두번째 대상 그룹은 /search로 라우팅된다.
    
    두 개의 독립된 마이크로 서비스가 서로 다른 작업을 처리한다.
    

### Target Groups

- EC2 인스턴스 (ASG로 관리될 수 있다) - HTTP
- ECS 작업 - HTTP
- Lamda Function
- IP 주소 (private)

- ALB는 여러 대상 그룹으로 라우팅할 수 있다.
- Health Check는 그룹 레벨에서 이루어진다.

### Application Load Balancer (v2) - Query Strings/Parameters Routing

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/79a74631-6f02-47fa-a226-922f5094af82/Untitled.png)

- 두개의 대상 그룹이 있다.
    - EC2 기반 대상 그룹
    - 데이터 센터 내부 온프레미스 개인 서버 기반 대상 그
        - 대상 그룹을 생성하기 위해서는 각 서버의 private IP가 대상 그룹에 지정되어야 한다.
- ALB를 통해 요청을 처리하고자 한다.
    - ALB에서 리다이렉션 라우팅 규칙을 만든다.
    - 쿼리 문자열을 기반으로 ALB가 각 대상 그룹에 리다이렉팅해준다.

### Application Load Balancer (v2) Good to Know

- ALB를 사용할 경우, 고정 호스트 이름이 부여된다.
- 애플리케이션 서버는 클라이언트의 IP를 직접 보지 못하며, **클라이언트의 실제 IP는 X-Forwarded-For라고 불리는 헤더에 삽입**된다.
- X-Forwarded-Port를 사용하는 포트와 X-Forwared-Proto에 의해 사용되는 프로토콜도 얻게 된다.

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/e0097c55-ca39-4d47-8313-0689a9537bb9/Untitled.png)

- 클라이언트 IP가 ALB와 직접 통신하면, 연결 종료라는 기능을 수행한다.
- ALB가 EC2 인스턴스와 통신할 때에는 사설 IP인 로드 밸런서 IP를 사용해 EC2 인스턴스와 통신하게 된다.
- EC2 인스턴스가 클라이언트 IP를 알기 위해서는 HTTP 요청에 있는 추가 헤더인 X-Forwarded-Port와 Proto를 확인해야 한다.

---

## Network Load Balancer (v2)

- L4 로드 밸런서
- **TCP와 UDP 트래픽**을 다룬다.
- NLB의 성능은 매우 높다.
    - 초당 수백만 건 요청 처리
    - 지연 시간도 짧다.
- 가용 영역별로 하나의 고정 IP를 갖는다. 그리고 탄력적 IP 주소를 각 가용 영역에 할당할 수 있다.
- **여러 개의 고정 IP를 가진 애플리케이션을 노출할 때 유용**하다.
- 시험 키워드 : 고성능, TCP/UDP, 정적 IP = 네트워크 로드 밸런서

### Network Load Balancer (v2) TCP (Layer4) Based Traffic

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/37dd8c8f-87db-4632-9688-d1761d20fabe/Untitled.png)

- 대상 그룹이 생성되면, NLB가 대상 그룹을 리다이렉트한다.

### Target Groups

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/4708d3a7-4a1e-4eb3-9b29-9815fe6daed7/Untitled.png)

- EC2 인스턴스
    - 네트워크 로드 밸런서가 TCP 또는 UDP 트래픽을 EC2 인스턴스로 리다이렉트할 수 있다.
- IP 주소 (private IP)
    - 반드시 하드코딩 되어야 한다.
    - 자체 데이터센터 서버의 프라이빗 IP를 사용할 수 있기 때문이다.
- ALB 앞에 NLB를 사용할 수 있다.
    - 네트워크 로드 밸런서 덕분에 고정 IP 주소를 얻을 수 있다.
    - 애플리케이션 로드 밸런서 덕분에 HTTP 유형의 트래픽을 처리하는 규칙을 얻을 수 있다.
    - 네트워크 로드 밸런서 대상 그룹이 수행하는 Health Check이다. TCP 프로토콜, HTTP 프로토콜, HTTPS 프로토콜을 지원한다.

---

## Gateway Load Balancer (GWLB)

- 네트워크 레벨(L3) 수준 로드 밸런서, IP 패킷 네트워크
- 기능 2가지
    - 투명 네트워크 게이트웨이 : VPC의 모든 트래픽이 GWLB 단일 엔트리와 출구를 통과한다.
    - 로드 밸런서 : 가상 어플라이언스 집합의 대상 그룹에 트래픽을 분산한다.
- 네트워크의 모든 트래픽이 **방화벽을 통과**하게 하거나 **침입 탐지 및 방지 시스템에 사용**한다.

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/428b2929-df71-405b-9be8-82a10ad6af53/Untitled.png)

- 유저가 ALB를 통해 EC2 인스턴스를 접근하기 전에 트래픽을 검사하고자 한다.
- 트랙픽 애플리케이션에 도달하기 전에 트래픽을 통과하려면 EC2 인스턴스와 같은 타사 가상 어플라이언스를 배포했다. → 이것을 GWLB를 통해 간단하게 해결할 수 있다.
- GWLB를 사용하면, VPC에서 라우팅 테이블이 업데이트 된다. 라우팅 테이블이 수정되면 모든 사용자 트래픽은 GWLB를 통과한다.
- GWLB는 가상 어플라이언스의 대상 그룹 전반으로 트래픽을 확산한다.
- 모든 트래픽은 어플라이언스테 도달하고, 어플라이언스는 트래픽을 분석하고 처리한다. (방화벽 및 침입탐지)
- 이상이 없으면 다시 GWLB로 보내고, 이상이 있으면 트래픽을 드롭한다.
- GWLB는 트래픽을 애플리케이션으로 보낸다.

- GWLB는 네트워크 트래픽을 분석하는 것이다.
- **6081번 포트의 GENEVE 프로토콜**을 사용한다.

### Target Groups

가상 어플라이언스를 위한 대상 그룹

- EC2 인스턴스, (인스턴스 ID 혹은 인스턴스 IP 주소로 등록)
- IP 주소 (private IP)

---

## Sticky Sessions (Session Affinity)

- 고정성 혹은 고정 세션을 실행하는 것은 클라이언트가 로드 밸런서에 2번의 요청에 대한 응답을 모두 같은 인스턴스가 처리하는 것이다.

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/9ef3e02c-7a23-4680-a6b9-82e332019008/Untitled.png)

- 이는 요청 분산과는 다른 동작 방식이다.
- 이 동작은 CLB와 ALB에서 설정할 수 있다.
- 요청의 일부로 함께 전달되는 쿠키에 의해 사용된다.
- 고정성은 만료 기간이 있다. 즉, **쿠키가 만료**되면 다른 EC2 인스턴스로 리다이렉션 할 수 있다.
- Use Case
    - 사용자 로그인
    - 중요한 정보를 취하는 세션 데이터를 잃지 않기 위해서 사용된다.
- 해당 방법은, EC2 인스턴스에 부하 불균형을 초래할 수 있다.
- 일부 인스턴스는 고정 사용자를 가질 수 있다.

### Cookies

2가지 유형 쿠키

쿠키에는 특정 이름이 있다.

- **애플리케이션 기반 쿠키**
    - 사용자 정의 쿠키
        - 애플리케이션에서 생성된다.
        - 애플리케이션에서 필요한 모든 사용자 정의 속성을 포함할 수 있다.
        - 쿠키 이름은 각 대상 그룹별로 개별적으로 지정해야 한다.
        - 다음과 같은 이름을 사용하면 안된다.
            
            (예약어 - AWSALB, AWSALBAPP, AWSALBTG..)
            
    - 애플리케이션 쿠키
        - 로드 밸런서 자체에서 생성된다.
        - ALB의 쿠키 이름은 AWSALBAPP이다.
    - 애플리케이션에서 기간을 설정할 수 있다.
- **기간 기반 쿠키**
    - 로드 밸런서에서 생성되는 쿠키
    - ALB - AWSALB, CLB - AWSELB
    - 특정 기간을 기반으로 만료되며, 그 기간은 로드 밸런서 자체에서 생성된다.

---

## Cross-Zone Load Balancing

- 교차 영역 로드 밸런싱을 사용하면, 각각의 로드 밸런서 인스턴스가 모든 가용 영역에 등록된 모든 인스턴스에 부하를 고르게 분배한다.
- 교차 영역 로드 밸런싱을 사용하면, 모든 영역에 있는 EC2 인스턴스에 트래픽이 고르게 분배된다.
- 교차 영역 로드 밸런싱을 사용하지 않고, 각 가용 영역마다 인스턴스 개수가 다르다면, 특정 영역에 있는 EC2 인스턴스에게 좀 더 많은 트래픽이 할당된다.

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/a62b49a7-29b5-4d56-a342-ce51fbab3db3/Untitled.png)

- **ALB**
    - **교차 영역 로드 밸런싱이 기본적으로 활성화**되어 있다. (대상 그룹을 설정해서 비활성화 할 수 있다.)
    - 데이터를 다른 가용 영역에 옮기는 데 비용이 들지 않는다.
- **NLB & GWLB**
    - 기본적으로 비활성화되어 있다.
    - **활성화하려면 비용을 지불**해야 한다.
    - AZ를 옮기려면 비용이 든다.
- **CLB**
    - 기본적으로 비활성화되어 있다.
    - 활성화시켜도 AZ간 데이터 이동에 비용이 들지 않는다.

---

## SSL/TLS

- SSL 인증서는 클라이언트와 로드 밸런서 사이에서 트래픽이 이동하는 동안 암호화 해준다. (전송 중 암호화 (in-flight encryption))
- 데이터는 네트워크를 이동하는 중에 암호화되고, 송수신자 측에서만 이를 복호화할 수 있다.
- SSL(Secure Socket Layer)은 연결을 암호화하는데 사용된다.
- TLS(Transport Layer Secure)는 새로운 버전의 SSL이다.
- 최근에는 TLS 인증서가 주로 사용되는데, 여전히 이를 SSL이라고 많이 부른다.

- Public 인증서는 인증기관(CA)에서 발급한다.
    - 인증 기관에는 Comodo, Symantec, GoDaddy, 등이 있다.
- Public 인증서를 로드 밸런서에 추가하면, 클라이언트와 로드 밸런서 사이의 연결을 암호화할 수 있다.
- SSL 인증서에는 만료 날짜가 있기 때문에 주기적으로 갱신하여 인증 상태를 유지해야 한다.

### Load Balancer - SSL 인증서

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/583221e1-9420-41e4-b968-2c2b4945f4f9/Untitled.png)

- 먼저 사용자가 HTTPS를 통해 로드밸런서에 접속한다.
    - S가 붙은 것은 SSL 인증서를 사용해서 암호화에 안전하다는 의미!
- 로드 밸런서는 내부적으로  SSL Termination을 수행한다.
- 백엔드에서는 암호화되지 않은 상태인 HTTP로 통신한다. (private VPC를 사용하기 때문에 안전하다)
- 로드 밸런서는 X.509 인증서를 사용하는데, 이것을 SSL/TLS 서버 인증이라고 한다.
- AWS에는 이러한 인증서를 관리하는 ACM(AWS Certificate Manager)이 있다.
    - 우리가 가진 인증서를 ACM에 등록할 수 있다.
- HTTP 리스너를 반드시 HTTPS 리스너로 해야한다.
    - 기본 인증서를 지정해야 한다.
    - 다중 도메인을 위해 다른 인증서를 추가할 수도 있다.
    - 클라이언트는 SNI(Server Name Indication)을 사용해서 접속할 호스트의 이름을 알릴 수 있다.

### SNI (Server Name Indication)

- 여러개의 SSL 인증서를 하나의 웹 서버에 로드해 하나의 웹 서버가 여러 개의 웹 사이트를 지원할 수 있게 해준다.
- 확장된 프로토콜로, 클라이언트가 SSL handshake 최종 단계에서 대상 서버의 호스트 이름을 지정하도록 한다.
- 클라이언트가 접속할 웹 사이트를 말했을 때, 서버는 어떤 인증서를 로드해야 하는지 알 수 있다.
- 모든 클라이언트가 지원하지는 않는다.

- ALB와 NLB, CloundFront 에서만 동작한다.
- CLB에서는 동작하지 않는다.
- 로드 밸런서에 SSL 인증서가 여러 개 있다면, ALB나 NLB 둘 중 하나이다.

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/574117b6-62f3-4264-b0a8-23938d73cade/Untitled.png)

- 각 대상 그룹에 해당하는 인증서를 ALB가 가지고 있다.
- 클라이언트가 도메인에 접속하고 싶다고 요청을 보내면, ALB는 도메인과 연결된 SSL 인증서를 로드한다.
- SNI를 사용해서 다중 SSL을 지정해 여러 개의 대상 그룹과 웹 사이트를 지원할 수 있다.

---

## Connection Draining

CLB 사용 경우 - Connection Draining

ALB & NLB 사용 경우 → Deregistration Delay (등록 취소 지연)

- 인스턴스가 등록 취소, 혹은 비정상 상태에 있을 때, 인스턴스에 어느 정도 시간을 주어서 활성 요청을 완료할 수 있도록 하는 기능이다.
- 인스턴스가 드레이닝되면 ELB는 등록 취소 중인 EC2 인스턴스로 새로운 요청을 보내지 않는다.

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/38a82620-55ab-40ec-97a7-2d3864ea46cc/Untitled.png)

- 어느 한 인스턴스가 드레이닝 모드일 때, 이미 해당 인스턴스에 연결된 유저들은 충분한 드레이닝 시간을 얻는다.
- 따라서 충분히 기존 연결 및 기존 요청을 완료할 수 있다.
- 이후 모든 작업이 끝나면 모든 연결이 정지된다.
- 새로운 연결이 들어오면 드레이닝 모드 인스턴스를 제외한 다른 인스턴스로 요청이 간다.
- 연결 드레이닝 파라미터는 매개변수로 표시할 수 있다. (1초 ~ 3600초 사이의 값으로 설정할 수 있다), 기본은 300초(5분)이다.
- 값을 0으로 설정하면 전부 다 비활성화 할 수 있다.
- 짧은 요청의 경우 드레이닝 값을 낮은 값으로 설정하면 좋다.

---

## Auto Scaling Group (ASG)

- 웹 사이트나 애플리케이션을 배포할 때 웹 사이트 방문자가 많아지면서 로드가 바뀔 수 있다.
- AWS에서 EC2 인스턴스 생성 API 호출을 통해 서버를 빠르게 생성하고 종료할 수 있다.
- 이를 자동화하고 싶으면 ASG를 생성하면 된다.

- **ASG의 목표는 스케일 아웃 (증가한 로드에 맞춰 EC2 인스턴스 추가), 스케일 인 (감소한 로드에 맞춰 EC2 인스턴스 제거) 하는 것이다.**
- ASG에서 실행되는 EC2 인스턴스의 최소 및 최대 개수를 보장하기 위해 매개변수를 전반적으로 정의할 수 있다.
- ASG는 로드 밸런서와 페어링하는 경우 ASG에 속한 모든 EC2 인스턴스가 로드 밸런서에 연결된다.
- 한 인스턴스가 비정상 종료하면 이를 대체할 새로운 인스턴스를 생성한다.

- ASG는 무료이며, 생성된 하위 리소스에 대한 비용만 지불하면 된다.

### Auto Scaling Group

- 최소 용량 (ASG 내 인스턴스 최소 개수 설정)
- 희망 용량 (ASG 내 인스턴스 희망 개수 설정)
- 최대 용량 (ASG 내 인스턴스 최대 개수 설정)

최대 용량 내에서 희망 용량을 더 높은 숫자로 설정하면 스케일 아웃 된다.

### Auto Scaling Group with Load Balancer

- ASG는 로드 밸런서와 작동한다.
- ASG와 연결된 인스턴스에 트래픽이 분산된다.
- EC2 인스턴스 상태를 확인하고 ASG로 전달할 수 있다.

### Auto Scaling Group Attribute

- 인스턴스 속성을 기반으로 ASG를 생성하려면 **시작 템플릿(Launch Template)**을 생성해야 한다.
- **시작 템플릿(Launch Template)에는 EC2 인스턴스를 시작하는 방법에 대한 정보가 포함**되어 있다.
    
    ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/37d4a84b-0268-42dd-9cb3-57baf9bddc9f/Untitled.png)
    
- ASG 최소/최대 용량, 초기 용량을 지정해야 한다.
- 스케일링 정책을 정의해야 한다.

### Auto Scaling - CloudWatch Alarms & Scaling

- CloudWatch 경보(Alarms) 기반으로 ASG를 스케일 인 및 아웃할 수 있다.
- ASG에서 트리거가 발생해 경보가 울리면, 스케일 아웃이 발생한다.
- 알람은 메트릭(지표)에 대한 경보가 울린다.
    - 평균 CPU나 원하는 사용자 지정 지표 등이 있을 수 있다.
- ASG 전체의 평균 CPU가 너무 높으면 EC2 인스턴스 추가가 필요한다. 지표에 따라 알람이 울리고, 경보가 ASG의 스케일링 활동을 유발할 수 있다.
- 경보에 의해 내부적으로 자동으로 스케일링이 이루어 진다.
- 경보를 기반으로 스케일 아웃 정책을 만들어 인스턴스 수를 늘리거나 줄일 수 있다.

---

## ASG - Dynamic Scaling Policies

- **대상 추적 스케일링 (Target Tracking Scaling)**
    - 가장 단순하고, 쉬운 설정
    - EC2 인스턴스에서 ASG의 평균 CPU 사용률을 추적해서 이 수치가 40%대에 머무를 수 있도록 할 때 사용한다.
    - **기본 기준선**을 새우고, 상시 사용이 가능하도록 한다.
- **단순/단계 스케일링 (Simple/Step Scaling)**
    - CloudWatch 경보를 설정하고, 그에 맞춰 스케일링
    - CPU 용량이 일정 시간 넘었을 때, 2 유닛 추가
    - CloudWatch 경보를 설정할 때는 **한 번에 추가할 유닛 수와 제거할 유닛 수를 단계별로 설정**해야 한다.
- **예약된 작업 (Scheduled Actions)**
    - **사용자 패턴을 바탕으로 스케일링 예상**하는 것이다.
    - 이벤트에 대비해 스케일링

### ASG - Predictive Scaling

- 예측 스케일링(Predictive Scaling)
    - ASG 내 오토 스케일링 서비스를 활용하여 지속적으로 예측 생성
    - 로드를 보고 다음 스케일링 예측한다.

### metrics to scale on

- CPU 사용률 (인스턴스 요청 → 연산 수행)
- RequestCountPerTarget
- Average Network In / Out (네트워크 병목 현상 파악)
- 사용자 정의 지표

### Scaling Cooldowns

- 스케일링 작업이 끝날 때마다 기본적으로 5분의 휴지 기간을 갖는다.
- 휴지 기간에는 ASG에 인스턴스 추가 또는 삭제가 불가능하다.
- 이는 새로운 인스턴스가 안정화될 수 있도록 하기 위해 사용된다.
- 즉시 사용 가능한 AMI를 사용해 시간을 단축하고, 요청을 신속하게 처리하는 것이 좋다.
