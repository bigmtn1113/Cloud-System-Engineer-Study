## Nginx와 Apache2 Dockerfile 작성

### 내용
1\) Dockerfile 직접 작성  
2\) 작성한 Dockerfile을 build해 이미지 생성  
3\) 생성된 이미지로 컨테이너 실행 및 test  
4\) 검증된 이미지를 Docker Hub에 Upload(push)

#### Nginx
```shell
vi Dockerfile_nginx
# ----------Dockerfile_nginx 파일 내용----------
FROM ubuntu:18.04

RUN apt-get update && apt-get -y install -q nginx

COPY index.html /var/www/html

EXPOSE 80

CMD ["nginx", "-g", "daemon off;"]
# -----------------------------------------------

vi index.html
# ----------index.html 파일 내용----------
<h1>Hello, Docker Application.</h1>
# ----------------------------------------

DOCKER_BUILDKIT=1 docker build -f Dockerfile_nginx -t webapp:1.0 .           # Dockerfile의 이름을 다르게 사용할 땐 -f 옵션 사용
docker image ls | grep webapp
docker image history webapp:1.0

docker container run -itd --name=webapp_test1 -p 9001:80 webapp:1.0
curl localhost:9001

docker login
docker tag webapp:1.0 kva231/webapp:1.0
docker image ls | grep webapp
docker push kva231/webapp:1.0
```

#### Apache2
```shell
vi Dockerfile
# ------------------------Dockerfile 내용------------------------
FROM ubuntu:18.04

RUN apt -y update && apt -y install apache2

RUN echo 'Docker Container Application3.' > /var/www/html/index.html

RUN mkdir /webapp
RUN echo '. /etc/apache2/envvars' > /webapp/run_http.sh && \
    echo 'mkdir -p /var/run/apache2' >> /webapp/run_http.sh && \
    echo 'mkdir -p /var/lock/apache2' >> /webapp/run_http.sh && \
    echo '/usr/sbin/apache2 -D FOREGROUND' >> /webapp/run_http.sh && \
    chmod 744 /webapp/run_http.sh             # RUN을 여러번 쓰면 그 만큼 Layer가 생기므로 되도록 \로 이어서 작성

EXPOSE 80

CMD /webapp/run_http.sh
# ---------------------------------------------------------------

DOCKER_BUILDKIT=1 docker build -t webapp:3.0 .
docker image ls | grep webapp
docker image history webapp:3.0

docker container run -itd --name=webapp_test3 -p 9003:80 webapp:3.0
curl localhost:9003

docker login
docker tag webapp:3.0 kva231/webapp:3.0
docker image ls | grep webapp
docker push kva231/webapp:3.0
```
