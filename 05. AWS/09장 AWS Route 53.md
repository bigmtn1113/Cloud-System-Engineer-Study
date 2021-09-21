## Amazon Route 53
**Amazon Route 53는 높은 가용성과 확장성이 뛰어난 클라우드 Domain Name System(DNS) 웹 서비스**

- 사용자를 인터넷 애플리케이션으로 라우팅할 수 있는 매우 안정적이고 비용 효율적인 방법을 개발자와 기업에 제공하기 위해 설계

- 사용자의 요청을 AWS에서 실행되는 인프라에 효과적으로 연결

- 사용자를 AWS 외부의 인프라로 라우팅할때도 사용

- 트래픽을 정상적인 엔드포인트로 라우팅하거나 애플리케이션 및 해당 엔드포인트의 상태를 개별적으로 모니터링하도록 DNS 상태 확인 구성 가능

<br/>

### Routing Policy
#### Simple routing policy(단순 라우팅 정책)
- 도메인에 대해 특정 기능을 수행하는 하나의 리소스만 있는 경우에 사용

#### Failover routing policy(장애 조치 라우팅 정책)
- Active - Passive 장애 조치를 구성하려는 경우에 사용

#### Geolocation routing policy(지리 위치 라우팅 정책)
- 사용자의 위치에 기반하여 트래픽 라우팅하려는 경우에 사용

#### Geoproximity routing policy(지리 근접 라우팅 정책)
- 리소스의 위치를 기반으로 트래픽을 라우팅

- 필요에 따라 한 위치의 리소스에서 다른 위치의 리소스로 트래픽을 보내려는 경우에 사용

#### Latency routing policy(지연 시간 라우팅 정책)
- 여러 AWS 리전에 리소스가 있고 최상의 지연 시간을 제공하는 리전으로 트래픽을 라우팅하려는 경우에 사용

#### Multivalue answer routing policy(다중 응답 라우팅 정책)
- DNS 쿼리에 무작위로 선택된 최대 8개의 정상 레코드로 응답하게 하려는 경우에 사용

#### Weighted routing policy(가중치 기반 라우팅 정책)
- 사용자가 지정하는 비율에 따라 여러 리소스로 트래픽을 라우팅하려는 경우에 사용
