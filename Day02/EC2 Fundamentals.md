# EC2 Fundamentals

### AWS Budget

- IAM User로 예산 대시보드를 보기 위해서는 Root 계정에서 허용해주어야 한다.
- Charge by Service를 클릭해서 서비스별 요금을 볼 수 있다.
- AWS Budget을 생성해서 예산을 책정할 수 있다.
    - limit을 초과하면 알람을 받을 수 있다.
    - 프리티어를 탐색하기 위해서는 zero spend budget을 만들어서 확인하면 된다.

### EC2 Section

- **Amazon EC2는 무엇인가?**
    - **Elastic Compute Cloud = IaaS**
    - EC2는 AWS에서 제공하는 가장 인기있는 제품이다.
    - EC2는 높은 수준의 많은 것들을 포함하고 있다.
        - 가상 머신 임대 (EC2 인스턴)
        - 가상 드라이브에 데이터 저장 (EBS)
        - 여러 머신에 로드를 분산할 수 있음 (ELB)
        - 서비스 확장 (ASG)

클라우드의 본질은 필요할 때마다 온디멘드로 컴퓨터를 대여하는 것이기 때문에 EC2는 매우 중요하다.

- **EC2 인스턴스에서 사용자는 무엇을 선택할 수 있나?**
    - Operating System(OS) : Linux, Windows, Mac OS
    - 원하는 컴퓨팅 성능과 코어 개수 (CPU)
    - Random Access Memory(RAM)의 양
    - 저장 공간(Storage)
        - NAS (Network Attached) - EBS, EFS
        - DAS (Direct Attached) - EC2 Instance Store
        - SAN (Storage Area Network)
    - Network Card : EC2 인스턴스에 연결할 네트워크 유형
    - Security Group : 방화벽 규칙
    - Bootstrap Script (Configure at first launch) : EC2 User Data

- **EC2 User Data란?**
    - 서버가 처음 실작될 때, 한 번만 실행되는 스크립트
    - 부팅 작업을 자동화 하는 것 → 업데이트 설치 / 소프트웨어 설치 / 공통 파일 다운로드
    - EC2 User Data는 root 사용자로 실행된다.

- **EC2 인스턴스 타입**
    - t2 micro : 1vCPU, 1GiB 메모리, EBS 전용 스토리지, 네트워크 성능 낮음
    - 같은 t2 군집에서 성능을 높이면 t2.xlarge로 이동할 수 있다. t2.xlarge는 vCPU 4개, 16GiB 메모리, 중간 수준의 네트워크 성능을 얻을 수 있다.
    - 다른 레벨의 타입을 사용할 수록 성능이 매우 좋아진다.
    - 따라서, 애플리케이션 필요에 따라 선택해서 적절한 인스턴스를 사용할 수 있다.
    - 프리티어 기준 t2.micro를 월 최대 750시간 사용할 수 있다.

### Launching an EC2 Instance running Linux

- **AWS 콘솔에서 사용되는 가상 머신을 라운칭 해보자.**
    1. Instance 이름을 지정한다.
    2. Base image를 선택한다.
        
        base image는 인스턴스 위에서 사용되는 OS 이미지이다. 여기서 AMI로 직접 커스텀 이미지를 만들어서 사용할 수 있다.
        
    3. Instance Type을 선택한다.
        1. 가장 기본적으로 t2.micro는 프리티어가 사용 가능하다.
    4. Instance에 로그인하기 위한 키 페어를 생성한다.
        1. SSH 유틸리티를 이용해 인스턴스에 접근하기 위해서는 필요하다.
        2. 따라서, 키 페어를 생성해야 한다.
            1. 키 페어 이름을 지정한다.
            2. 키 페어 타입을 지정한다.
            3. 키 페어 포맷을 정한다.
                1. .pem : Linux, MacOS, Windows10
                2. .ppk : Window10 이하
    5. 네트워크 세팅을 한다.
        1. 인스턴스는 Public IP가 Enable되어 있다.
        2. 트래픽을 조절하기 위해 Security Group을 붙여주어야 한다.
            - rule을 생성해야 한다.
                
                모든 IP에서 SSH 트래픽을 허용
                
                모든 IP에서 HTTP 트래픽을 허용
                
    6. 스토리지 구성을 한다.
        1. EBS 볼륨 스토리지 구성을 한다. → gp2
    7. Advanced details
        1. User Data를 설정한다.
            
            첫 번째 시작시 한 번 실행되는 스크립트이다.
            
        
        ```bash
        yum update -y
        yum install -y httpd
        systemctl start httpd
        systemctl enable httpd
        echo "<h1>Hello World from $(hostnmae -f)</h1>" > /var/www/html/index.html
        ```
        

