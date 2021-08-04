# Docker 설치와 튜토리얼

---

## Docker 설치와 작동 확인
### Linux에 설치하기
Linux에 Docker를 설치하는 방법은 배포판이나 버전에 따라 절차가 다르다.

- **Ubuntu에 Docker 설치**  
  #### 1) apt 업데이트
    `sudo apt update`
  
  #### 2) https를 경유하여 repository를 사용할 수 있도록 패키지 설치  
    ```
    sudo apt -y install \
    > apt-transport-https \
    > ca-certificates \
    > curl \
    > software-properties-common
    ```
  
  #### 3) Docker의 공식 GPG(GNU Privacy Guard) 키 추가
    `sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -`
  
  #### 4) GPG 키 확인
    `sudo apt-key fingerprint 0EBFCD88`
  
  #### 5) repository 등록
    ```
    sudo add-apt-repository \
    "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
    $(lsb_release -cs) \
    stable"
    ```
  
  #### 6) repository 정보 추가 확인
    `cat /etc/apt/sources.list`
  
  #### 7) apt 업데이트
    `sudo apt update`
  
  #### 8) Docker 설치
    `sudo apt -y install docker-ce`
  
  #### 9) Docker 버전 확인
    `sudo docker version`

  #### ※ sudo 없이 docker 사용
    ```
    sudo usermod -aG docker 유저 이름
    sudo systemctl enable docker
    sydo systemctl restart docker
    sudo reboot
    ```

- **Cent OS에 Docker 설치**  
  #### 1) repository 등록
    ```
    sudo cd /etc/yum.repos.d
    sudo yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
    ```
  
  #### 2) repository 정보 추가 확인
    ```
    sudo ls /etc/yum.repos.d
    sudo yum repolist
    ```
  
  #### 3) Docker 설치
    `sudo yum -y install docker-ce && sudo yum -y update`
  
  #### 4) Docker 서비스 데몬 실행
    ```
    sudo mkdir -p /etc/systemd/system/docker.service.d
    sudo systemctl daemon-reload
    sudo systemctl enable --now docekr
    sudo systemctl start docker
    sudo systemctl status docker
    ```
  
  #### 5) Docker 버전 확인
    `sudo docker version`

<br/>

### Docker에서 'Hello World' 표시
`docker container run <Docker 이미지명> <실행할 명령>`

ex) `docker container run ubuntu:latest /bin/echo 'Hello World'`  
Ubuntu 이미지를 바탕으로 Docker 컨테이너를 작성 및 실행한 후, 작성한 컨테이너 안에서 "Hello Wolrd" 표시

#### ※ 로컬 캐시
docker container run ~~ 명령을 실행하면 Docker 컨테이너의 바탕이 되는 Docker 이미지가 로컬 환경에 있는지 확인한다.  
로컬 환경에 없다면 Docker repository에서 Docker 이미지를 다운로드한 후, Docker 컨테이너를 실행한다.  
만일 로컬 환경에 있다면 로컬 환경에 있는 Docker 이미지를 바탕으로 Docker 컨테이너를 실행한다.

로컬 환경에 이미지가 있으면 더 이상 다운로드를 하지 않으므로 처음보다 빠른 속도로 컨테이너가 시작된다.

<br/>

### Docker version 확인
`docker version`

<br/>

### Docker 실행 환경 확인
`docker system info`

<br/>

### Docker 디스크 이용 상황 확인
`docker system df`
