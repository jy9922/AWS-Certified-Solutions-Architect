# 2. IAM 및 AWS CLI

### IAM

- **Identity and Access Management**
- 글로벌 서비스에 해당된다.
- 사용자를 생성하고 그룹에 배치해야 한다.
- 계정을 생성할 때 root 계정이 만들어진다.
    - root 계정은 default로 생성되는 것이다. root 계정은 모든 권한을 가지고 있다.
    - root 계정은 계정을 생성할 때만 사용되어야 한다. 이후 루트 계정은 사용해서도, 공유해서도 안 된다.
- 이후 IAM에서 사용자 계정을 생성해야 한다.
    - 하나의 사용자 = 조직 내의 한 사람
    - 필요시 이러한 사용자들을 하나의 그룹으로 묶을 수 있다.
- **그룹에는 사용자만 배치할 수 있다.** 다른 그룹을 포함할 수 없다.
    - 그룹이 없는 사용자가 있을 수 있다. (추천하지 않는 방법)
    - 한 사용자가 다수의 그룹에 속할 수 있다.

### IAM : Permissions

- AWS 계정을 사용하도록 허용하기 위해서이다.
- 허용을 위해서는 권한을 부여해야 한다.
- 사용자 또는 그룹에게 IAM 정책인 JSON 문서를 지정할 수 있다.
- 정책을 사용해 사용자들의 권한을 정의할 수 있다.
- AWS에서는 이러한 권한에 **최소 권한의 원칙**을 적용한다.
    - 사용자가 꼭 필요로 하는 것 이상의 권한을 주지 않는다.

### IAM Policy

- 그룹에 정책을 적용하면, 그룹에 속한 사용자들은 같은 정책의 상속이 가능하다.
- 그룹이 없는 사용자의 경우 **인라인 정책**을 생성해 적용하면 된다.

### IAM Policies Structure

- JSON 문서로 이루어져 있다.

```json
{
	"Version": "2012-10-17", // 보통은 2012-10-17이 정책 언어 버전이다.
	"Id": "정책을 식별하기 위한 ID" // Optional
	"Statement": [
		{
			"Sid": "문장 식별번호",
			**"Effect": "접근 Allow or Deny",
			"Prinicipal": {
				// 특정 정책이 적용될 계정, 사용자, role
			},
			"Actions": [
				// effect에 기반한 API 호출 목록
			],
			"Resource": [
				// Action이 적용되는 리소스 목록
			]**
			"Condition": [
				// 조건 (선택 사항)
			]
		}
	]
}
```

### IAM 보호

- 그룹과 사용자들의 정보가 침해당하지 않도록 보호하는 두가지 방법
    - 비밀번호 보호 정책
        - 계정의 비밀번호를 강력하게 해야 한다.
        - 비밀번호 최소 길이 설정, 특정 유형의 글자 요구(ex. 특수문자), IAM 사용자 비번 변경 허용 or 금지, 일정 기간 비번 만료
    - **다중 요소 인증(Multi Factor Authentication - MFA)**

### IAM MFA

- MFA 장치를 이용해서 IAM 계정을 보호할 수 있다.
- MFA는 **비밀번호 + 내가 소유한 보안 장치**를 함께 사용하는 것이다.
- MFA의 장점
    - 사용자가 해킹을 당해 비밀번호를 탈취 당해도 MFA에서 발급한 토큰을 몰라 해당 계정에 접근이 불가능하다.
- MFA 장치
    - Virtual MFA device - Google Authenticator, Authy
    - U2F Security Key - YubiKey (물리적 키)
    - Hardware Key Fob MFA Device
    - Hardware Key Fob MFA Device for AWS GovCloud

### AWS Access Key

- AWS에 액세스 하는 방법들
    - AWS Console
        - 아이디/비번 + MFA로 보호됨
    - AWS CLI
        - Access Key로 보호
    - AWS SDK
        - Access Key로 보호
    - AWS CloudShell
- Access Key는 어떻게 생성할 수 있을까?
    - AWS Management Console을 통해 생성 가능하다.
    - 사용자들이 소유한 Access Key를 관리한다.
- Access Key는 비밀번호가 아닌 암호와 같다. (공유 X)

### Creation of Access Key

- Access Key를 발급받으면 Access Key ID와 Secret Access Key가 주어진다.

### AWS CLI

- AWS CLI는 명령줄 shell에서 명령어를 사용하여 AWS 서비스와 상호작용할 수 있도록 해주는 도구이다.
- AWS CLI는 모든 명령이 aws로 시작한다.

### AWS SDK

- AWS Software Development Kit
- 특정 언어로 된 라이브러리 집합이다. 프로그래밍 언어에 따라 개별 SDK가 존재한다.
- 터미널에서 사용하는 것이 아닌, 코딩을 통해 애플리케이션 내에 심어두는 것이다.
- 따라서 애플리케이션 내에 자체적으로 AWS SDK가 있는 것이다.
- 다양한 프로그래밍 언어를 지원한다 : JavaScript, Python, PHP, Jaca, …
- Android, iOS, IoT 디바이스 모두 지원하고 있다.

### AWS Cloud Shell

- CloudShell은 AWS에서 제공하는 무료 터미널이다.
- CloudShell을 사용 가능한 특정 리전이 있다.
- CloudShell의 기본 리전은 내가 지금 로그인 중인 리전이다.
- 전체 저장소가 있다. 따라서 CloudShell을 종료 후에도 파일이 그대로 남아 있다.
- 파일을 생성하고 다운로드 혹은 업로드가 가능하다.

### IAM Role

- AWS 서비스에도 특정 권한이 필요하다.
- IAM Role은 실제 사용자가 아닌 AWS의 서비스에 부여되는 것이다.
- IAM Role은 AWS에서 특정 서비스가 잠깐 동안 인증을 받고 필요한 일을 할 수 있게 해준다.
- **예시**
    - EC2 인스턴스가 AWS에 어떤 정보에 접근하고자 할 때, IAM Role을 부여해주면 된다.
    - EC2 인스턴스 Role, Lambda Function Role, Role of CloudFormation…

### IAM Security Tools (Audit IAM)

- **IAM 자격 증명 보고서 (account level)**
    - 계정에 있는 사용자 다양한 자격 증명의 상태를 포함한다.
- **IAM Access Advisor (User level)**
    - 사용자에게 부여된 서비스의 권한과 해당 서비스에 마지막으로 액세스한 시간을 볼 수 있다.
    - 이를 통해 어떤 권한이 사용되지 않는지 알 수 있어, 사용자의 권한을 줄여 최소 권한 원칙을 지킬 수 있다.

### IAM Guidelines

- 루트 계정은 계정 설정 외에는 사용하지 않는다.
- 하나의 AWS 사용자 = 실제 한 명의 사용자
    - 자격 증명 공유 XXXX
- 사용자를 그룹에 포함시키자 → 그룹에 권한을 부여하자
- AWS 서비스에 IAM Role을 적용하자
- 비밀번호 정책은 강력하게 하자
- 비밀번호 + MFA를 이용해 보안을 더욱 강화하자
- CLI, SDK를 이용해 접근하려면 액세스 키를 발급 받아야 한다. (액세스 키 공유 XXXX)
- 계정 감사를 위해서는 **IAM 자격 증명 보고서(계정 수준)와 IAM 액세스 분석기(사용자 수준)**를 사용하면 된다.
