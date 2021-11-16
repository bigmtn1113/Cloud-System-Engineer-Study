## Amazon RDS
**완전 관리형 서비스**  
스키마 구조가 복잡한 데이터를 사용하는 애플리케이션  
여러 데이터 세트를 결합하고 연결해야 하는 애플리케이션  
특정한 포맷 형식의 구문 규칙을 만족하는 경우만 입력 되도록 설계가 필요한 애플리케이션

- MySQL, MariaDB, PostgreSQL, Oracle는 최대 16TB

- MySQL 및 PostgreSQL용 Aurora는 최대 64TB

<br/>

### Amazon DB 선정 시 고려 사항
#### 확장성(완전 관리형 서비스)
- 처리량. 초당/분당 트랜잭션

- 추후 확장가능 여부

#### 스토리지 요구 사항
- DB 크기

- Data 용량 단위

- ALL flash-Disk(SSD)

- IOPS/ MBPS

#### 내구성
- 데이터 내구성, 데이터 가용성 및 복구성 수준

- 관련 규제 의무

<br/>

### DB 유형
#### 관계형
**정형 데이터 관리**

엄격한 스키마 규칙 및 데이터 품질 적용이 필요  
DB가 과도한 R/W 용량을 필요로 하지 않음  
- Amazon Aurora, MS SQL Server, Oracle DB, MYSQL, MariaDB, PostgreSQL, ...

#### 비관계형
**비정형 데이터 관리**

데이터가 기존 스키마에 적합하지 않음  
R/W 속도가 기존 SQL DB에서 경제적으로 지원할 수 있는 범위 초과  
- Amazon DynamoDB, MongoDB, Cassndra, Elasticsearch, Redis

#### 관계형 vs 비관계형
항목 | 관계형 | 비관계형
:---: | :---: | :---:
 데이터 스토리지 | 행 및 열 | 키 값, 문서 및 그래프
스키마 | 고정 | 동적
쿼리 | SQL 기반 | 문서(document) 수집에 집중
확장성 | 수직적 | 수평적
워크로드 | 일관성이 뛰어난 OLTP, OLAP | 낮은 지연 시간의 애플리케이션을 포함한 수많은 데이터 액세스 패턴

#### NoSQL DB 장점
- 유연성
  - 유연한 스키마 제공으로 인해 빠르고 반복적인 개발 가능

- 확장성
  - 고가의 강력한 서버를 추가하는 대신 분산형 H/W 클러스터를 이용해 확장하도록 설계

- 고성능
  - 특정 데이터 모델 및 액세스 패턴에 최적화

- 고가용성
  - 각 Data Model에 맞춰 특별히 구축된 뛰어난 기능의 API와 데이터 유형 제공

<br/>

### 유지 관리
#### 가용성 및 장애 조치
- Amazon RDS Multi-AZ 배포는 SQL Server 네이티브 미러링 기술과 같은 동기식 논리적 복제를 사용하여 대기 복제본의 데이터를 최신 형태로 유지

- 가용 영역 장애 또는 DB 인스턴스 장애가 발생하는 경우 데이터를 보호할 수 있으며, 해당 옵션은 별도의 추가 비용 없이 DB 인스턴스를 사용한 시간만큼 요금 부과

- 별도의 복제 및 백업 솔류션 없이도 기본적으로 향상된 가용성 및 내구성 제공

- Multi-AZ 옵션 사용 시, Amazon RDS는 자동으로 하나의 기본 DB 인스턴스를 생성하고 동시에 다른 가용 영역의 예비 인스턴스에 데이터를 복제

- 인프라에 장애가 발생하면 생성된 예비 인스턴스(또는 Read Replica)로 자동 장애 조치를 수행하여 관리자가 직접 개입할 필요 없이 DB 작업을 바로 재개

#### 자동 백업
- DB 인스턴스를 특정 시점으로 복구 가능

- Amazon RDS는 기본 백업 기간내 매일 자동으로 데이터에 대한 완전한 스냅샷을 생성하고, DB 인스턴스를 업데이트 시 트랜잭션 로그를 캡처

