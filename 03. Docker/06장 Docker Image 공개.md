# Docker Image 공개

---

## Docker Registry를 사용한 Private Registry 구축
### 로컬 환경에 Docker Registry 구축
Docker registry를 private network 안에서 구축하기 위해 Docker Store에 공개되어 있는 registry 이미지를 사용

#### registry 다운로드
```bash
docker pull registry
```

#### registry 컨테이너 시작
```bash
docker container run -d -p 5000:5000 --name=registry registry
```
registry는 5000번 포트를 사용하므로 -p 옵션 값을 5000:5000으로 지정

<br/>

### Docker Image Upload
#### Dockerfile 작성
```dockerfile
FROM nginx:1.21

EXPOSE 80

CMD ["nginx", "-g", "daemon off;"]
```

#### Dockerfile build
```bash
docker build -t localhost:5000/nginx-registry .
```

#### Image Upload
```bash
docker image push localhost:5000/nginx-registry 
```
localhost의 5000번 포트에서 작동하는 registry에 이미지 업로드

#### local Image 삭제
```bash
docker image rm localhost:5000/nginx-registry
```

<br/>

### Docker Image Download
#### Image Download
```bash
docker image pull localhost:5000/nginx-registry
```

#### Image Test
```bash
docker container run -d -p 80:80 localhost:5000/nginx-registry
curl localhost:80
```
