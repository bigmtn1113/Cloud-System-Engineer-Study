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

---

## 명령 및 데몬 실행
### RUN(명령 실행)

#### Shell 형식
```dockerfile
RUN 명령
```

명령의 지정을 쉘에서 실행하는 형식으로 기술하는 방법  
/bin/sh -c를 사용하여 명령을 실행한 것과 동일

ex)
```dockerfile
RUN apt-get -y install nginx
```

#### Exec 형식
```dockerfile
RUN ["실행 가능한 명령", "매개변수1", "매개변수2", ...]
```

쉘을 경우하지 않고 직접 실행하는 형식으로 기술하는 방법  
명령 인수에 환경변수를 지정 불가  
실행하고 싶은 명령을 JSON 배열로 지정

ex)
```dockerfile
FROM ubuntu:latest

RUN echo shell 형식
RUN ["echo", "Exec 형식"]
RUN ["/bin/bash", "-c", "echo 'Exec 형식에서 bash 사용'"]
```
build 후, `docker history 이미지명`을 통해 확인해보면  
1번째 RUN은 /bin/sh으로 실행,  
2번째 RUN은 쉘을 거치지 않고 실행,  
3번째 RUN은 /bin/bash으로 실행됨을 알 수 있다.

#### ※ RUN 시, 이미지의 레이어
Dockerfile을 빌드하면 명령마다 내부 이미지가 하나씩 작성되므로
```dockerfile
RUN yum -y install httpd
RUN yum -y install php
```
보다
```dockerfile
RUN yum -y install httpd php
```
이렇게 작성하는 것이 더 좋다.

<br/>

### CMD(데몬 실행)
#### Shell 형식
```dockerfile
CMD 명령
```

RUN 명령 구문과 동일

ex)
```dockerfile
CMD nginx -g 'daemon off;'
```

#### Exec 형식
```dockerfile
CMD ["실행 가능한 명령", "매개변수1", "매개변수2", ...]
```

RUN 명령 구문과 동일

ex)
```dockerfile
CMD ["nginx", "-g", "daemon off;"]
```

#### 컨테이너 실행
ex) `docker container run -d -p 80:80 cmd-sample`  
위의 CMD 예제를 포함한 Dockerfile을 build해서 만든 이미지가 cmd-sample이고,  
이 이미지로 컨테이너를 background로 실행시킨 것이다.  
컨테이너가 실행될 때 자동으로 CMD 명령이 실행되면서 nginx가 foreground로 실행된다.

<br/>

### ENTRYPOINT(데몬 실행)
#### Shell 형식
```dockerfile
ENTRYPOINT 명령
```

RUN 명령 구문과 동일

ex)
```dockerfile
ENTRYPOINT nginx -g 'daemon off;'
```

#### Exec 형식
```dockerfile
ENTRYPOINT ["실행 가능한 명령", "매개변수1", "매개변수2", ...]
```

RUN 명령 구문과 동일

ex)
```dockerfile
ENTRYPOINT ["nginx", "-g", "daemon off;"]
```

#### CMD 명령과의 차이
- CMD - docker container run 명령 실행 시에 인수로 새로운 명령을 지정하면 이것을 우선 실행
- ENTRYPOINT - ENTRYPOINT 명령으로는 실행하려는 명령을 지정하고 CMD 명령으로는 그 명령의 인수를 지정해 조합 가능

ex)
```dockerfile
FROM ubuntu:16.04

ENTRYPOINT ["top"]
CMD ["-d", "10"]
```
```bash
docker container run -it sample		# top -d 10 실행
docker container run -it sample -d 2		# top -d 2 실행. CMD 명령 덮어쓰기
```

<br/>

### ONBUILD(빌드 완료 후에 실행)
```dockerfile
ONBUILD 명령
```

Dockerfile로부터 생성한 이미지를 베이스 이미지로 한 다른 Dockerfile을 빌드할 때, 실행하고 싶은 명령을 기술

ex)  
Dockerfile.base
```dockerfile
FROM ubuntu:17.10

# 웹 서버 설치 및 설정
...

ONBUILD ADD site.tar /var/www/html/
```

```bash
docker build -t baseimage -f Dockerfile.base .		# 앱 실행 환경 구축 이미지(Infra)
```

Dockerfile
```dockerfile
FROM baseimage
...
```

```bash
docker build -t webimage .				# 개발한 프로그램을 전개한 이미지(App)
```

ONBUILD 명령으로 인해 Dockerfile을 빌드할 때 ADD 명령이 수행되므로, site.tar 파일은 Dockerfile과 같은 경로에 있어야 한다.

