# 1. GitOps
GitOps란 Git에 푸시된 코드를 기반으로 CICD의 일련의 단계를 정의하고 관리하는 선언적 방법이다. 
Git에 인프라 및 애플리케이션의 모든 정의를 정의하고 이를 단일 정보 소스로 사용한다.
# 2. OpenShift GitOps
OpenShift GitOps는 ArgoCD를 사용하여 GitOps를 구현한다. ArgoCD는 Git에 정의된 K8S 메니페스트들(yaml파일들)을
지속적으로 감시하며 변경사항이 감지되면 자동 혹은 수동으로 클러스터와 싱크를 맞춘다.
## 2.1 어플리케이션 생성
웹콘솔 또는 Application CRD를 정의해서 어플리케이션을 생성할 수 있다.
### 2.1.1 Application 리소스 정의
Application은 ArgoCD에서 어플리케이션을 정의하는 CRD이다.
```
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: spring-petclinic
  namespace: openshift-gitops
spec:
  destination: # 목적지 클러스터 설정
    namespace: spring-test-application
    server: 'https://kubernetes.default.svc'
  ignoreDifferences: # 컨테이너이미지는 이미지스트림을 통해 정의되므로 비교에서 제외
    - group: apps
      jsonPointers:
        - /spec/template/spec/containers
      kind: Deployment
  project: default
  source: # Git 레파지토리 설정
    path: ./k8s
    repoURL: >-
      http://gitea-spring-test-application.apps.dev.honecloud.co.kr/devopsdt/spring-petclinic.git
    targetRevision: HEAD
```
### 2.1.1 웹콘솔을 통한 어플리케이션 생성
