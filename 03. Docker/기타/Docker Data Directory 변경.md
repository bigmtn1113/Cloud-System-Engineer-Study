# Docker Data Directory 변경

<br>

default 경로는 /var/lib/docker/

### 1. 기존 docker 데이터 복사
```bash
sudo cp -R /var/lib/docker <변경 디렉터리>
```

### 2. 방법 1: /lib/systemd/system/docker.service 파일 변경
```bash
[Unit]
...

[Service]
...
ExecStart=/usr/bin/dockerd -H fd:// --containerd=/run/containerd/containerd.sock --data-root=<변경 디렉터리>
...

[Install]
...
```

ExecStart에 --data-root=<변경 디렉터리> 옵션 추가

### 2. 방법 2: /etc/docker/daemon.json 파일 생성

```json
{
  "data-root" : <변경 디렉터리>
}
```

### 3. Docker 재시작 및 확인
Docker 재시작 후, Root 디렉터리가 변경 됐는지 확인

```bash
sudo systemctl restart docker.service

docker info | grep Root
```

### 4. 이상 없을 시, /var/lib/docker 디렉터리 삭제