#### ※ ONBUILD 명령을 사용한 팀 개발
개발팀 안에 실행 환경 Dockerfile을 작성할 담당자를 정한 후, 그 담당자가 OS/미들웨어의 설치나 설정, 라이브러리 검증이나 도입 등을 하고 베이스가 되는 Dockerfile을 만든다.
웹 콘텐츠 개발자는 이 베이스가 되는 Dockerfile을 바탕으로 각자 개발한 소스코드를 전개해 테스트를 하면, 팀 멤버 전원이 똑같은 실행 환경에서 개발과 테스트를 진행할 수 있다.

<br/>

### STOPSIGNAL(시스템 콜 시그널의 설정)
```dockerfile
STOPSIGNAL 시그널
```

시그널 번호(9 등) 또는 시그널명(SIGKILL 등)을 지정 가능

ex)
```dockerfile
STOPSIGNAL SIGKILL
```

<br/>

### HEALTHCHECK(컨테이너 헬스 체크)
```dockerfile
HEALTHCHECK [옵션] CMD 명령

# or 헬스 체크 None
HEALTHCHECK NONE
```

Docker에 대해 컨테이너의 상태를 어떻게 확인할지를 설정  
--interval=n 옵션으로 헬스 체크 간격을,  
--timeout=n 옵션으로 헬스 체크 타임아웃을,  
--retries=N 옵션으로 타임아웃 횟수 지정 가능

ex)
```dockerfile
HEALTHCHECK --interval=5m --timeout=3s CMD curl -f http://localhost/ || exit 1
```
5분마다 가동 중인 웹 서버의 메인 페이지를 3초 안에 표시할 수 있는지 없는지 확인한다.  
`docker container inspect webapp`으로 헬스 체크 결과(Health 부분)를 확인할 수 있다.

---

## 환경 및 네트워크 설정
### ENV(환경변수 설정)
#### key value 형
```dockerfile
ENV <key> <value>
```

단일 환경변수에 하나의 값을 설정

ex)
```dockerfile
ENV myName "Taesan Kim"
ENV myNickName bigmountain
```

#### key=value 형
```dockerfile
ENV <key>=<value>
```

한 번에 여러 개의 환경변수를 설정

ex)
```dockerfile
ENV myName="Taesan Kim" \
	myNickName=bigmountain
```
하나의 ENV 명령을 사용했으므로 만들어지는 Docker 이미지는 하나이다.

#### ※ 특징
value 값은 모두 문자열로 취급(공백이나 따옴표와 같은 문자를 포함한 것도 문자로 취급)한다.  
\$myName 같이 변수 앞에 \를 추가하면 이스케이프 처리를 할 수 있다  
ENV 명령으로 지정한 환경변수는 docker container run할 때 --env 옵션을 사용하면 변경할 수 있다.

<br/>

### USER(사용자 지정)
```dockerfile
USER 유저명[:그룹명]

# or
USER 유저ID[:그룹ID]
```

RUN, CMD, ENTRYPOINT 같은 명령을 실행하기 위한 사용자를 지정할 때 사용  

ex)
```dockerfile
RUN ["adduser" "kts"]
USER kts
```

<br/>

### LABEL(라벨 지정)
```dockerfile
LABEL <key>=<value>
```

이미지에 버전 정보나 작성자 정보, 코멘트 등과 같은 정보를 제공할 때 사용

ex)
```dockerfile
LABEL maintainer="Taesan Kim" \
	title="WebAPP" \
	version="1.0" \
	description="This image is WebApplicationServer"
```
Docker 1.13이전 버전에서는 작성자를 기술할 때 MAINTAINER 명령을 사용했었지만, 현재 deprecated 되었다.

<br/>

### WORKDIR(작업 디렉터리 지정)
```dockerfile
WORKDIR [작업 디렉터리 경로]
```

RUN, CMD, ENTRYPOINT, COPY, ADD 같은 명령을 실행하기 위한 작업용 디렉터리를 지정할 때 사용
만일 지정한 디렉터리가 없으면 새로 작성

ex)
```dockerfile
WORKDIR /first
WORKDIR second
```
작업 디렉터리는 /firtst/second가 된다.

---

## 파일 설정
### ADD(파일 및 디렉터리 추가)
```dockerfile
ADD [--chown=유저명:그룹명] <호스트의 파일 경로> <Docker 이미지의 파일 경로>

# or
ADD [--chown=유저명:그룹명] ["<호스트의 파일 경로>" "<Docker 이미지의 파일 경로>"]
```

