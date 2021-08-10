## Docker Container self Load Balancing

### 사전 준비 작업
- 2개의 터미널 창(t1, t2) 준비

```shell
docker network create -d bridge --subnet 172.200.1.0/24 netlb              # 사용자 정의 브리지 네트워크 생성
docker network ls

docker container run -itd --name=nlb_test1 --net=netlb --net-alias=nlb-net ubuntu:14.04     # --net-alias로 target group 지정
docker container run -itd --name=nlb_test2 --net=netlb --net-alias=nlb-net ubuntu:14.04
docker container run -itd --name=nlb_test3 --net=netlb --net-alias=nlb-net ubuntu:14.04

docker container inspect nlb_test1 | grep IPA                              # 172.200.1.xxx 대역의 IP 확인
docker container inspect nlb_test2 | grep IPA
docker container inspect nlb_test3 | grep IPA

docker container run -it --name=frontend --net=netlb ubuntu:14.04 bash
컨테이너 bash# ifconfig                                                    # 앞에 만든 컨테이너들과 동일한 IP 대역
컨테이너 bash# ping -c 2 nlb-net                                           # 랜덤으로 target group으로 지정된 컨테이너에 Ping 패킷 전달 -> 자체 DNS

컨테이너 bash# apt update
컨테이너 bash# apt -y install dnsutils
컨테이너 bash# dig nlb-net                                                 # target group 컨테이너들 확인

t2) docker container run -itd --name=nlb_test4 --net=netlb --net-alias=nlb-net ubuntu:14.04       # target group에 컨테이너 추가
컨테이너 bash# dig nlb-net
```
