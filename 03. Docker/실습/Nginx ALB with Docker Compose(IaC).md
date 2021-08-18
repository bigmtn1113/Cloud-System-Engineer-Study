## Nginx ALB with Docker Compose(IaC)

### 사전 준비 작업
- 터미널 2개 준비

<br/>

### 참고 사항
- [Docker Container Application Load Balancer](https://github.com/kva231/Cloud-System-Engineer-Study/blob/master/03.%20Docker/%EC%8B%A4%EC%8A%B5/Docker%20Container%20Application%20Load%20Balancer.md)

<br/>

### 내용
- Docker Compose를 이용해 Nginx ALB 구현

#### 구조
- docker-compose.yaml
- nginx_alb
  - Dockerfile-nginx
  - nginx.conf
- pyfla_app1
  - Dockerfile-pyapp1
  - pyfla_app1.py
  - requirements.txt
- pyfla_app2
  - Dockerfile-pyapp2
  - pyfla_app2.py
  - requirements.txt
- pyfla_app3
  - Dockerfile-pyapp3
  - pyfla_app3.py
  - requirements.txt

```bash
mkdir -p LABs/nginx_alb && cd $_
vi docker-compose.yaml
```
```yaml
# --------------docker-compose.yaml--------------
version: '3'
services:
  pyfla_app1:
    build:
      context: ./pyfla_app1
      dockerfile: Dockerfile-pyapp1
    ports:
      - "5001:5000"

  pyfla_app2:
    build:
      context: ./pyfla_app2
      dockerfile: Dockerfile-pyapp2
    ports:
      - "5002:5000"

  pyfla_app3:
    build:
      context: ./pyfla_app3
      dockerfile: Dockerfile-pyapp3
    ports:
      - "5003:5000"

  nginx:
    build:
      context: ./nginx_alb
      dockerfile: Dockerfile-nginx
    ports:
      - "8080:80"
    depends_on:
      - pyfla_app1
      - pyfla_app2
      - pyfla_app3
# ----------------------------------------------------- 
```
```bash
mkdir nginx_alb && cd $_
vi Dockerfile-nginx
```
```dockerfile
# --------------Dockerfile-nginx--------------
FROM nginx:1.19
COPY nginx.conf /etc/nginx/conf.d/default.conf
# ----------------------------------------------
```
```bash
vi nginx.conf
# --------------nginx.conf--------------
upstream web-alb {
  server 172.17.0.1:5001;
  server 172.17.0.1:5002;
  server 172.17.0.1:5003;
}

server {
  location / {
    proxy_pass	http://web-alb;
  }
}
# ----------------------------------------

mkdir -p ../pyfla_app1 && cd $_
vi Dockerfile-pyapp1
```
```dockerfile
# --------------Dockerfile-pyapp1--------------
FROM python:3

COPY ./requirements.txt /requirements.txt

WORKDIR /

RUN pip install -r requirements.txt

COPY . .

CMD ["python3", "pyfla_app1.py"]
# -------------------------------------------------
```
```bash
vi pyfla_app1.py
```
```python
# --------------pyfla_app1.py--------------
from flask import Flask

app1 = Flask(__name__)

@app1.route('/')
def hello_world():
    return 'Nginx Web Application Load Balancing Server [1]\n'

if __name__ == '__main__':
    app1.run(debug=True, host='0.0.0.0')
# -------------------------------------------
```
```bash
vi requirements.txt
# --------------requirements.txt--------------
Flask==1.1.2
# -----------------------------------------------

mkdir -p ../pyfla_app2
cp -r . ../pyfla_app2/

cd ../pyfla_app2
mv Dockerfile-pyapp1 Dockerfile-pyapp2
mv pyfla_app1.py pyfla_app2.py

vi Dockerfile-pyapp2			# CMD만 수정
```
```dockerfile
# --------------Dockerfile-pyapp2--------------
CMD ["python3", "pyfla_app2.py"]
# -------------------------------------------------
```
```bash
vi pyfla_app2.py				# app이름, 함수 리턴값 수정
```
```python
# --------------pyfla_app2.py--------------
app2 = Flask(__name__)

@app2.route('/')
def hello_world():
    return 'Nginx Web Application Load Balancing Server [2]\n'

if __name__ == '__main__':
    app2.run(debug=True, host='0.0.0.0')
# -------------------------------------------
```
```bash
mkdir -p ../pyfla_app3
cp -r . ../pyfla_app3/

cd ../pyfla_app3
mv Dockerfile-pyapp2 Dockerfile-pyapp3
mv pyfla_app2.py pyfla_app3.py

vi Dockerfile-pyapp3			# CMD만 수정
```
```dockerfile
# --------------Dockerfile-pyapp3--------------
CMD ["python3", "pyfla_app3.py"]
# -------------------------------------------------
```
```bash
vi pyfla_app3.py				# app이름, 함수 리턴값 수정
```
```python
# --------------pyfla_app3.py--------------
app3 = Flask(__name__)

@app3.route('/')
def hello_world():
    return 'Nginx Web Application Load Balancing Server [3]\n'

if __name__ == '__main__':
    app3.run(debug=True, host='0.0.0.0')
# -------------------------------------------
```
```bash
cd ..
docker-compose up

# 새 터미널
cd LABs/nginx_alb/

docker-compose ps             # 컨테이너 4개 UP 확인

curl localhost:8080                        # 입력할 때마다 출력문이 바뀌는 거 확인
http://192.168.56.201:8080 접속            # 새로고침할 때마다 글자가 바뀌는 거 확인
```
