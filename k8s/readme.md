# 1. BuildConfig
BuildConfig는 Openshift의 CRD이며 이미지 build, 레지스트리 push, deploy의 과정을 자동화해준다

```
kind: BuildConfig
apiVersion: build.openshift.io/v1
metadata:
  name: spring-petclinic
  namespace: spring-test-application

...

spec:
  output: # 빌드 성공 후 푸시될 레파지토리 정의
    to:
      kind: ImageStreamTag
      name: 'spring-petclinic:latest'
  strategy: # 빌드 전략 정의 (s2i, docker 등)
    dockerStrategy:
      dockerfilePath: Dockerfile
    type: Docker
  source: # 소스 정의 (git, binary 등)
    git:
      uri: http://gitea-spring-test-application.apps.dev.honecloud.co.kr/devopsdt/spring-petclinic.git

```
## 1.1 빌드소스
### 1.1.1 Dockerfile 소스
Dockerfile 스크립트를 직접 제공하는 형태

```
spec:
  source: 
    dockerfile: "FROM registry.dev.honecloud.co.kr:5000/openjdk:11.0.1-jre-slim-stretch\nEXPOSE 8080\nARG JAR=spring-petclinic-2.5.0-SNAPSHOT.jar\nCOPY ./target/$JAR /app.jar\nENTRYPOINT ["java","-jar","/app.jar"]"
```
### 1.1.2 이미지 소스
미리 정의된 이미지로 부터 파일을 추가할 수 있다
```
spec:
  source:
    images:
    - from:
        kind: ImageStreamTag
        name: spring-petclinic:latest
      paths:
      - destinationDir: injected/dir
        sourcePath: /usr/lib/tmp.jar
```
### 1.1.3 Git 소스
Git으로부터 소스를 받아온다
```
spec:
  source:
    git:
      uri: http://gitea-spring-test-application.apps.dev.honecloud.co.kr/devopsdt/spring-petclinic.git
```

## 1.2 빌드전략
### 1.2.1 Docker 빌드
Docker 빌더를 이용해 이미지를 빌드하고 배포한다. 

```
spec:
  strategy:
    dockerStrategy:
      dockerfilePath: Dockerfile
    type: Docker
```
### 1.2.2 S2I(Source to Image) 빌드
application 소스로부터 컨테이너 이미지를 빌드해주는 방식

S2I 베이스 이미지에는 application 빌드, 실행에 필요한 모든 소스를 포함하고 있으며 아래 나열한 스크립트 또한 정의되어야 한다.
그러면 스크립트가 순차적으로 실행되며 이미지가 생성된다.
- assemble: 어플리케이션을 빌드하고 적절한 위치에 이동시킴
- run: 빌드된 어플리케이션을 실행
- save-artifacts: 빌드에 필요한 artifacts를 수집한다. (예를 들어 메이븐빌드의 경우 .m2폴더)
- usage: 설명서
- test/run: 테스트 스크립트
```
spec:
  strategy:
    sourceStrategy:
      from:
        kind: "ImageStreamTag"
        name: "builder-image:lastest"
```
