# 로컬 클러스터 구성
## 로컬 클러스터 설치 및 구동
1. https://console.redhat.com/openshift/create/local 접속 후 pkg 및 secret 다운
2. 명령어 실행하여 cluster start
```
crc start
INFO Checking if running as non-root
INFO Checking if crc-admin-helper executable is cached
INFO Checking for obsolete admin-helper executable
INFO Checking if running on a supported CPU architecture
INFO Checking minimum RAM requirements
INFO Checking if running emulated on a M1 CPU
INFO Checking if HyperKit is installed
INFO Checking if qcow-tool is installed
INFO Checking if crc-driver-hyperkit is installed
CodeReady Containers requires a pull secret to download content from Red Hat.
You can copy it from the Pull Secret section of https://cloud.redhat.com/openshift/create/local.
? Please enter the pull secret ********************************************************************************************************************************************************

```
## cli 명령어로 웹콘솔 접속하기
```
crc console
```
## 비번확인
```
crc console --credentials
To login as a regular user, run 'oc login -u developer -p developer https://api.crc.testing:6443'.
To login as an admin, run 'oc login -u kubeadmin -p cuHsu-VqEAe-8Uchc-frTyh https://api.crc.testing:6443'
```
# oc 설치
```
brew install openshift-cli
```

