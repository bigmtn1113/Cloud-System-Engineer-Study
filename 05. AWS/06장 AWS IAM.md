## IAM(Identity and Access Management)
AWS 리소스에 대한 액세스를 중앙에서 안전하게 제어할 수 있는 웹 서비스

리소스를 사용하도록 인증 및 권한이 부여된 대상을 제어하는 접근제어 기능(ACL, Access Control List)  
만들어진 Entity에 정책을 적용하여 AWS 리소스에 대한 접근 제어  
User, Group, Role, Policy를 생성 가능

- AWS 계정
  - 가입 시 생성되는 계정으로 ROOT 계정이라고 함

  - 모든 것을 다 수행할 수 있는 super user

  - 정보보안 감사 정책에 의거 ROOT 계정 사용 제한 권고

- IAM 계정
  - 일반 사용자 계정

  - 업무 별로 분리해서 사용

  - Administrator 권한을 부여받으면 관리자로 임명되며 운영에 사용

- 권고 방안
  - ROOT는 MFA 방식으로 제한 설정

  - Administrator 권한을 부여 받은 계정이 개발자 등의 계정을 만들고 권한 할당

  - 일반 계정은 최소 권한의 원칙에 의거해 필요한 권한만 부여

- User
  - 접근을 수행하는 account

- Group
  - 공통 역할을 하는 계정들의 집합

- Role
  - 생성된 Role은 사용자나 그룹에 할당하는 것이 아니라 EC2 같은 AWS 서비스 리소스에 할당

- Policy
  - 하나 또는 다수의 Permissions를 정의한 문서로 JSON 파일로 구성

  - Identity-based policies(자격증명 기반 정책): User, Group, Role에 할당하는 IAM 정책

  - Resource-based policies(리소스 기반 정책): S3 Bucket, EC2 등 AWS 서비스 자원에 할당하는 정책

- Permission
  - 어떤 리소스와 어떤 작업을 Allow/Deny할지를 결정
