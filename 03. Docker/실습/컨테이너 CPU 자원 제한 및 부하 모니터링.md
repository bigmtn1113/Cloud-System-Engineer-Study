## 컨테이너 CPU 자원 제한 및 부하 모니터링

### 사전 준비 작업
- 웹에서 cAdvisor 접속
- 터미널 2개(t1, t2) 준비
- t1에서 htop 가동

```shell
t2) docker run -d --rm --name=cpu_1024 --cpu-shares=1024 leecloudo/stress:1.0 stress --cpu 4
t1) htop 확인

t2) docker run -d --rm --name=cpu_512 --cpu-shares=512 leecloudo/stress:1.0 stress --cpu 4
t2) ps -auxf | grep stress

# cadvisor에서 두 개의 컨테이너 CPU 정보 및 Usage per Core 확인
# 확인 후, 빠르게 docker stop으로 컨테이너 두개 종료
```
