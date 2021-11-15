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
stress --cpu 2
```
#### Memory 부하
```bash
stress --vm 2 --vm-bytes 2048m
```
#### HDD 부하
```bash
stress --hdd 2 --hdd-bytes 1024m
```