- **인스턴스 접속**
    - Launch를 하고 10초 정도 기다리면 인스턴스가 Running State로 바껴있는 것을 볼 수 있다.
    - Public IP를 통해 접속을 해보면 접속 가능한 것을 확인할 수 있다.
    - 여기서 주의할 점은, URL 앞에 HTTP를 꼭 명시해주어야 한다.
        - 왜? SG를 내가 HTTP와 SSH 프로토콜로 뚫어줬기 때문이다.
        
- **인스턴스 중지**
    
    Stop instance를 통해 인스턴스를 종료할 수 있다. Stop하면 돈이 빠져나가지 않는다.
    

- **인스턴스 제거**
    - 인스턴스를 종료할 수 있다.

✔️ 인스턴스를 중지시킨 후 다시 키면, 새로운 Public IP를 할당받는 것을 알 수 있다. (Private IP는 바뀌지 않음!)

### EC2 Instance Types

- **naming convetion**
    - m5.2xlarget
    - m : 인스턴스 클래스
    - 5 : 인스턴스 세대
    - 2xlarge : 인스턴스 클래스 사이즈
    

7개 타입의 EC2 인스턴스가 있다.

General Purpose, Compute Optimized, Memory  Optimized, Accelerated Computing, Storage  Optimized, Instance Features, Measuring Instance Performance 

- **General Purpose 인스턴스 타입**
    - 밸런스 좋은 Compute, Memory, Networking
        - Web Server
        - Code Repo
    - t2.micro가 여기에 속한다.
- **Compute Optimized 인스턴스 타입**
    - **계산 집약적인(Compute intensive) 테스크 수행**에 있어 높은 성능이 필요한 경우 좋다. (good CPU)
        - Batch 프로세싱 워크로드
        - 고성능 웹 서버
        - 미디어 트랜스코딩
        - High performance computing (HPC)
        - Scientific modeling & ML
        - dedicated gaming servers (전용 게임 서버)
    - C로 시작
- **Memory Optimized 인스턴스 타입**
    - **큰 데이터 세트**가 메모리에 있는 경우, 빠른 성능을 위한 워크로드에서 필요하다.
        - 고성능 관계형/비관계형 데이터베이스
        - 전용 웹 스케일 캐시 저장소 (ex. elastic cache)
        - BI(Business Intelligence)를 위한 인메모리 데이터베이스
        - 실시간 빅데이터 처리 애플리케이션
    - R로 시작함
- **Storage Optimized 인스턴스 타입**
    - 큰 데이터에 자주 접근이 있고, 읽고/쓰기가 빈번하게 일어나는 스토리지 집약 테스크 수행을 하는데 좋다.
        - 높은 빈도의 **온라인 거래 처리(OLTP)** 시스템
        - 관계형&NoSQL 데이터베이스
        - 인메모리 데이터베이스 캐시 (ex. Redis)
        - 데이터 웨어 하우스
        - 전용 파일 시스템
    - I, G, H로 시작

