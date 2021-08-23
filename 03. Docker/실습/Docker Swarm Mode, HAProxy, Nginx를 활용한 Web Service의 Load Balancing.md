## Docker Swarm Mode, HAProxy, Nginx를 활용한 Web Service의 Load Balancing

### 사전 준비 작업
- HostOS 3대 준비
- HostOS별 터미널 준비

### 내용
- docker swarm: 여러 호스트(서버)의 컨테이너들을 배포 및 관리하는 orchestration tool
- swarm manager에 HAProxy 구성
- 웹(nginx) 접속 수행 -> overlay network 내에 있는 nginx container로 트래픽 분산(LB)

#### ※ Overlay Network
Docker Daemon Host간의 분산 네트워크 생성  
서로 다른 Docker Host에서 실행되는 컨테이너 간 통신을 위한 논리적인 네트워크 그룹

#### ※ docker_gwbridge
Overlay Network를 개별 Docker Daemon의 물리적 Network에 연결하는 Bridge Network

#### ※ ingress
Service의 Node들간에 Load Balancing을 하는 Overlay Network  
Docker Swarm의 모든 Node가 노출된 Port로 요청을 받게되면 해당 요청을 IPVS라는 모듈로 전달하고,  
IPVS는 해당 Service에 참여하는 모든 IP 주소를 추적하고 그 중 하나를 선택한 뒤, 요청을 해당 경로로 Routing

<br/>


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

#### Compose 파일과 Stack
```bash
docker network create --driver=overlay --attachable haproxy-web
docker network ls | grep haproxy-web

mkdir -p LABs/haproxy-nginx && cd $_
vi haproxy-web.yaml
```
```yaml
# -------------------haproxy-web.yaml-------------------
version: '3'
services:
  nginx:
    image: nginx:1.21
    deploy:
      replicas: 4
      placement:
        constraints: [node.role != manager]
      restart_policy:
        condition: on-failure
        max_attempts: 3
    environment:
      SERVICE_PORTS: 80
    networks:
      - haproxy-web

  proxy:
    image: dbgurum/haproxy:1.0
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
    ports:
      - 80:80
    networks:
      - haproxy-web
    deploy:
      mode: global
      placement:
        constraints: [node.role == manager]
    depends_on:
      - nginx

networks:
  haproxy-web:
    external: true
# -----------------------------------------------------------
```
```bash
docker stack deploy --compose-file=haproxy-web.yaml haproxy-web               # 80번 포트 사용 중이면 다른 포트 사용할 것
                                                                              # --compose-file, -c

# nginx 및 proxy 확인
docker stack services haproxy-web                                             # haproxy-web service 확인 
docker stack ps haproxy-web                                                   # 실행중인 컨테이너들도 확인

docker service logs -f haproxy-web_nginx
http://192.168.56.201접속 후, 새로고침 반복	              # [node.role != manager] 설정으로 worker1, 2 node에만 적용
```
