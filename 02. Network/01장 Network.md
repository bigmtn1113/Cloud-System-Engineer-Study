# Network

<br/>

## Network 기본
### Network 정의
Net + Work. 컴퓨터들이 통신 기술을 이용하여 그물망처럼 연결된 통신 이용 형태

### Network 구성 요소
- **전송 단말기**: 정보 생성, 저장, 검색, 가공 작업을 하면서 동시에 Network에 Access할 수 있는 장치  
　　　　　　ex) PC, Mobile, ...

- **전송 매체**: 전기적인 신호를 전달할 수 있는 Cable  
　　　　　　ex) 동축케이블, 광케이블, ...

- **전송 프로토콜**: 프로토콜들은 각 전송 장비마다 주소를 부여하고 부여된 주소를 기반으로 사용자 Data 전송

- **전송 장비**: PAN, LAN, MAN, WAN 구간에서 사용되는 장비. 신호의 중폭과 사용자 주소 기반으로 정보 전달 기능

### Network를 이용한 데이터 전송 방법
Unicast - 1 대 1  
Broadcast - 1 대 All  
Multicast - 1 대 Many

- **Simplex(단방향)**: 한 시스템에서만 전송 가능  
　　　　　　　ex) TV, Radio, ...

- **Half Duplex(양방향)**: 양방향 전송이 가능하지만 한 시점에서는 단 방향 전송만 가능  
　　　　　　　ex) 무전기

- **Full Duplex(양방향)**: 한 시점에 양방향 전송 가능  
　　　　　　　ex) 핸드폰

### Network Protocol
Protocol: 통신 단말기 사이에 정보를 전송하기 위한 통신규약 혹은 표준

독점적 프로토콜 -> 프로토콜 개발 기준 설립(OSI 7 Layer)  
　=> 복잡함 감소, 인터페이스 표준화, 상호 호환성 확보, ...

### Network Protocol 계층화
OSI 7 Layer  
TCP/IP 4 Layer

- 복잡하고 다양한 유형의 통신 기능을 하나의 큰 프로토콜로 정의하기 거의 불가능하여 프로토콜 계층화

- 기능별로 계층을 구분하여 각 기능에 맞게 표준화 진행

- 네트워크 추가, 변경, 유지 보수 등의 문제 해결 편리

<br/>

## DHCP
### DHCP 서비스
Dynamic Host Configuration Protocol. 동적 IP 제공 서비스  
DHCP는 **UDP** 전송 프로토콜이다.  
DHCP 서버는 IP를 자동으로 부여하는 서버로써 MS의 서비스뿐만 아니라 대부분의 IP 자동 부여를 목적으로 한다.

### DHCP 원리
DHCP 서버는 client에게 IP 주소를 할당할 때 영구적으로 할당하는 것이 아니라 임대기간을 명시하여 그 기간 동안만 client가 IP 주소를 사용하도록 한다.  
client는 임대기간을 연장하고 싶으면 DHCP 서버에 요청하면 되고, 더 이상 필요없으면 IP 주소를 반납하면 된다.

1. **DHCP Discover**  
IP 주소가 할당되지 않은 client는 MAC 주소를 기반으로 IP 주소를 받아오기 위해 LAN에 MAC 주소가 담긴 Discover 패킷을 Broadcast한다.

2. **DHCP Offer**  
DHCP 서버가 Discover 패킷을 받게 되면, DHCP 서버는 Offer 패킷을 Broadcast한다. 이때 MAC 주소에 클라이언트로부터 받았던 MAC 주소를 적어서 보낸다. 

3. **DHCP Request**  
해당 클라이언트는 Offer를 받았으면 해당 네트워크에 DHCP 서버가 있다고 판단해 Request 패킷(IP 요청)을 Broadcast한다.

4. **DHCP ACK**  
DHCP 서버는 Request 패킷을 받으면 자신의 IP 임대 풀에서 할당 가능한 IP 주소를 찾아 패킷에 담고 Broadcast한다. 클라이언트가 이 패킷을 정상적으로 받으면 IP 주소가 할당된다.