[instances.vantage.sh](http://instances.vantage.sh) 해당 웹사이트에서 AWS에서 이용 가능한 모든 인스턴스 타입을 찾을 수 있다.

### Security Groups

- Security Group은 AWS의 네트워크 보안의 기초이다.
- EC2 인스턴스에 들어오고 나가는 트래픽에 대해 통제한다.
- **allow 규칙만 포함**하고 있다.
- rule은 IP 주소 또는 다른 Security Group을 통해 작성될 수 있다.
- EC2에 대한 inbound, outbound 트래픽에 대한 규칙을 설정한다.

- **Security Group은 EC2의 방화벽 역할**을 한다.
    - 포트
    - IP 범위 (IPv4, IPv6)
    - inbound, outbound traffic network 통제
    
- **알면 좋은 것**
    - 두 개 이상의 인스턴스에 부착할 수 있다. 하나의 인스턴스는 여러개의 SG를 가질 수 있다.
    - SG는 하나의 리전/VPC에 제한된다.
    - EC2 밖에 방화벽 같이 존재한다.
    - SSH 접근을 위한 분리된 SG를 유지하는 것이 좋다.
    - time out이 발생해 EC2에 접근을 못하면, SG 문제이고, connection refuse와 같은 문제는 애플리케이션 자체 문제이다.
    - default로 모든 inbound traffic은 다 막혀있고, outbound traffic은 모두 허용이다.
- **Ports**
    - **22 = SSH**(Secure SHell) - Linux 인스턴스 로그인
    - **21 = FTP**(File Transfer Protocol) - 파일을 업로드하고 공유
    - **22 = SFTP**(Secure File Transfer Protocol) - SSH를 이용해 파일 업로
    - **80 = HTTP - unsecured 웹 사이트**
    - **443 = HTTPS** - secured 웹 사이트
    - **3389 = RDP**(Remote Desktop Protocol) - 윈도 인스턴스 로그

### SSH

- Mac, Linux, Windows v10 이상에서 사용 가능한 CLI 유틸리티
- Window v10 이하에서는 Putty를 사용해야 한다.
    - putty는 EC2 인스턴스에 접속하기 위해 SSH 프로토콜을 사용한다.
- 모든 운영체제에서 가능한, 웹 브라우저를 통해 EC2 Instance Connect를 사용하여 접속할 수 있다.

### EC2 Instances Purchasing Options

- **On-Demand Instances** : 초 단위로 사용한만큼 지불
    - Linux, Windows = 초당 요금
    - 다른 OS - 시간당 요금
    - 짧은 워크로드에 적합
    - 예측 가능한 가격을 얻는다.
- **Reserved(예약형)**
    - 기간 1년 및 3년 기간
    - On-demand에 비해 72프로 할인 제공(최대 할인)
    - 특정 인스턴스 속성을 예약
    - 선결제 제공
    - 긴 워크로드에 적합
    - 데이터베이스
    - 예약 인스턴스가 더이상 필요하지 않는 경우, 시장에 판매 가능하다.
- **Savings Plans(저축 플랜)**
    - 기간 1년 및 3년 기간
    - 특정 사용량을 달러로 약정한다.
    - 긴 워크로드에 적합
    - 특정 인스턴스 패밀리 및 지역에 고정
    - OS 전환 가능
- **Spot instances(스팟 인스턴스)**
    - 가장 공격적인 할인을 가지고 있다. (최대 90% 할인)
    - 매우 짧은 워크로드에 적합
    - 매우 저렴하다.
    - 언제 어디서든 인스턴스를 잃을 수 있어, 신뢰성이 떨어진다.
    - 가장 비용 효율적인 인스턴스
    - 배치 작업, 데이터 분석, 이미지 처리, 모든 종류의 분산 워크로드에 적합하고, 중요한 작업이나 데이터베이스에는 적합하지 않다.
- **Dedicated Host(전용 호스트)**
    - 실제 물리 서버를 얻음
    - 전체 물리적 서버 예약 가능
    - 인스턴스 배치를 제어한다.
    - 어떤 고객도 해당 하드웨어를 공유하지 않는다.
    - 초당 비용 지불
    - 1년 혹은 3년 동안 예약
    - AWS에서 가장 비싼 옵션
    - 자체 하드웨어에서 자체 인스턴스가 있다.
- **Capacity Reservations(용량 예약)**
    - 특정 AZ에서 모든 기간 동안 용량 예약 가능
    - 온데맨드 인스턴스 예약 가능하다.
    - 청구 할인이 없다.

### **Private vs Public IP (IPv4)**

- AWS도 IPv6를 지원한다.
- 대게는 IPv4를 많이 사용하고, IPv6는 IoT에 적합하다.
- 인터넷에서는 고유한 Public IP를 가지고 있다.
- Private IP는 개인 네트워크 대역에서 고유해야 한다. 같은 네트워크 대역에 있는 호스트끼리 통신할 수 있다.
- 따라서 개인 네트워크 대역에 있는 호스트는 NAT 장치와 인터넷 게이트웨를 통해 인터넷과 연결할 수 있다.
- **Elastic IP**
    - EC2 인스턴스를 시작할 때마다 Public IP가 바뀐다.
    - 따라서, 고정된 Public IP를 위해 Elastic IP를 지정할 수 있다.
    - 삭제하지 않은 한 사용자 소유이다.
    - 하나의 인스턴스에만 연결할 수 있다.
    - 계정당 5개까지 사용 가능하지만, 늘려달라고 문의 가능하다.
    - 하지만, 전박적으로 Elastic IP를 사용하는 것을 피하는게 좋다.
        - 임의의 Public IP를 사용하고, DNS를 할당해주는 것이 좋다. DNS는 route53으로 가는 것을 볼 수 있을 것이다.
        - 또는 Public IP 대신 Load Balancer를 사용하는 것이 좋다.
- EC2 인스턴스에는 임의의 Public IP와 고정된 Private IP가 할당된다.
- SSH를 이용할 땐, VPN이 없는 이상 Private IP 사용 불가하다.

### EC2 Placement Group

- AWS 인프라 내에서 EC2 인스턴스 위치 전략에 대해 통제하고 싶을 때 사용한다.
- AWS의 하드웨어와 직접적인 상호작용하지 않고, EC2 인스턴스를 원하는 방식을 AWS에 알린다.
- 다음과 같이 placement group을 만들 수 있다.
    - **Cluster** : 대기 시간 짧음, 성능은 높지만 리스크가 큼
        - 같은 가용영역, 같은 랙에 있다. (동일한 하드웨어에 인스턴스 여러개 존재)
        - 네트워크 성능 좋음, 하지만 장애 발생시 모든 인스턴스 동시 실패
        - 좋은 네트워크 → 빅데이터 작업, 짧은 대기 시간이 필요한 애플리케이션 (초고대역폭과 짧은 대기 시간이 필요한 경우 좋음)
    - **Spread** : 인스턴스가 다양한 하드웨어에 분산, 배치 당, 각 가용영역 당 max 7개 인스턴스, 중요한 애플리케이션 경우 사용
        - 여러개의 가용 영역에 최대 7개까지의 인스턴스 분산 저장(배치 그룹에 제한이 있음)
        - 리스크 최소화
        - 고가용성 극대화, 중요한 애플리케이션
    - **Partition** : 서로 다른 AZ 내의 하드웨어 랙 세트에 분산 → 실패에 격리되어 있지 않다. 확장 가능하다. Hadoop, Kafka와 같은 곳에 사용
        - 가용영역 당 7개의 파티션 사용 가능
        - 파티션은 동일한 region에 여러 AZ에 분산되어 있다.
        - 각 파티션에는 많은 EC2 인스턴스가 있음
        - 각 파티션은 AWS 랙을 가리킨다.
        - 랙 오류로 인한 위험에 안전한다.
        - 설정을 통해 수백개의 인스턴스를 얻을 수 있다.

### ENI

- Elastic Network Interfaces(ENI)
- VPC의 논리적 구성요소이다. **가상 네트워크 드**를 나타낸다.
- EC2 인스턴스에 네트워크 액세스 권한을 부여한다.
- EC2 인스턴스는 default로 eth0 ENI에 연결된다. private IP와 같은 네트워크 연결이 제공된다.
- ENI가 갖는 속성들
    - Public, Private IPv4, 보조 IP(가능)
    - 각 ENI는 private IPv4 당 elastic IP를 가질 수 있다.
    - 하나 이상의 security group 연결 가능
    - MAC 주소 첨부 가능
- EC2 인스턴스에 독립적을 ENI를 생성할 수 있고, 장애 조치를 위해 EC2 인스턴스에 즉석에서 attach가 가능하다.
- ENI는 특정 가용영역에서만 바인딩할 수 있다.
- **ENI를 옮김으로써 Private IP가 바뀔 수 있다. 이건 장애 조치에서 매우 유용하다.**
- 인스턴스를 종료하면 default ENI는 자동으로 없어진다.

### EC2 Hibernate (절전모드)

- 인스턴스
    - 중지 - 디스크(EBS)의 데이터는 유지된다.
    - 종료 - 디스크(EBS)의 데이터 또한 삭제된다.
- 인스턴스 다시 시작
    - OS가 부팅되고, EC2 User Data 스크립트가 실행된다
    - 애플리케이션이 시작되고 caches가 올라온다

**Hibernate를 사용해보자**

- 인스턴스를 최대 절전 모드로 전환하고, RAM 내 상태는 보존된다.
- 인스턴스 부팅이 진행 중이다.
- root EBS 볼륨 파일에 RAM 상태가 기록된다.
- 따라서 EBS는 RAM의 상태가 저장될 공간이 필요하다. 그리고 root 볼륨은 암호화해야 한다.

**만약, Hibernate(최대 절전 모드)가 실행되면**

- 정지 상태에서 인스턴스의 RAM은 EBS 볼륨에 덤프될 것이다.
- 인스턴스가 종료되면 RAM이 사라지지만 EBS 볼륨에는 RAM 상태가 보존되어 있다.
- 다시 인스턴스가 시작되면 RAM이 디스크에서 메모리에 로드된다.

**사용 사례**

- 장기 실행 프로세스를 원하는 경우
- RAM 상태를 저장하기 위해
- 빠른 부팅과 같이 재부팅만 하려는 경우

**특징**

- 다양한 family를 지원하고, RAM 크기는 150GB 미만이어야 한다.
- 다양한 운영체제에서 작동한다.
- 최대 절전 모드는 60일을 넘지 않아야 한다.
