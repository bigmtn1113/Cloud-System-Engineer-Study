# Dockerfile을 사용한 코드에 의한 서버 구축

---

## Dockerfile을 사용한 구성 관리
### Dockerfile
**Docker 상에서 작동시킬 컨테이너의 구성 정보를 기술하기 위한 파일**  
텍스트 형식의 파일이며 확장자는 필요 없다.

#### 컨테이너 구성정보
- 베이스가 될 Docker 이미지
- Docker 컨테이너 안에서 수행한 명령
- 환경변수 등의 설정
- Docker 컨테이너 안에서 작동시켜둘 데몬 실행

<br/>

### Dockerfile의 기본 구문
#### 기본 서식
명령 인수

#### 주요 Dockerfile 명령
명령 | 설명
--- | ---
FROM | 베이스 이미지 지정
RUN | 명령 실행
CMD<br/>ENTRYPOINT | 컨테이너 실행 명령
EXPOSE | 포트 익스포트
ENV | 환경변수
ADD | 파일/디렉터리 추가
COPY | 파일 복사
VOLUME | 볼륨 마운트
WORKDIR | 작업 디렉터리

---

## Dockerfile의 빌드와 이미지 레이어
### Dockerfile로부터 Docker 이미지 만들기
`docker build [옵션] <Dockerfile 경로> | URL | -`

ex)  
```bash
mkdir sample && cd $_
vi Dockerfile
```
```dockerfile
# ------------Dockerfile 내용------------
FROM centos:7
# -----------------------------------------
```
```bash
docker build -t sample:1.0 .
```
centos:7 이미지가 로컬에 없으면 Docker repository에서 다운받고, 있으면 다운받지 않는다.

#### Dockerfile 파일명 다르게 사용해 build하기
ex) `docker build -t sample -f Dockerfile.base .`  
-f 옵션 사용

#### ※ 중간 이미지의 재이용
Docker는 이미지를 빌드할 때 자동으로 **중간 이미지**를 생성한다.  
그리고 다른 이미지를 빌드할 때 중간 이미지를 내부적으로 재이용함으로써 빌드를 고속으로 수행한다.  
이미지를 재이용 중일 땐 'Using cache'라고 표시된다.  
이 캐시를 이용하고 싶지 않을 경우엔 --no-cache 옵션을 지정하면 된다.

<br/>

### Docker 이미지의 레이어 구조
Dockerfile을 빌드하면 Dockerfile의 명령별로 이미지를 작성한다.  
이렇게 작성된 여러 개의 이미지는 레이어 구조로 되어 있다.  

#### Dockerfile 예제
```dockerfile
FROM ubuntu:latest

RUN apt-get update && apt-get -y install -q nginx

COPY index.html /usr/share/nginx/html/

CMD ["nginx", "-g", "daemon off;"]
```

위의 예제를 build하면 명령 줄마다 이미지가 만들어지고 겹치게 되어 4개의 레이어를 가진 이미지가 생성된다.

작성한 이미지는 다른 이미지와 공유되므로 디스크의 용량을 효율적으로 이용할 수 있다.

---

## 멀티스테이지 빌드를 사용한 애플리케이션 개발
애플리케이션 개발 시에 개발 환경에서 사용한 라이브러리나 개발 지원 툴이 제품 환경에서 반드시 사용되는 것은 아니다.  
제품 환경에는 애플리케이션을 실행하기 위해 최소한으로 필요한 실행 모듈만 배치하는 것이 컴퓨팅 리소스를 효율적으로 활용할 수 있다는 점에서나 보안 관점에서 볼 때 바람직하다.

### Dockerfile 만들기
```dockerfile
FROM golang:1.8.4-jessie AS builder

WORKDIR /go/src/github.com/asashiho/greet
RUN go get -d -v github.com/urfave/cli

COPY main.go .
RUN GOOS=linux go build -a -o greet .

# ------------------------------------
FROM busybox
WORKDIR /opt/greet/bin

COPY --from=builder /go/src/github.com/asashiho/greet/ .
ENTRYPOINT ["./greet"]
```

<br/>

### Docker 이미지의 빌드 
```bash
docker build -t greet .
```

빌드 로그를 확인해보면 먼저 개발 환경용 이미지 builder가 생성된다.  
이 builder 이미지로 소스코드를 build하여 실행 가능 바이너리 파일을 생성한다.  
그 다음 제품 환경용 이미지에 실행 가능 바이너리 파일이 복사된다.

`docker image ls`를 해서 생성된 이미지들 크기를 확인해보면 제품 환경용 이미지인 greet은 개발 환경용 이미지인 golang보다 훨씬 작다는 것을 알 수 있다
