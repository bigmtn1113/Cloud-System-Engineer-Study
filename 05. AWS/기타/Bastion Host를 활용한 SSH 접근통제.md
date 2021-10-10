## Bastion Host를 활용한 SSH 접근통제

### 환경구성
- Public Subnet - Bastion Host
- Private Subnet - Web Instance

<br/>

### 과정
#### 1\) Bastion Host에 적용시킬 Security Group 생성
- 인바운드 규칙 - SSH / 22 / 접근허용 IP

#### 2\) Bastion Host EC2 생성
- 1)번 과정에서 만든 Security Group 적용
- Key 생성 및 적용

#### 3\) Web Instance에 적용시킬 Security Group 생성
- 인바운드 규칙 - SSH / 22 / 1)번 과정에서 만든 Security Group

#### 4\) Web Instance EC2 생성
- 3)번 과정에서 만든 Security Group 적용
- Key 생성 및 적용

#### 5\) Putty Pageant 설치 및 사용
- https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html 에서 다운로드
- Pageant 프로그램 실행 후, Web Instance 접속 키 등록
- Putty 설정(Connection - SSH - Auth - Authentification parameters - Allow agent forwarding) 셋팅

#### 6\) 접속 확인
- Bastion Host에서 Web Instance로 SSH 접근되는지 확인

<br/>

#### ※ Bastion Host와 Web Instance에 각각 다른 Key를 적용하는 이유
- 같은 Key로 EC2를 만들면 Bastion Host가 외부 위험에 노출될 시, Web Instance도 같이 노출

#### ※ Putty Pageant
- Bastion Host와 Web Instance가 같은 VPC 안에 있으면 내부 IP로 통신 가능
- Web Instance는 Bastion Host을 통해서만 SSH 접근 가능
- 그러나 Bastion Host가 Web Instance의 접속 Key를 갖고 있지 않아 SSH 접속 불가능
- Pageant로 Web Instance 접속 Key를 등록하면 Web Instance로 SSH 접속 가능
