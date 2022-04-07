## Linux에서 CodeCommit 사용

### 사전 작업
1\. IAM User 생성  
2\. 생성한 User의 보안 자격 증명에서 **AWS CodeCommit에 대한 HTTPS Git 자격 증명** 생성

<br/>

### 과정
#### 1\. git 설치
```bash
yum install -y git
```

#### 2\. git config 설정
```bash
git config --global credential.helper '!aws codecommit credential-helper $@'
git config --global credential.Usehttppath true
git config --global user.name "유저 네임"
git config --global user.email "유저 이메일"
```

#### 3\. CodeCommit에 저장할 소스 코드들이 있는 디렉터리로 이동
```bash
cd <디렉터리 명>
```

#### 4\. 디렉터리와 CodeCommit 리포지터리 연결
```bash
git init
git remote add codecommit <리포지터리 HTTPS URL>
```

#### 5\. CodeCommit에 소스 코드들 업로드
```bash
git add .
git commit -m "커밋 메시지"
git push codecommit master          # Username과 Password는 사전 작업에서 생성한 자격 증명 내용 입력
```

<br/>

### ※ 참고
- git config는 HTTPS 방식으로 설정

- **git clone**
  ```bash
  git clone <리포지터리 HTTPS URL>
  ```
