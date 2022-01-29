# Spring PetClinic Sample Application
오픈시프트에 배포하고 CICD를 구축하기 위한 K8S메니페스트 파일들이 k8s 디렉터리 하위에 있습니다.

## ArgoCD를 통한 애플리케이션 생성
```
git clone http://do-gitlab-ce.apps.apps.dev.honecloud.co.kr/root/spring-petclinic-ngw.git
oc create -f ./spring-petclinic-ngw/k8s/argocd/application-spring-petclinic.yaml
```
## pipeline 생성
```
oc create -f spring-petclinic/k8s/pipeline/task-maven-build.yaml
oc create -f spring-petclinic/k8s/pipeline/task-openshift-cli-workspaces.yaml
oc create -f spring-petclinic/k8s/pipeline/pipeline-docker-build-and-deploy-pr.yaml
```
