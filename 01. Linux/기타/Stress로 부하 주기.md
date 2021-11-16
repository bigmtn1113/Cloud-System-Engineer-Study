## Stress로 부하 주기

### Stress 설치
#### CentOS
```bash
yum install epel-release -y
yum install stress -y
```
#### Ubuntu
```bash
apt-get install stress -y
```

<br/>

### Stress 사용
#### CPU 부하
```bash
stress --cpu 2                        # stress --cpu <코어 수>
```
#### Memory 부하
```bash
stress --vm 2 --vm-bytes 2048m        # stress --vm <프로세스 수> --vm-bytes <사용할 크기>
```
#### HDD 부하
```bash
stress --hdd 2 --hdd-bytes 1024m      # stress --hdd <hdd 수> --hdd-bytes <사용할 크기>
```
#### Timeout
```bash
stress --cpu 2 --timeout 300s
```
