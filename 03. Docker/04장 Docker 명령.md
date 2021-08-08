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
