# 7. Route53

## DNS란?

- Domain Name System
- 사람에게 친숙한 호스트 이름을 대상 서버 IP 주소로 번역해준다.
- URL과 호스트 이름 → IP로 변환
- 계층적 구조 이름이 있다.

### DNS 용어

- Domain Registrar : 도메인 이름을 등록하는 곳
    - Amazon Route 53, GoDaddy
- DNS Records : A, AAAA, CNAME, NS, …
- Zone File : DNS 레코드를 포함하는 파일 (호스트 이름과 IP 주소를 일치)
- Name Server : DNS 쿼리를 실제로 해결하는 서버
- Top Level Domain (TLD) : .com
- Second Level Domain (SLD) : goole.com
- FQDN (Fully Qualified Domin Name)

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/47bc9225-f9a7-4110-b139-60706a59c1f0/Untitled.png)

### DNS 동작 원리

- 웹 서버가 있다. (도메인 이름 example.com을 이용하고자 한다.)
- example.com은 DNS용 서버를 등록해야 한다.

- 클라이언트가 example.com에 접근하기 위해서는 로컬 DNS 서버에 질의를 한다.
    - 로컬 DNS 서버는 보통 ISP에서 할당되고 관리된다.
- 로컬 DNS 서버가 해당 질의에 대한 답을 못한다면, ICANN에 의해 관리되는 DNS 서버의 루트에게 물어본다.
- 루트 DNS 서버는 →.com 네임 서버의 IP 주소를 알려준다.
- 로컬 DNS 서버는 다시 .com 네임 서버에 쿼리 요청을 보내고, 네임 서버는 [example.com](http://example.com) 서버에 대한 공인 IP를 알려준다.
- 로컬 DNS 서버는 서브 도메인 DNS 서버(레지스트라 : Route53)로 쿼리를 날리고, 최종적으로 IP를 얻는다.

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/a8157a1a-e6cf-487e-b2e2-1dda88b4df5f/Untitled.png)

---

## Route53

- 53은 전통적인 DNS 포트!
- 고가용성, 확장성을 갖춘 완전 관리형 권한이 있는 DNS
    - 권한이 있다 = 고객이 DNS 레코드를 업데이트 가능하다.
    - DNS에 대한 완전 제어가 가능하다.
- Route53은 DNS 레지스트라로 레코드가 저장되어 있다.
- 클라이언트가 도메인 쿼리를 Route53에게 보내면, Route53이 매핑되는 IP주소를 반환하고, 클라이언트는 그 IP 주소를 기반으로 애플리케이션에 접근한다.

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/b58da739-2055-4ab7-b292-874369e93a4e/Untitled.png)

- Route53 리소스 관련 상태를 확인할 수 있다.
- 100% SLA 가용성을 제공하는 유일한 AWS 서비스이다.

### Route53 - Records

- 레코드를 정의하고, 특정 도메인으로 라우팅하는 방법을 정의한다.
- 각 레코드는 도메인에는 다음과 같은 항목이 있다.
    - Domain/subdomain Name
    - Record Type
    - Value
    - Routing Policy
    - TTL (DNS 레코드 캐시 시간)
- 레코드 종류에는 A, AAAA, CNAME, NS가 있다.

### Route53 - Record Types

- A : 호스트 이름과 IPv4 매핑
- AAAA : 호스트 이름과 IPv6 매핑
- CNAME : 호스트 이름을 다른 호스트 이름과 매핑
- NS : 호스팅 존의 이름 서버, 서버의 DNS 이름 또는 IP 주소로 호스팅 존에 대한 DNS 쿼리에 응답할 수 있다. (트래픽이 도메인으로 라우팅되는 방식을 제어한다)

### Route53 - Hosted Zones

- 레코드이 컨테이너
- 도메인과 서브도메인으로 가는 트래픽의 라우팅 방식을 정의한다.
- Public Hosted Zones와 Private Hosted Zones가 있다.
- Private Hosted Zones에는 VPC만이 URL을 리졸브할 수 있다.
- hosted zone 사용하면 1년에 최소 12달러 지불해야 한다.
