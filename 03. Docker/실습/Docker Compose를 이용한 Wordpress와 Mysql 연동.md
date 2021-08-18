## Docker Compose를 이용한 Wordpress와 Mysql 연동

### Docker Compose
- 멀티 컨테이너 서비스
- 코드로 개발하는 컨테이너 서비스
- docker-compose: 멀티 컨테이너 생성 및 관리 도구.　*.yaml(*.yml) 코드 작성을 통해 실행(IaC)

### 사전 준비 작업
- 터미널 2개 준비

### 내용
#### docker-compose 설치
```shell
sudo curl -L https://github.com/docker/compose/releases/download/1.27.4/docker-compose-$(uname -s)-$(uname -m) -o /usr/local/bin/docker-compose

sudo chmod +x /usr/local/bin/docker-compose
sudo ln -s /usr/local/bin/docker-compose /usr/bin/docker-compose
sudo chown kebin /usr/local/bin/docker-compose

docker-compose -v
docker-compose version
```

#### wordpress와 mysql 연동
```shell
mkdir -p LABs/my_wp && cd $_
vi docker-compose.yaml
```
```yaml
# ----------------------docker-compose.yaml 내용----------------------
version: '3.9'
services:
  mydb:
    image: mysql:8.0
    container_name: mysql_app
    volumes:
      - mydb_data:/var/lib/mysql
    restart: always
    ports:
      - "3306:3306"
    networks:
      - backend-net
    environment:
      - MYSQL_ROOT_PASSWORD=wordpress
      - MYSQL_DATABASE=wordpress
      - MYSQL_USER=wordpress
      - MYSQL_PASSWORD=wordpress

  myweb:
    depends_on:
      - mydb
    image: wordpress:latest
    container_name: wordpress_app
    volumes:
      - myweb_data:/var/www/html
      - ${PWD}/myweb-log:/var/log
    restart: always
    ports:
      - "8888:80"
    networks:
      - frontend-net
      - backend-net
    environment:
      - WORDPRESS_DB_HOST=mydb:3306
      - WORDPRESS_DB_NAME=wordpress
      - WORDPRESS_DB_USER=wordpress
      - WORDPRESS_DB_PASSWORD=wordpress

networks:
  frontend-net: {}
  backend-net: {}
volumes:
  mydb_data: {}
  myweb_data: {}
# --------------------------------------------------------------------
```
```shell
docker-compose up             # foreground 형태로 실행되므로 이 터미널은 해당 컨테이너의 로그창이 됨

# 새 터미널
cd LABs/my_wp/

docker-compose ps            # 컨테이너 2개 UP 확인
docker volume ls             # 생성된 volume들 확인
docker network ls            # 생성된 network 확인

http://192.168.56.201:8888 접속 후, 로그인            # 서버 환경에 따라 안 들어가질 수 있다.

docker container exec -it mysql_app bash
컨테이너 bash# mysql -uroot -q              # 비밀번호: wordpress

mysql> show databases;
mysql> use wordpress;
mysql> show tables;             # 12개 테이블이 자동 생성 되고 게시판에 사용했던 정보가 테이블 데이터로 저장됨
```
