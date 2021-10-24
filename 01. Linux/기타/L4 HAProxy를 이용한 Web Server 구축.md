## L4 HAProxy를 이용한 Web Server 구축

### 사전 준비 작업
- HAProxy 역할을 수행할 Server 1대
- Web 역할을 수행할 Server 2대

  ※ Server OS는 모두 Ubuntu

<br/>

### 내용
- HAProxy Load Balancer를 이용해 트래픽을 효과적으로 분산시켜 서버에 가해지는 부담 감소

<br/>

### 구현
#### Web Server
- Apache2 설치
  ```bash
  sudo apt install apache2 -y
  ```

- Web Server 구분을 위해 index.html 수정
  ```bash
  vi /var/www/html/index.html
  ```
  ```html
  ...
  <title>WebServer1</title>                         <!-- 다른 Web Server는 WebServer2로 설정 -->
  ...
  ```

#### HAProxy Load Balancer
- HAProxy 설치
  ```bash
  sudo apt install haproxy -y
  ```

- HAProxy 설정 변경
  ```bash
  vi /etc/haproxy/haproxy.cfg
  # --------------haproxy.conf--------------
  ...
  frontend LOADBALANCER-01
    bind 192.168.56.100:80                         # HAProxy Server IP의 80번 포트로 요청
    mode http
    default_backend WEBSERVER-01
  
  backend WEBSERVERS-01
    balance roundrobin                             # Load Balancing Algorithm
    server web1 192.168.56.101:80 check            # 트래픽을 보낼 Server 지정(Web Server의 IP와 Port)
    server web2 192.168.56.102:80 check
    option httpchk
  # ----------------------------------------
  ```

#### Web 접속
- http://192.168.56.100 으로 접속 후, 새로고침을 통해 Load Balancing이 되는 지 확인
