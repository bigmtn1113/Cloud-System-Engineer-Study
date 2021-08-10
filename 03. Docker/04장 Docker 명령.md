# Docker 명령

---

## Docker 이미지 조작
### Docker Hub
https://hub.docker.com/  
GitHub와 같은 소스코드 관리 툴과 연계하여 코드를 빌드하는 기능이나 실행 가능한 앱의 이미지를 관리하는 기능을 갖춘 **Docker의 공식 repository 서비스**

물리 서버든, 가상 머신이든, 클라우드든 Docker 이미지 배포 가능

<br/>

### docker image pull
**이미지 다운로드**

`docker image pull [옵션] 이미지명[:태그명]`  
태그명을 생략하면 latest 버전을 다운로드

ex) `docker image pull centos:7`

#### ※ latest 버전 이미지를 pull 할 때 주의사항
latest 버전으로 이미지를 다운받으면 docker image ls로 확인할 시, latest라고 표시가 돼서 정확한 버전을 알 수가 없다.  
버전을 확인하려면 container를 bash로 실행시킨 후, /etc/os-release 파일을 열어봐야 정확한 버전을 알 수 있다.

<br/>

### docker image ls
**이미지 목록 표시**

`docker image ls [옵션] [repository명]`

ex) `docker image ls --digests`  
Docker registry에 업로드한 이미지는 이미지를 고유하게 식별하기 위한 Digest가 부여된다.  
Digest를 표시하고 싶을 땐 --digestss 옵션을 설정한다.  
tag로 작성된 image들은 DIGEST에 <none>으로 표시된다.

<br/>

### docker image inspect
**이미지 상세 정보 확인**

`docker image inspect [옵션] 이미지명 [이미지명]`
결과는 JSON 형식으로 표시된다.

ex) `docker image inspect --format="{{.Os}}" centos:7 ubuntu:18.04`  
원하는 정보만 보고 싶을 땐 --format 옵션에서 찾고자 하는 정보를 지정한다.  
`docker image inspect centos:7 | grep Os`도 하나의 방법이 될 수 있다.

<br/>

### docker image tag
**이미지 태그 설정**

`docker image tag 소스 이미지명[:태그명] 타겟 이미지명[:태그명]`  
식별하기 쉬운 버전명을 붙이는 것이 일반적이다.  
Docker Hub에 작성한 이미지를 등록하려면 **<Docker Hub 사용자명>/이미지명:[태그명]** 식으로 작성한다.  
태그는 이미지에 별명을 붙일 뿐 이미지를 복사하거나 이름을 바꾼 것이 아니므로 이미지 ID가 똑같다.

ex) `docker image tag nginx kva231/webserver:1.0`

<br/>

### docker search
**이미지 검색**

`docker search [옵션] <검색 키워드>`  
결과로 표시되는 항목 중 OFFICIAL은 공식 이미지인지 아닌지를 판단하고, AUTOMATED는 Dockerfile을 바탕으로 자동 생성된 이미지인지 아닌지를 판단한다.

ex) `docker search --filter=stars=1000 nginx`  
즐겨찾기가 1000이상인 nginx 이미지 목록을 출력한다.

<br/>

### docker image rm
**이미지 삭제**

`docker image rm [옵션] 이미지명 [이미지명]`  
이미지명은 [REPOSITORY] 또는 [IMAGE ID]을 지정한다.

ex) `docker image rm nginx`

#### ※ docker image prune
`docker image prune -a`을 사용하면 사용하지 않은 이미지를 모두 삭제할 수 있다.

<br/>

### docker login
**Docker 로그인**

`docker login [옵션] [서버]`  
--username, -u와 --password, -p 옵션을 통해 로그인할 수 있으며 서버명을 지정하지 않았을 때는 Docker Hub에 액세스된다.

ex) `docker login`

<br/>

### docker image push
**이미지 업로드**

`docker image push 이미지명[:태그명]`  
**<Docker Hub 사용자명>/이미지명:[태그명]** 같은 형식을 지정하고 이미지를 업로드한다.  

ex) `docker image push kva231/webserver:1.0`

<br/>

### docker logout
**Docker 로그아웃**

`docker logout [서버명]`  
서버명을 지정하지 않았을 땐 Docker Hub에 액세스한다.

ex) `docker logout`

<br/>

### ※ Docker Content Trust(DCT)
- 서명  
  이미지 작성자가 Docker registry에 이미지를 업로드하기 전에 로컬 환경에서 이미지 작성자의 Offline Key(비밀키)를 사용하여 이미지에 서명한다.

