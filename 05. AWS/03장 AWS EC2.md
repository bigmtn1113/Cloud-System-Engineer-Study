## AWS EC2(Elastic Compute Cloud)
- 확장 가능 컴퓨팅 용량 제공

- 하드웨어에 선 투자할 필요가 없어 더 빠르게 앱 개발 및 배포 가능

- 원하는 만큼 가상 서버를 구축하고 보안 및 네트워크 구성과 스토리지 관리 가능

- 요구 사항이나 갑작스런 트래픽 증가 등 변동 사항에 따라 신속하게 규모를 확장하거나 축소할 수 있어 서버 트래픽 예측 필요성 감소

### Instance
가상 컴퓨팅 환경

#### Instance Type
인스턴스를 위한 CPU, Memory, Storage, Networking 용량의 여러가지 구성 제공

- **범용(General Purpose):** 모든 리소스의 균형을 알맞게 유지하여 사용 가능

- **컴퓨팅 최적화(Compute Optimized):** 고성능 프로세서의 이점을 활용(일괄처리, 머신러닝, ...)

- **메모리 최적화(Memory Optimized):** 메모리에서 대규모 데이터(관계형 및 NoSQL 데이터, 키-값 유형 데이터, 비정형 데이터)를 처리

- **가속화된 컴퓨팅(Accelerated Computing):** 그래픽 처리와 같은 하드웨어 기반 컴퓨팅 리소스에 비중

- **스토리지 최적화(Storage Optimized):** 로컬 스토리지의 초대형 데이터 세트에 대한 순차적 읽기 및 쓰기 엑세스가 많이 필요한 작업(대량 병렬 처리, 데이터 웨어하우스, 하둡 분산, 로그 처리, ...)에 적합

#### Tag
사용자가 생성하여 EC2 리소스에 할당할 수 있는 메타데이터

#### Key Pair
인스턴스 로그인 정보 보호
AWS는 public key를 저장하고 사용자는 개인 키를 안전한 장소에 보관

#### Instance Store Volume
임시 데이터를 저장하는 스토리지 볼륨으로 인스턴스 중단, 최대 절전 모드로 전환 또는 종료 시 삭제

#### ※ VPC(Virtual Private Cloud)
논리적으로는 격리 상태지만 원할 때마다 고객의 네트워크와 간편히 연결할 수 있도록 해주는 가상 네트워크

<br/>

### Image
#### AMI(Amazon Machine Image)
EC2 인스턴스의 기반이 되는 이미지
서버에 필요한 OS와 여러 SW들이 적절히 구성된 상태로 제공되는 템플릿
인스턴스를 쉽게 생성 가능

<br/>

### EBS(Elastic Block Store)
EBS 볼륨을 사용해 영구 스토리지 볼륨에 데이터 저장

<br/>

### Network & Security
#### Security Group
보안을 위해 IP와 포트 번호를 이용해 정의해두는 서버 접속 규칙  
인스턴스에 연결할 수 있는 프로토콜, 포트, 소스 IP 범위를 지정하는 방화벽 기능

#### EIP(Elastic IP)
동적 클라우드 컴퓨팅을 위한 고정 IPv4 주소 
