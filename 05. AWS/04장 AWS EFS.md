## Amazon EFS(Elastic File System)
스토리지를 프로비저닝하거나 관리하지 않고도, 파일 데이터를 공유할 수 있게 해주는 간단하고 한 번만 설정하면 되는 탄력적 서버리스 파일 시스템

- AWS 클라우드 서비스 및 온프레미스 리소스와 함께 사용 가능하며, 애플리케이션 중단 없이 온프레미스에 페타바이트 규모로 확장 가능

- 파일 추가 및 제거할 때 파일 시스템을 자동으로 확장 및 축소 가능하며, 확장을 위해 용량을 프로비저닝하고 관리할 필요 없음

- Standard 또는 One Zone 스토리지 클래스를 사용하여 파일 시스템을 생성할 수 있는 선택 옵션 제공

- Standard 스토리지 클래스는 다중 AZ 전반에 데이터를 저장

- One Zone 스토리지 클래스는 Standard 스토리지 클래스를 사용하는 파일 시스템에 비해 47% 낮은 비용으로 단일 AZ 내에 데이터를 중복 저장
  - Standard - Amazon EFS Standard, Amazon EFS Standard-IA(Amazon EFS Standard-Infrequent Access)

  - One Zone - Amazon EFS One Zone, Amazon EFS One Zone-IA(Amazon EFS One Zone-Infrequent Access)

- 기존 온프레미스에서 사용하던 NFS, NAS와 동일한 서비스

- 간단하고 확장 가능하며 안정적인 클라우드 파일 스토리지

- NFS Protocol 사용

- EFS 생성 시, VPC 안의 AZ Subnet에 Mount Target 생성(AZ의 Subnet 하나만 Mount Target 생성)

- Linux 인스턴스에만 사용가능(Windows 지원 x. 사용하고 싶을 경우 Linux SAMBA 연동)

<br/>

### Amazon EFS 장점
#### 간편성
- 빠르고 쉽게 구성 가능

- 복잡한 파일시스템을 배포 및 배치 불필요

#### 자동확장
- 파일이 추가 또는 제거됨에 따라 자동으로 확장 및 축소

- 필요한 만큼 자동으로 스토리지 공간 확보

#### 공유 파일 스토리지
- 수 천대 EC2 동시 Access 가능

- 온프레미스 서버 Access 가능

#### 원활한 통합
- NFSv4 Protocol을 통해 EFS 파일 시스템 탑재

- Direct Connect & VPN을 통해 온프레미스 서버와 탐재

#### 높은 가용성 및 내구성 제고

#### 보안
- POSIX 권한을 통해 파일시스템에 대한 액세스 제어

- IAM을 통해 액세스 제어
