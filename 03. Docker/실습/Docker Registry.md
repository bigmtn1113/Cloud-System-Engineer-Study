## Docker Registry

### 목적
기업 인프라 내에서 이미지 공유 목적

### 사전 준비 작업
- 테스트를 위해 서버 2대 준비

```shell
# 1번 서버
docker pull registry

docker info               # Insecure Registries: 127.0.0.0/8 확인

sudo vi /etc/init.d/docker
# ------------------/etc/init.d/docker 내용------------------------
31 DOCKER_OPTS=--insecure-registry 192.168.56.201:5000
# -----------------------------------------------------------------

sudo vi /etc/docker/daemon.json
# ------------------/etc/docker/daemon.json 내용-------------------
{ "insecure-registries": ["192.168.56.201:5000"] }
# -----------------------------------------------------------------

sudo service docker restart
docker info               # Insecure Registries: 192.168.56.201:5000 추가 확인

docker container run -d -p 5000:5000 --name=local-registry registry

sudo netstat -nlp | grep 5000
curl -X GET http://192.168.56.201:5000/v2/_catalog

docker image tag test:1.0 192.168.56.201:5000/test:1.0
docker image ls | grep test
docker push 192.168.56.201:5000/test:1.0

curl -X GET http://192.168.56.201:5000/v2/_catalog
curl -X GET http://192.168.56.201:5000/v2/test/tags/list

# 2번 서버
sudo vi /etc/init.d/docker
# ------------------/etc/init.d/docker 내용------------------------
31 DOCKER_OPTS=--insecure-registry 192.168.56.201:5000
# -----------------------------------------------------------------

sudo vi /etc/docker/daemon.json
# ------------------/etc/docker/daemon.json 내용-------------------
{ "insecure-registries": ["192.168.56.201:5000"] }
# -----------------------------------------------------------------

sudo service docker restart
docker info               # Insecure Registries: 192.168.56.201:5000 추가 확인

docker pull 192.168.56.201:5000/test:1.0

docker image tag test:2.0 192.168.56.201:5000/test:2.0
docker push 192.168.56.201:5000/test:2.0
```
