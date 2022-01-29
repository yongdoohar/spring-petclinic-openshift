# 1. OpenShift Pipeline
OpenShift Pipeline은 Tekton기반의 CICD workflow를 구현하는 솔루션이다
## 1.2 Pipeline
워크플로우를 정의한 CRD이다. 미리 정의된 Task/ClusterTask들의 조합으로 정의된다.
```
apiVersion: tekton.dev/v1beta1
kind: Pipeline
metadata:
  name: docker-build-and-deploy-pr
  namespace: spring-test-application
spec:
  params:
    - default: spring-petclinic
      name: IMAGE_NAME
      type: string
    - default: >-
        http://gitea-spring-test-application.apps.dev.honecloud.co.kr/devopsdt/spring-petclinic.git
      name: GIT_REPO
      type: string
    - default: main
      name: GIT_REVISION
      type: string
  tasks:
    - name: fetch-repository
      params: # 입력 파라미터 정의
        - name: url
          value: $(params.GIT_REPO)
        - name: revision
          value: $(params.GIT_REVISION)
        - name: deleteExisting
          value: 'true'
      taskRef: # 실행시킬 Task 정의
        kind: ClusterTask
        name: git-clone
      workspaces:
        - name: output
          workspace: workspace
    - name: maven-build
      runAfter: # runAfter속성으로 dependency 설정
        - fetch-repository
      taskRef:
        kind: Task
        name: maven-build
      workspaces:
        - name: source
          workspace: workspace
    
          ...
    
    
  workspaces: # 워크스페이스 정의
    - name: workspace
```
## 1.3 Task
Pipeline을 구성하는 단위이다. Task는 또한 Step들로 정의되며 각 Step들이 순차적으로 실행된다.
```
apiVersion: tekton.dev/v1beta1
kind: Task
metadata:
  name: maven-build
  namespace: spring-test-application
spec:
  steps:
    - args:
        - cd /tmp/src && mvn package -Dmaven.test.skip=true
      command:
        - /bin/bash
        - '-c'
      image: 'registry.dev.honecloud.co.kr:5000/maven:3.6.3-jdk-8'
      name: patch
      resources: {}
  workspaces:
    - mountPath: /tmp/src
      name: source
```

