## 같은 네트워크에 속한 컨테이너 간의 Ping

### 사전 준비 작업
- 3개의 터미널 창(t1, t2, t3) 준비

```
t1) docker network ls
t1) docker network create mynet
t1) docker network ls
t1) ifconfig

t1) docker container run -it --name=ubuntu1 --net=mynet ubuntu:14.04 bash
t2) docker container run -it --name=ubuntu2 --net=mynet ubuntu:14.04 bash

t3) docker container inspect ubuntu1 | grep IPA
t3) docker container inspect ubuntu2 | grep IPA

t3) sudo iptraf-ng
t1) 컨테이너 bash# ping ubuntu2             # 컨테이너 이름으로 ping이 가능
t2) 컨테이너 bash# ping ubuntu1

t3) 이 과정을 ipetraf-ng로 확인
```
