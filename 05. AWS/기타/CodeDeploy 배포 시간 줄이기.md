## CodeDeploy 배포 시간 줄이기

### 방법
- **Auto Scaling Group**의 상태 확인 유예 기간 변경
- **Target Group**의 등록 취소 지연 시간 변경
- **CodeDeploy**의 배포 구성 변경 또는 생성

<br/>

### Auto Scaling Group
#### Health check grace period(상태 확인 유예 기간)
- EC2 Auto Scaling이 새 인스턴스가 서비스 상태가 된 후 새 인스턴스에 대한 첫 번째 상태 확인을 수행할 때까지의 시간

<br/>

### Target Group
#### Deregistration delay(등록 취소 지연)
- ELB는 등록 취소 중인 대상으로 요청을 전송하는 것을 중지함  
- 등록 취소 프로세스를 완료하기 전에 기본적으로 300초 동안 대기  
- 등록 취소하는 대상의 초기 상태는 draining  
- 등록 취소 지연이 경과한 후, 등록 취소 프로세스가 완료되며 대상 상태는 unused가 됨

<br/>

### CodeDeploy
#### Deployment configuration(배포 구성)
- **CodeDeployDefault.AllAtOnce**
  - 한 번에 가급적 많은 수의 인스턴스에 애플리케이션 개정을 배포  
  - 인스턴스가 하나라도 배포 성공하면 전체 배포 상태가 성공으로 표시  
  - 어떤 인스턴스에도 배포되지 않으면 전체 배포 상태가 실패로 표시

- **CodeDeployDefault.HalfAtATime**
  - 최대 절반의 인스턴스(분수는 반내림함)에 한번에 배포  
  - 인스턴스의 절반 이상(분수는 반올림함)에 배포되면 전체 배포 상태가 성공으로 표시  
  - 그렇지 않으면 배포 실패  
  - ex) 9개면 5개 이상이 배포에 성공해야 함  

- **CodeDeployDefault.OneAtATime**
  - 한 번에 한 인스턴스에만 애플리케이션 개정을 배포  
  - 모든 인스턴스에 배포되면 전체 배포 상태가 성공으로 표시  
  - 예외로 마지막 인스턴스가 실패했는데 성공으로 표시될 수 있음  
  - 이럴 경우엔 마지막 인스턴스를 제외한 나머지 모든 인스턴스에 배포되면 성공

<br/>

### ※ 참고
한번에 배포되는 비율을 임의로 설정한 배포 구성 생성 가능. ex) 70% 배포
