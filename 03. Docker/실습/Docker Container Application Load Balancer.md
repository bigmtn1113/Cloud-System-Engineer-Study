## Docker Container Application Load Balancer

### 내용
- Container 위에 Nginx를 실행시키고 Load Balancing을 구현
- Load Balancer 1대와 Web Server 3대를 각각 컨테이너로 구축

```shell
docker container run -itd -p 80:80 -h frontend --name=frontend nginx:1.19
sudo netstat -nlp | grep 80
curl localhost:80

docker container run -itd -e SERVER_PORT=5001 -p 5001:80 -h alb-node01 -u root --name=alb-node01 nginx:1.19
docker container run -itd -e SERVER_PORT=5002 -p 5002:80 -h alb-node02 -u root --name=alb-node02 nginx:1.19
docker container run -itd -e SERVER_PORT=5003 -p 5003:80 -h alb-node03 -u root --name=alb-node03 nginx:1.19

sudo netstat -nlp | grep 5001
sudo netstat -nlp | grep 5002
sudo netstat -nlp | grep 5003

vi nginx.conf
# ---------nginx.conf 파일 내용---------
events { worker_connections 1024; }

http {
  upstream backend-alb {
    server 192.168.56.201:5001;             # HostOS의 IP. 컨테이너의 IP가 아니다.
    server 192.168.56.201:5002;
    server 192.168.56.201:5003;
  }

  server {
    listen 80 default_server;
    location / {
      proxy_pass	http://backend-alb;
    }
  }
}
# -------------------------------------

docker cp nginx.conf frontend:/etc/nginx/nginx.conf
docker restart frontend

# 192.168.56.201 반복 접속하면서 변화 확인
```