- 검증  
  서명된 이미지를 다운로드할 때 이미지 작성자의 Tagging Key(공개키)를 사용하여 이미지 진위 여부를 파악한다.  
  만일 변조된 경우는 그 이미지를 무효로 만든다.

#### ※ DCT 기능 유효화
`export DOCKER_CONTENT_TRUST=1`  
이 기능을 유효화해 놓으면 이미지 다운로드 시, 이미지 검증이 일어나고 Tagging 메시지가 출력된다.

#### ※ DCT 기능 무효화
`export DOCKER_CONTENT_TRUST=0`

---

## Docker 컨테이너 생성/ 시작/ 정지
### Docker 컨테이너 Life Cycle
1\) 컨테이너 생성  
2\) 컨테이너 시작  
3\) 컨테이너 정지  
4\) 컨테이너 삭제

- docker container create  
	**컨테이너 생성**
	
	이미지(Linux의 작동에 필요한 /etc나 /bin 등과 같은 디렉터리 및 파일들)로부터 컨테이너를 생성한다.  
	또한, 이미지의 스냅샷(스토리지 안에 존재하는 파일과 디렉터리를 특정 타이밍에서 추출한 것)을 취한다.

- docker container start  
	**컨테이너 시작**
	
	정지 중인 컨테이너를 시작할 때 사용한다.  
	컨테이너 상에서 임의의 프로세스를 시작한다.

- docker container run  
	**컨테이너 생성 및 시작**
	
	이미지로부터 컨테이너를 생성하고 이미지 스냅샷을 취하며, 컨테이너 상에서 임의의 프로세스를 시작한다.

- docker container stop  
	**컨테이너 정지**
	
	실행 중인 컨테이너를 정지시킬 때 사용한다.  

- docker container rm  
	**컨테이너 삭제**
	
	정지 중인 컨테이너를 삭제할 때 사용한다.

<br/>

### docker container run
**컨테이너 생성 및 시작**  

`docker container run [옵션] 이미지명[:태그명] [인수]`

ex) `docker container run -it --name "test1" centos /bin/cal`  
centos 이미지로 test1이라는 이름의 컨테이너를 실행시키고, 컨테이너 안에서 /bin/cal 명령을 실행한다.  
--interactive, -i 옵션은 컨테이너의 표준 출력을 연다는 뜻이고  
--tty, -t 옵션은 tty(단말 디바이스)를 확보한다는 뜻이다.

ex) `docker container run -d centos /bin/ping localhost`  
centos 이미지로 컨테이너를 생성하고, localhost에 /bin/ping 명령을 실행한다.  
--detach, -d 옵션은 컨테이너를 백그라운드에서 실행한다는 뜻이다.  
`docker container logs [옵션] <컨테이너 식별자>`를 통해 백그라운드에서 실행중인지 확인할 수 있다.

ex) `docker container run -d -p 8080:80 nginx`  
--publish, -p 옵션으로 호스트와 컨테이너의 포트를 매핑할 수 있다.  
이때, docker-proxy가 생기고 호스트의 포트 번호 8080과 컨테이너의 포트 번호 80을 매핑시킨다.

ex) `docker container run -d --dns 192.168.1.1 nginx`  
--dns 옵션으로 컨테이너용 DNS 서버의 IP 주소를 지정할 수 있다.

ex) `docker container run -it -h www.test.com --add-host www.test2.com:192.168.1.1 centos`  
--hostname, -h 옵션으로 컨테이너 자신의 호스트명을 지정할 수 있고  
--add-host 옵션으로 컨테이너 안의 /etc/hosts에 호스트명과 IP 주소를 정의할 수 있다.  
`cat /etc/hosts`하면 정의된 내용을 확인할 수 있다.

#### ※ --net 옵션
설정값 | 설명
--- | ---
bridge | 브리지 연결을 사용
none | 네트워크에 연결하지 않음
container:[name \| id] | 다른 컨테이너의 네트워크를 사용
host | 컨테이너가 호스트 OS의 네트워크를 사용
NETWORK | 사용자 정의 네트워크를 사용

- 다른 container 사용  
  **컨테이너의 IP, Mac, veth 등을 공유**


  ex) `docker container run -itd --name=container_link2 --net=container:container_link1 centos`
  
  `docker container inspect container_link2 | grep IPA`를 통해 IP가 없는 것을 볼 수 있고  
  `docker container exec container_link2 ifconfig`를 통해 IP가 container_link1과 똑같은 것을 볼 수 있다.  
  container_link2는 자체적으로 IP를 가지지 않고 container_link1의 IP를 공유한다.

