# Docker 용량 정리

<br>

## 용량 확인
### Docker 디스크 사용량 확인
```bash
docker system df
```
![image](https://user-images.githubusercontent.com/46125158/200555815-4171e12f-859f-4ad9-8826-7c214e065329.png)

### Docker data directory 용량 확인
du 명령어의 `s(summarize)` 옵션은 용량의 합계를 출력

```bash
du -sh /var/lib/docker/

# Images 용량 확인
du -sh /var/lib/docker/overlay2

# Containers 용량 확인
du -sh /var/lib/docker/containers

# Volumes 용량 확인
du -sh /var/lib/docker/volumes
```

<br>

## Docker 데이터 저장소 정리
`a(all)` 옵션은 dangling 이미지(tag: <none>)뿐만 아니라 사용하지 않는 모든 이미지를 제거  
`f(force)` 옵션은 확인 메시지(y/N)를 표시하지 않고 진행

### Images 정리
사용하지 않는 images 제거

```bash
docker image prune -af
```

### Containers 정리
사용하지 않는 containers 제거

```bash
docker container prune -f
```

### Volumes 정리
컨테이너에서 사용하지 않는 volumes 제거

```bash
docker volume prune -f
```

### ※ 한번에 모두 정리
기본적으로 현재 볼륨을 사용하는 컨테이너가 없는 경우 중요한 데이터가 삭제되는 것을 방지하기 위해 볼륨을 제거하지 않으니 volumes 옵션 사용해 정리

```bash
docker system prune --volumes -af
```

<hr>

## 참고
- **Docker CLI** - https://docs.docker.com/engine/reference/commandline/docker/
