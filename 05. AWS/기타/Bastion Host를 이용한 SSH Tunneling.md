## Bastion Host를 이용한 SSH Tunneling

#### SSH(Secure Shell Protocol)
네트워크 프로토콜 중 하나로 컴퓨터 간의 인터넷 같은 Public Network를 통해 통신할 때 서로 **안전한 통신**을 하기 위해 사용하는 프로토콜

#### SSH Tunneling
사내 내부망에 있는 서버로 직접 접속을 허용하지 않는 경우 외부에서 내부에 접근하기 위해 터널링을 이용  
이때 SSH 프로토콜을 이용하여 패킷을 암호화

<br/>

### EC2에 접근
#### Putty 사용
- Bastion Host 설정  
  - Session -> Host Name - \<Bastion Host IP\>  
  - Session -> Port - 22  
  - Connection -> SSH -> Auth -> Private key file for authentification - \<Bastion Host PPK\>  
  - Connection -> SSH -> Auth -> Tunnels -> Destination - <접속하려는 EC2 IP>:ec2-user  
  - Connection -> SSH -> Auth -> Tunnels -> Source port - 22

- 접속하려는 EC2 설정(Bastion Host 접속 유지한 채로 진행)  
  - Session -> Host Name - localhost  
  - Session -> Port - 22  
  - Connection -> SSH -> Auth -> Private key file for authentification - \<접속하려는 EC2 PPK\>

<br/>

### RDS(Aurora)에 접근
#### DBeaver 사용
- SSH  
  - Settings  
    - Host/IP - \<Bastion Host IP\>  
    - User Name - ec2-user  
    - Authentication Method - Public Key  
    - Private Key - \<Bastion Host PEM\>

- Main  
  - Server  
    - Server Host - \<Aurora Endpoint\>  
  - Authentication  
    - Username - \<Aurora 마스터 사용자 이름\>  
    - Password - \<Aurora 마스터 사용자 비밀번호 입력\>

<br/>

### ElastiCache(Redis)에 접근
#### Redis Desktop Manager 사용
- Main Settings  
  - Name - \<Cluster Name\>  
  - Address - \<ElastiCache for Redis Endpoint\> : 6379

- Secutiry  
  - SSH Tunnel  
    - SSH Address - \<Bastion Host IP\>  
    - SSH User - ec2-user  
    - Private Key - \<Bastion Host PEM\>

<br/>

### ※ 참고
Bastion에 접속해서 ssh, mysql, redis-cli 명령어를 쳐서 접속하기 보단 SSH Tunneling 사용 권장
