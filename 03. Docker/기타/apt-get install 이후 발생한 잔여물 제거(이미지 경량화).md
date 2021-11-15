## apt-get install 이후 발생한 잔여물 제거(이미지 경량화)

### 목적
**불필요한 파일 제거를 통한 이미지 경량화**

### 내용
- **apt-get clean:** 설치에 사용한 패키지 라이브러리 파일(/var/cache/apt/archives/ 안의 .deb) 삭제
- **apt-get autoremove:** 다른 패키지 설치시, 의존성 때문에 설치된 패키지가 더 이상 사용되지 않을때 자동 삭제

```shell
vi Dockerfile
# ----------------Dockerfile 내용----------------
FROM ubuntu:14.04
MAINTAINER "Taesan-Kim <kva231@naver.com>"
LABEL "purpose"="webserver practice"

RUN apt-get update && apt-get -y install apache2 && \
	apt-get -y clean && \
	apt-get -y autoremove && \
	rm -rfv /tmp/* /var/lib/apt/lists/* /var/tmp/*

WORKDIR /var/www/html
ADD index.html .
EXPOSE 80
CMD ["apache2ctl", "-D", "FOREGROUND"]
# -----------------------------------------------

DOCKER_BUILDKIT=1 docker build -t myhttp:3.0 .
docker image ls | grep myhttp
docker image history myhttp:3.0
```
