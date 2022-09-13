---
title: "Integrate Harbor into Pipelines"
keywords: 'Kubernetes, Docker, DevOps, Jenkins, Harbor'
description: 'Integrate Harbor into your pipeline to push images to your Harbor registry.'
linkTitle: "Integrate Harbor into Pipelines"
weight: 11320
---

이 튜토리얼은 Harbor를 Kuberix Enterprise 파이프라인에 통합하는 방법을 보여줍니다.

## 전제 조건

- [Kuberix Enterprise DevOps 시스템 활성화](../../../pluggable-components/devops/)가 필요합니다.
- 작업 공간, DevOps 프로젝트 및 사용자(`project-regular`)를 생성해야 합니다. 이 계정은 '운영자' 역할로 DevOps 프로젝트에 초대되어야 합니다. 준비되지 않은 경우 [작업 공간, 프로젝트, 사용자 및 역할 만들기](../../../quick-start/create-workspace-and-project/)를 참조하십시오.

## 하버 설치

[Kuberix Enterprise의 앱 스토어](../../../application-store/built-in-apps/harbor-app/)를 통해 하버를 설치하는 것이 좋습니다. 또는 Helm3를 통해 Harbour를 수동으로 설치하십시오.

```bash
helm repo add harbor https://helm.goharbor.io
# For a quick start, you can expose Harbor by nodeport and disable tls.
# Set externalURL to one of your node ip and make sure it can be accessed by jenkins.
helm install harbor-release harbor/harbor --set expose.type=nodePort,externalURL=http://$ip:30002,expose.tls.enabled=false
```

## Harbor 자격 증명 받기

1. Harbor가 설치된 후 `<NodeIP>:30002`를 방문하여 기본 계정과 비밀번호(`admin/Harbor12345`)로 콘솔에 로그인합니다. 왼쪽 탐색 창에서 **Projects**를 클릭하고 **Projects** 페이지에서 **NEW PROJECT**를 클릭합니다.

2. 표시된 대화 상자에서 이름(`ke-devops-harbor`)을 설정하고 **OK**을 클릭합니다.

3. 방금 생성한 프로젝트를 클릭하고 **Robot Accounts** 탭에서 **NEW ROBOT ACCOUNT**를 클릭합니다.

4. 표시된 대화 상자에서 로봇 계정의 이름(`robot-test`)을 설정하고 **SAVE**을 클릭합니다. **Permissions**에서 아티팩트 푸시 확인란을 선택했는지 확인합니다.

5. 표시된 대화 상자에서 **EXPORT TO FILE**를 클릭하여 토큰을 저장합니다.

## 안전하지 않은 레지스트리 활성화

Harbour 레지스트리에 대한 보안을 무시하도록 Docker를 구성해야 합니다.

1. 호스트에서 `vim /etc/docker/daemon.json` 명령을 실행하여 `daemon.json` 파일을 편집하고 다음 내용을 입력하고 변경 사항을 저장합니다.

   ```json
   {
     "insecure-registries" : ["103.61.38.55:30002"]
   }
   ```

   {{< notice note >}}

   Make sure you replace `103.61.38.55:30002` with your Harbor registry address. The default location of the `daemon.json` file is `/etc/docker/daemon.json` on Linux or `C:\ProgramData\docker\config\daemon.json` on Windows.

   {{</ notice >}}

