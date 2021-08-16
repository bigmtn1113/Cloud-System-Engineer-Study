## Data Volume Container

### 내용
- 데이터 저장만을 목적으로 하는 컨테이너  
- 컨테이너 간의 디렉터리를 공유  

데이터 볼륨 컨테이너의 볼륨은 HostOS의 /var/lib/docker/volumes/에 위치하며 이는 Docker가 관리

```shell
docker create -v /data-volume --name=data-vol ubuntu:14.04            # 프로세스 없이 컨테이너만 생성. /data-volume은 target(컨테이너 디렉터리)

docker container run -it --volumes-from=data-vol ubuntu:14.04 bash
컨테이너 bash# echo 'test1' > /data-volume/test1.txt
컨테이너 bash# cat /data-volume/test1.txt
컨테이너 bash# exit

docker container run -it --volumes-from=data-vol ubuntu:14.04 bash
컨테이너 bash# echo 'test2' > /data-volume/test2.txt
컨테이너 bash# cat /data-volume/test2.txt
컨테이너 bash# ls /data-volume
컨테이너 bash# exit

sudo su -
ls /var/lib/docker/volumes/볼륨이름/_data
```
