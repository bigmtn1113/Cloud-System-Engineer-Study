## CloudFormation
### 업무 자동화
사람이 관리 작업을 하면 실수가 발생할 수 있고, 시간도 많이 걸리며 항상 동일하게 작업되어야 하는 것을 보장 받기 어렵다.  
이 뿐만 아니라 확장 불가, 버전 제어 불가, 감사 내역 부족, 일관성 없는 데이터 관리 등의 여러 위험이 따른다.

#### ※ 멱등성
연산을 여러 번 적용하더라도 결과가 달라지지 않는 성질

<br/>

### Template
생성할 리소스를 설명하는 JSON/YAML 형식 파일  
전체 애플리케이션 스택 정의

- 인프라 관리 최소화

- 모든 리소스와 해당 속성에 대한 설정 한번에 가능

- 리소스를 단 한곳에서만 관리하면 되므로 전체적인 흐름 파악이 쉽고 변경 작업도 용이

- 코드만 버전관리한다면 초기부터 인프라 변경 과정 추적 가능

- 템플릿을 코드로 취급하고 원하는 버전 제어 방법(Git, SVN, ...)으로 이를 관리

#### Template 구조
- AWSTemplateFormatVersion
  - 현재 유일한 유효 값은 2010-09-09

- Description(OPTIONAL)
  - 템플릿을 설명하는 섹션

- Metadata(OPTIONAL)
  - 템플릿에 대한 세부 정보 기술

- Parameters(OPTIONAL)
  - 스택을 생성할 때 필요한 파라미터 기술

  - Resources와 Outputs에서 사용

- Mappings(OPTIONAL)
  - key-value 형식의 map 선언

  - 키는 문자열, 값은 문자열이나 list

  - Fn::FindInMap 내장 함수를 사용하여 맵에서 값 호출

- Conditions(OPTIONAL)
  - 스택이 프로덕션용인지 테스트 환경용인지에 따라 달라지는 리소스를 조건부로 생성

- Transform(OPTIONAL)
  - 서버리스 애플리케이션의 경우 사용할 AWS SAM(Serverless Application Model) 버전

- Resources
  - AWS 리소스 및 속성 설정

- Outputs(OPTIONAL)
  - 템플릿 하나에 여러가지 리소스 구성 가능

#### Stack
CloudFormation가 리소스를 관리하는 하나의 단위

- 스택 단위로 인프라 생성 및 제거 가능

- 스택에서 관리하는 모든 리소스는 Template로만 정의

- 스택을 생성할 때마다 CloudFormation이 템플릿에 작성된 리소스를 프로비저닝 수행

- 스택을 생성하려면 해당 인스턴스를 생성할 권한이 필요하므로 IAM을 이용해 권한 설정 필요

- 스택의 리소스를 수정하기 위해 새로운 스택 생성 불필요

<br/>

### CloudFormation designer
CloudFormation Template 생성, 보기 및 수정을 위한 그래픽 도구

- 끌어 놓기 인터페이스를 사용해 템플릿 리소스 다이어그램 생성 가능
- 통합 JSON 및 YAML 편집기를 사용하여 세부 정보 편집 가능
