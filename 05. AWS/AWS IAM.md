## AWS IAM(Identity and Access Management)
**AWS 리소스에 대한 액세스를 중앙에서 안전하게 제어할 수 있는 웹 서비스**

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

  - **Identity-based policies(자격증명 기반 정책):** User, Group, Role에 할당하는 IAM 정책

  - **Resource-based policies(리소스 기반 정책):** S3 Bucket, EC2 등 AWS 서비스 자원에 할당하는 정책

- Permission
  - 어떤 리소스와 어떤 작업을 Allow/Deny할지를 결정

<br/>

### Policy
#### AWS Managed Policies
AWS에서 만들어서 제공해주는 Policy

- AdministratorAccess 정책을 할당 받은 사용자는 모든 작업 수행 가능

#### Customer Managed Policies
사용자 생성 Policy

- 정책에서 Create Policy를 통해 직접 정책 생성

- 고객 관리 정책은 정책을 생성한 계정에서만 유효

- 정책 JSON 파일은 AWS에서 제공하는 Visual Editor를 사용하면 쉽게 생성 가능

#### JSON 파일
- **Version:** "2012-10-17" 또는 "2008-10-17" 선택

- **ID:** 선택 사항. Policy의 구분자로 UUID를 사용 권장

- **SID:** 선택 사항. Statement ID로 statement를 구분하기 위해 사용

- **Effect:** Allow, Deny 사용

- **Principal, NotPrincipal:** 대상(Users, Services, Roles) 지정

- **Action, NotAction:** 서비스의 API Calls 지정

- **Resource, NotResource:** Action이 영향을 미치는 리소스 리스트 지정

- **Condition:** 조건 지정. 조건을 충족하는 경우에만 해당 정책 적용

<br/>

### 권한 경계
**자격 증명 기반 정책을 통해 IAM 엔터티에 부여할 수 있는 최대 권한을 설정하는 고급 기능**

- 엔터티에 대한 권한 경계를 설정할 경우, 해당 엔터티는 자격 증명 기반 정책 및 관련 권한 경계 모두에서 허용되는 작업만 수행 가능

- 이미 명시적으로 허용된 넓은 범위의 권한을 특정 사용자 또는 그룹을 대상으로 허용 범위를 제약하는 방법으로 매우 효과적