호스트상의 파일이나 디렉터리, 원격 파일을 Docker 이미지 안으로 복사할 때 사용  
--chown 옵션은 Linux OS 기반의 컨테이너 대상으로 사용

ex)
```dockerfile
# hos로 시작하는 모든 파일 추가
ADD hos* /docker_dir/

# 원격 파일 추가
ADD http://www.wings.msn.to/index.php /docker_dir/web/
```
이미지에 추가하고 싶은 파일이 원격 파일이 URL인 경우, 추가한 파일은 퍼미션이 600이 된다.  
호스트의 파일이 tar 아카이브거나 압축 포맷(gzip, bzip2 등)일 때는 디렉터리로 압축을 푼다.  
단, 원격 URL로부터 다운로드한 리소스는 압축이 풀리지 않는다.  
ADD 명령은 인증을 지원하지 않으므로 원격 파일의 다운로드에 인증이 필요한 경우는 RUN 명령에서 wget이나 curl을 사용해야 한다.

<br/>

### EXPOSE(포트 설정)
```dockerfile
EXPOSE <포트 번호> [<포트 번호>/<프로토콜>]
```

Docker에게 실행 중인 컨테이너가 listen하고 있는 네트워크를 알려줌  
docker container run 명령의 -p 옵션을 사용할 때 어떤 포트를 호스트에 공개할지를 정의

ex)
```dockerfile
# 프로토콜 명시하지 않을 경우, default 값은 tcp
EXPOSE 8080

EXPOSE 8080/udp
```

<br/>

### ARG(Dockerfile 내 변수 설정)
```dockerfile
ARG <이름>[=기본값]
```

Dockerfile 안에서 사용할 변수 정의  
ENV와는 달리 이 변수는 Dockerfile 안에서만 사용 가능

ex)
```dockerfile
ARG YOURNAME="Taesan"
RUN echo $YOURNAME
```
Dockerfile을 빌드할 때 --build-arg 옵션을 붙여 ARG 명령에서 지정한 YOURNAME에 값을 지정하면 지정한 값이 출력되고,  
옵션을 붙이지 않으면 default 값인 Taesan이 출력된다.

<br/>

### SHELL(기본 쉘 설정)
```dockerfile
SHELL ["쉘의 경로", "파라미터"]
```

쉘 형식으로 명령을 실행할 때 기본 쉘을 설정  
default 값은 Linux는 ["/bin/sh", "-c"], Windows는 ["cmd", "/S", "/C"]

ex)
```dockerfile
SHELL ["/bin/bash", "-c"]
```
SHELL 명령을 지정하면 Dockerfile 안에서 Shell 형식으로 지정한 RUN, CMD, ENTRYPOINT 명령에서도 유효해진다.

<br/>

### COPY(파일 복사)
```dockerfile
COPY [--chown=유저명:그룹명] <호스트의 파일 경로> <Docker 이미지의 파일 경로>

# or
COPY [--chown=유저명:그룹명] ["<호스트의 파일 경로>" "<Docker 이미지의 파일 경로>"]
```

ADD 명령과 매우 유사하나, COPY 명령은 호스트상의 파일을 이미지 안으로 복사하는 처리만 가능
단순히 이미지 안에 파일을 배치하기만 하고 싶을 때 사용

<br/>

### VOLUME(볼륨 마운트)
```dockerfile
VOLUME ["/마운트 포인트"]
```

지정한 이름의 마운트 포인트를 작성하고, 호스트나 그 외 다른 컨테이너로부터 볼륨의 외부 마운트를 수행  
JSON 배열 또는 여러 개의 인수로 된 문자열을 지정 가능  

ex)
```dockerfile
# JSON 배열
VOLUME ["/var/log/"]

# 여러 개의 인수로 된 문자열
VOLUME /var/log /var/db

# 예제
FROM ubuntu

RUN mkdir /myvol
RUN echo "hello world" > /myvol/greeting

VOLUME /myvol
```

<br/>

### ※ Dockerfile 저장 위치와 .dockerignore 파일
Docker에서 빌드를 하면 빌드를 실행한 디렉터리 아래에 있는 모든 파일이 Docker 데몬으로 전송된다.  
그래서 Dockerfile의 저장 위치는 빈 디렉터리가 좋다.  
Docker의 빌드에 필요 없는 파일은 Dockerfile과 똑같은 디렉터리에 두지 않도록 한다.

빌드에서 제외하고 싶은 파일이 있는 경우에는 '.dockerignore'이라는 이름의 파일 안에 해당 파일명을 기술한다.  
.dockerignore 파일을 설정해 두면 빌드에 불필요한 파일이 전송되지 않으므로 처리 속도가 빨라진다.
