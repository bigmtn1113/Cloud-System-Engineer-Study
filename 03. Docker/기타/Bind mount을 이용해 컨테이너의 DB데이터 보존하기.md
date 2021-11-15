## Bind mount을 이용해 컨테이너의 DB데이터 보존하기
### 내용
원래 컨테이너가 종료되면 그 안의 데이터들은 모두 사라진다.  
이때, bind mount을 이용하면 HostOS와 컨테이너를 mount시켜 컨테이너의 데이터를 보존할 수 있다.

container를 run할 때 --volume, -v 옵션을 사용한다.

```shell
docker container run -itd --rm --name=vtest-mysql \
> -e MYSQL_ROOT_PASSWORD=pass123 \
> -v /home/kebin/mysql-volume:/var/lib/mysql mysql:5.7                  # HostOS와 컨테이너에 해당 디렉터리 및 파일이 없으면 자동으로 생성된다.

docker container exec -it vtest-mysql bash
컨테이너 bash# service mysql status
컨테이너 bash# mysql -uroot -p

mysql> show databases;
mysql> use dockerdb;		      # 없으면 create database docekrdb한 후, 실행
mysql> status;

mysql> create table t1 (c1 int, c2 varchar(10));
mysql> show tables;
mysql> insert into t1 values (10, 'docker');
mysql> select * from t1;
mysql> exit;

컨테이너 bash# ls -l /var/lib/mysql/dockerdb
컨테이너 bash# exit

sudo ls -l mysql-volume/dockerdb

docker container stop vtest-mysql           # 컨테이너 run 할때 --rm 옵션을 줬으므로 stop 시, 자동 삭제

# 컨테이너가 종료 되었는데도 db 데이터가 존재
# 삭제된 컨테이너의 볼륨을 재사용하면 자동으로 db의 모든 데이터가 연결됨 -> 데이터의 영속성(지속성)
docker container run -itd --rm --name=vtest-mysql \
> -e MYSQL_ROOT_PASSWORD=pass123 \
> -e MYSQL_DATABASE=dockerdb \
> -v /home/kebin/mysql-volume:/var/lib/mysql mysql:5.7

docker container exec -it vtest-mysql bash
컨테이너 bash# ls -l /var/lib/mysql/dockerdb
컨테이너 bash# exit

sudo ls -l mysql-volume/dockerdb
```