2. Run the following commands to restart Docker for the changes to take effect.

   ```bash
   sudo systemctl daemon-reload
   sudo systemctl restart docker
   ```

   {{< notice note >}}

   이 솔루션은 격리된 테스트나 엄격하게 제어되는 에어 갭 환경에서 사용하는 것이 좋습니다. 자세한 내용은 [일반 HTTP 레지스트리 배포](https://docs.docker.com/registry/insecure/#deploy-a-plain-http-registry)를 참조하세요. 위 작업을 완료한 후 프로젝트에 워크로드를 배포할 때 Harbor 레지스트리의 이미지를 사용할 수도 있습니다. Harbour 레지스트리에 대한 이미지 Secret을 생성한 다음 Harbour 레지스트리를 선택하고 **Container Image** 탭 아래의 **Container Settings**에서 이미지의 절대 경로를 입력하여 이미지를 검색해야 합니다.

   {{</ notice >}}

## 자격 증명 생성

1. KubeSphere에 `project-regular`로 로그인하고 DevOps 프로젝트로 이동하여 **DevOps Project Settings** 아래의 **Credentials**에서 Harbor에 대한 자격 증명을 생성합니다.

2. **Create Credentials** 페이지에서 자격 증명 ID(`robot-test`)를 설정하고 **Type**에 대해 **Username and password**를 선택합니다. **Username** 필드는 방금 다운로드한 JSON 파일의 '이름' 값과 같아야 하며 **Password/Token**에 대한 파일의 '토큰' 값을 입력해야 합니다.

3. **OK**을 클릭하여 저장합니다.

## 파이프라인 생성

1. **Pipelines** 페이지로 이동하여 **Create**를 클릭합니다. **Basic Information** 탭에서 파이프라인의 이름(`demo-pipeline`)을 입력하고 **Next**를 클릭합니다.

2. **Advanced Settings**에서 기본값을 사용하고 **Create**를 클릭합니다.

## 젠킨스 파일 수정

1. 파이프라인을 클릭하여 세부 정보 페이지로 이동하고 **Edit Jenkinsfile**을 클릭합니다.

2. 다음 내용을 복사하여 Jenkinsfile에 붙여넣습니다. 'REGISTRY', 'HARBOR_NAMESPACE', 'APP_NAME' 및 ​​'HARBOR_CREDENTIAL' 값을 자신의 값으로 바꿔야 합니다.

   ```groovy
   pipeline {  
     agent {
       node {
         label 'maven'
       }
     }
     
     environment {
       // the address of your harbor registry
       REGISTRY = '103.61.38.55:30002'
       // the project name
       // make sure your robot account have enough access to the project
       HARBOR_NAMESPACE = 'ke-devops-harbor'
       // docker image name
       APP_NAME = 'docker-example'
       // ‘robot-test’ is the credential ID you created on the Kuberix Enterprise console
       HARBOR_CREDENTIAL = credentials('robot-test')
     }
     
     stages {
       stage('docker login') {
         steps{
           container ('maven') {
             // replace the Docker Hub username behind -u and do not forget ''. You can also use a Docker Hub token. 
             sh '''echo $HARBOR_CREDENTIAL_PSW | docker login $REGISTRY -u 'robot$robot-test' --password-stdin'''
               }
             }  
           }
           
       stage('build & push') {
         steps {
           container ('maven') {
             sh 'git clone https://github.com/kstaken/dockerfile-examples.git'
             sh 'cd dockerfile-examples/rethinkdb && docker build -t $REGISTRY/$HARBOR_NAMESPACE/$APP_NAME:devops-test .'
             sh 'docker push  $REGISTRY/$HARBOR_NAMESPACE/$APP_NAME:devops-test'
             }
           }
         }
       }
     }
   
   
   ```

   {{< notice note >}}

   환경 변수가 있는 Jenkins 자격 증명을 통해 매개변수를 `docker login -u`에 전달할 수 있습니다. 그러나 모든 Harbor 로봇 계정의 사용자 이름에는 "\$" 문자가 포함되어 있으며 환경 변수에서 사용할 때 Jenkins에 의해 "\$$"로 변환됩니다. [자세히 알아보기](https://number1.co.za/rancher-cannot-use-harbor-robot-account-imagepullbackoff-pull-access-denied/).

   {{</ notice >}} 

## Run the Pipeline

Jenkinsfile을 저장하면 Kuberix Enterprise가 그래픽 편집 패널에 모든 단계와 단계를 자동으로 생성합니다. **Run**을 클릭하여 파이프라인을 실행합니다. 모든 것이 잘되면 이미지가 Jenkins에 의해 Harbour 레지스트리로 푸시됩니다.

