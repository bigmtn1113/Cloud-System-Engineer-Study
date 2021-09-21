## Amazon CloudFront
**CDN(Content Delivery Network) 서비스**

캐싱을 통해 사용자에게 좀 더 빠른 전송 속도를 제공함을 목적으로 함  
전 세계 이곳저곳에 Edge Location을 두고 Client에 가장 가까운 Edge Location을 찾아 Latency를 최소화시켜 빠른 데이터 액세스 제공

<br/>

### Origin Server
**원본 데이터를 갖고 있는 서버**

보통 AWS에서의 Origin Server는 S3, EC2 인스턴스

<br/>

### Edge Location
**AWS에서 실질적으로 제공하는 전 세계에 퍼져 있는 서버**

같은 요청에 대해서 빠르게 응답해주기 위해 Cache 기능 제공

<br/>

### 동작 원리
**1\)** 클라이언트로부터 Edge Location으로 요청이 발생

**2\)** Edge Location은 요청이 발생한 데이터에 대해 캐싱 여부 확인

**3-1\)** 사용자의 근거리에 위치한 Edge Location 중 캐싱 데이터가 존재한다면 사용자의 요청에 맞는 데이터를 응답

**3-2\)** 사용자의 요청에 적합한 데이터가 캐싱되어 있지 않은 경우 Origin Server로 요청이 포워딩

**4\)** Origin Server는 Edge Location에 캐싱 데이터를 생성하고, 클라이언트로 응답
