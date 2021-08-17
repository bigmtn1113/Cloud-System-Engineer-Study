# Dockerfile을 사용한 코드에 의한 서버 구축

---

## Dockerfile을 사용한 구성 관리
### Dockerfile
**Docker 상에서 작동시킬 컨테이너의 구성 정보를 기술하기 위한 파일**  
텍스트 형식의 파일이며 확장자는 필요 없다.

#### 컨테이너 구성정보
- 베이스가 될 Docker 이미지
- Docker 컨테이너 안에서 수행한 명령
- 환경변수 등의 설정
- Docker 컨테이너 안에서 작동시켜둘 데몬 실행

<br/>

### Dockerfile의 기본 구문
#### 기본 서식
명령 인수

#### 주요 Dockerfile 명령
명령 | 설명
--- | ---
FROM | 베이스 이미지 지정
RUN | 명령 실행
CMD<br/>ENTRYPOINT | 컨테이너 실행 명령
EXPOSE | 포트 익스포트
ENV | 환경변수
ADD | 파일/디렉터리 추가
COPY | 파일 복사
VOLUME | 볼륨 마운트
WORKDIR | 작업 디렉터리