- 호스트 OS 네트워크 사용  
  **컨테이너가 호스트 IP와 Port를 사용**
  
  호스트 네트워크를 이용하므로 vethxxxxx도 생성되지 않는다.  
  낮은 지연시작을 갖을 수 있으나, 호스트 부담이 증가하므로 추천하지 않는 방식이다.

  ex) `docker container run --name=nginx_host --net=host nginx:1.19`

- 사용자 정의 네트워크 사용  
  ex) `docker container run -it --net=webapp-net centos`
  
  사용자 정의 네트워크를 작성 및 사용하고 컨테이너를 생성 및 실행한다.

<br/>

### docker container ls
**가동 컨테이너 목록 표시**

`docker container ls [옵션]`  

ex) `docker container ls -a -f exited=0`  
종료 코드가 0인 모든 컨테이너 목록을 표시한다.  
--all, -a 옵션은 모든 컨테이너를 표시한다는 뜻이고  
--filter, -f 옵션은 컨테이너를 필터링한다는 뜻이다.

<br/>

### docker container stats
**컨테이너 가동 확인**

`docker container stats [컨테이너 식별자]`

ex) `docker container stats webserver`  
CPU, 메모리 사용률, 네트워크 I/O 등 정보를 출력한다.  
`docker container top <컨테이너 식별자> [ps 옵션]`를 통해 컨테이너에서 실행중인 프로세스를 확인할 수도 있다.

<br/>

### docker container start
**컨테이너 시작**

`docker container start [옵션] <컨테이너 식별자> [컨테이너 식별자]`

ex) `docker container start 5370f74ed4cf`

<br/>

### docker container stop
**컨테이너 정지**

`docker container stop [옵션] <컨테이너 식별자> [컨테이너 식별자]`  

ex) `docker container stop -t 2 5370f74ed4cf`  
컨테이너를 2초 후에 정지시킨다.  
--time, -t 옵션은 컨테이너의 정지 시간을 지정(default는 10초)한다는 뜻이다.  
`docker container kill [옵션] <컨테이너 식별자> [컨테이너 식별자]`를 통해 강제적으로 정지시킬 수 있다.

<br/>

### docker container restart
**컨테이너 재시작**

`docker container restart [옵션] <컨테이너 식별자> [컨테이너 식별자]`

ex) `docker container restart -t 2 webserver`  
컨테이너를 2초 후에 재시작한다.  
--time, -t 옵션은 컨테이너의 재시작 시간을 지정(default는 10초)한다는 뜻이다.

<br/>

### docker container rm
**컨테이너 삭제**

`docker container rm [옵션] <컨테이너 식별자> [컨테이너 식별자]`

ex) `docker container rm 5370f74ed4cf`

#### ※ docker container prune
정지 중인 모든 컨테이너를 삭제할 수 있다.

<br/>

### docker container pause/ unpause
**컨테이너 중단/재개**

`docker container pause <컨테이너 식별자>`

ex)   
```
docker container pause webserver
ls
docker container unpause webserver
```

---

## Docker 컨테이너 네트워크
### docker network ls
**네트워크 목록 표시**

`docker network ls [옵션]`

네트워크 구성 정보 목록을 확인할 수 있다.  
기본값으로 bridge, host, none 이 세개의 네트워크를 만든다.  
네트워크를 지정하지 않고 컨테이너를 시작하면 bridge 네트워크로 컨테이너가 시작된다.  
`docker container inspect test | grep NetworkID` 하면 test 컨테이너가 소속된 네트워크를 알 수 있다.

ex) `docker network ls`

<br/>

### docker network create
**네트워크 작성**

`docker network create [옵션] 네트워크`

--driver, -d 옵션을 주면 네트워크 브리지(기본값) 또는 오버레이를 지정할 수 있고  
--ip-range 옵션을 주면 컨테이너에 할당하는 IP 주소의 범위를 지정할 수 있다.  
이 외에도 --subnet 옵션으로 서브넷을 CIDR 형식으로 지정할 수 있다.

ex) `docker network create -d bridge --subnet=172.100.1.0/24 --ip-range=172.100.1.0/24 web-network`  
사용자 정의 네트워크 web-network를 생성한다.  
`docker container run --net=web-network ~~`하면 web-network 대역대로 컨테이너에 IP가 할당된다.

<br/>

### docker network inspect
**네트워크 상세 정보 확인**

`docker network inspect [옵션] 네트워크`

네트워크 이름, id, 서브넷, 게이트 웨이 등뿐만 아니라 네트워크 안에 가동 중인 컨테이너 정보도 확인할 수 있다.

ex) `docker network inspect web-network`
