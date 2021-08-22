## Docker Swarm Mode 사용해보기

### 사전 준비 작업
- HostOS 3대 준비
- HostOS별 터미널 준비

### 내용
- 여러 호스트(서버)의 컨테이너들을 배포 및 관리하는 orchestration tool

#### Manager Node와 Worker Node 지정
```bash
docker info | grep Swarm              # active 상태 확인

# HostOS 1
docker swarm init --advertise-addr=192.168.56.201         # docker swarm join --token 비밀키 192.168.56.201:2377 확인
                                                          # Manager 역할을 할 Host 지정
                                                          # swarm manager 기본포트: 2377
                                                          # 7946 port(worker node 통신), 4789 port(ingress overlay network) 방화벽 open 상태여야 함

# HostOS 2, HostOS 3
docker swarm join --token 비밀키 192.168.56.201:2377         # Manager Node에서 확인했던 내용 그대로 입력

# HostOS 1
docker info | grep Swarm
docker info | grep Managers           # Manager 수 1 확인
docker info | grep Node               # Node 수 3 확인

docker node ls                        # Docker Swarm에 참여한 Node 정보 및 Manager Node가 누구인지 확인
docker network ls                     # docker_gwbridge, ingress 확인
```

#### Docker Swarm Visualizer
- 여러 호스트에 올라간 컨테이너를 시작적 형태로 피드백
- cluster가 구성 완료된 환경에서 사용

```bash
docker service create --name=viz_swarm -p 7070:8080 \
--constraint=node.role==manager \
--mount=type=bind,src=/var/run/docker.sock,dst=/var/run/docker.sock \
dockersamples/visualizer

docker service ls                     # viz_swarm 서비스 실행 확인

http://192.168.56.201:7070 접속
```
