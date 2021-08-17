## Docker Compose를 이용한 wordpress와 mysql 연동

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
cd LABs/
mkdir myweb && cd $_
vi docker-compose.yaml
```
```yaml
# ----------------------docker-compose.yaml 내용----------------------
version: '3.8'
services:
  mydb:
    image: mysql:8.0
    container_name: mysql_app
    volumes:
      - mydb_data:/var/lib/mysql
    restart: always
    ports:
      - '3306:3306'
    environment:
      - MYSQL_ROOT_PASSWORD=password#
      - MYSQL_DATABASE=wpdb
      - MYSQL_USER=wpuser
      - MYSQL_PASSWORD=wppassword

  myweb:
    image: wordpress:latest
    container_name: wordpress_app
    volumes:
      - myweb_data:/var/www/html
      - /home/kebin/myweb-log:/var/log
    ports:
      - '8888:80'
    restart: always
    environment:
      - WORDPRESS_DB_HOST=mysql_app:3306
      - WORDPRESS_DB_NAME=wpdb
      - WORDPRESS_DB_USER=wpuser
      - WORDPRESS_DB_PASSWORD=wppassword
    depends_on:
      - mydb

volumes:
  mydb_data: {}
  myweb_data: {}
# --------------------------------------------------------------------
```
```shell
docker-compose up             # foreground 형태로 실행되므로 이 터미널은 해당 컨테이너의 로그창이 됨

# 새 터미널
cd LABs/myweb/

docker-compose ps            # 컨테이너 2개 UP 확인
docker volume ls             # 생성된 volume들 확인
docker network ls            # 생성된 network 확인

http://192.168.56.201:8888 접속             # 서버 환경에 따라 안 들어가질 수 있다.

docker container exec -it mysql_app bash
컨테이너 bash# mysql -uroot -q              # 비밀번호: password#

mysql> show databases;
mysql> use wpdb;
mysql> show tables;             # 12개 테이블이 자동 생성 되고 게시판에 사용했던 정보가 테이블 데이터로 저장됨
```