- 특정 시점으로 복구할 때, DB 인스턴스를 사용자가 요청한 특정 시점으로 복구하기 위해 가장 적합한 일일 백업에 트랜잭션 로그 적용

- RDS는 사용자가 지정한 보존 기간(기본적으론 1일, 최대 35일)동안 DB 인스턴스의 백업 보관

#### 암호화
- 인스턴스 뿐만 아니라 인스턴스의 기본 스토리지, 자동 백업 파일, 읽기 전용 복제본 및 스냅샷의 데이터 모두를 암호화하며 키 관리를 위해 AWS Key Management Service와 자동으로 통합

- 별도의 보안 솔루션 없이도 AWS KMS와 연계하여 암호화 설정 가능

#### 모니터링
- RDS용 Enhanced Monitoring RDS 인스턴스 상태에 대한 가시성 제공

- RDS DB 인스턴스에 대한 "Enganced Monitoring" 옵션을 활성화하고 시간 단위 설정

- Enhanced Monitoring에서 정의된 시간 단위에 따라 주요 운영 체제 지표를 수집하고 정보 처리 수행

#### Performance insight
- 데이터베이스의 로드를 평가하고 시각화하여 성능 문제 탐지

- 애플리케이션의 성능에 영향을 미치지 않는 가벼운 데이터 수집 방법을 사용하고 어떤 SQL 명령문이 부하를 일으키는지 그 이유와 지표 확인 가능

<br/>

### Read Replica(읽기 전용 복제본)
읽기 시간을 줄이기 위해 근접한 Region에 읽기 전용 복제본 생성

#### 복제 시, 트래픽 비용
- 리전이 다르기 때문에 원본 AWS 리전을 벗어나 데이터를 전송하면 데이터 트래픽 비용 발생

#### 읽기 개수의 제한
- AWS MySQL DB 인스턴스당 5개까지 읽기 전용 복제본 생성 가능

- 추가 읽기 전용 복제본 확장이 필요하면 복제본을 사용해 읽기 전용으로 복제

#### 읽기 전용 복제본 삭제
- 각 복제본은 독립 실행형 DB 인스턴스로 승격

<br/>

### Amazon Aurora DB
MySQL 및 PostgreSQL과 호환되는 완전 관리형 관계형 데이터베이스

- 기존보다 MySQL은 처리량을 5배, PostgreSQL은 최대 3배까지 성능 증가 가능

- cluster endpoint(각 instance의 endpoint)를 보유

- 각각의 인스턴스에 대한 진단을 개별적으로 가능

- 인스턴스별로 발생하는 문제인지 클러스터에서 전체적으로 발생하는 문제인지 확인하고 진단 가능

- 각 인스턴스의 endpoint를 사용해 인스턴스의 크기를 다르게 하거나 다른 parameter group을 설정하거나 다르게 구성 가능

- Aurora Cluster는 한 개 이상의 DB Instance와 Cluster Volume으로 구성

- Cluster Volume은 Virtual DB Storage Volume으로 각 가용 영역에 데이터의 사본으로 존재
  - **Primary Instance:** 읽기 및 쓰기 작업 지원. 클러스터 볼륨의 모든 데이터 수정을 실행

  - **Replica Instance:** 읽기 작업 지원. 최대 15개까지 복제본 가능. Fail-over시 우선순위 지정 가능

#### ※ Amazon RDS MySQL과 Amazon Aurora MySQL 차이
- **Storage:** 전자는 자체 EBS로 운영하지만 후자는 Shared Storage를 사용

- **Management:** 전자는 관리자가 RDS MySQL의 버전을 올리면서 사용하지만 후자는 AWS가 개발해서 버전 업그레이드를 주기적으로 하기 때문에 optional 또는 mandatory가 AWS에 의해 결정

- **Read Replica 구성:** 전자는 standby와 read replica를 만들 때 binlog를 사용하지만 후자는 내부 Storage 및 redo log 전송을 통해 빠른 동기화가 가능하면 bandwidth 감소가 가능
