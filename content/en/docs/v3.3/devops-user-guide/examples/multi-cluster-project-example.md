---
title: "Deploy Apps in a Multi-cluster Project Using a Jenkinsfile"
keywords: 'Kubernetes, Kuberix, Docker, DevOps, Jenkins, Multi-cluster'
description: 'Learn how to deploy apps in a multi-cluster project using a Jenkinsfile-based pipeline.'
linkTitle: "Deploy Apps in a Multi-cluster Project Using a Jenkinsfile"
weight: 11420
---

## 전제 조건

- [멀티 클러스터 기능을 활성화](../../../multicluster-management/)하고 여러 클러스터로 작업 공간을 만들어야 합니다.
- [Docker Hub](https://hub.docker.com/) 계정이 있어야 합니다.
- 호스트 클러스터에서 [Kuberix Enterprise DevOps System을 활성화](../../../pluggable-components/devops/)해야 합니다.
- 호스트 클러스터에서 멀티 클러스터 프로젝트와 DevOps 프로젝트를 생성하려면 `workspace-self-provisioner` 역할을 가진 사용자(예: `project-admin`)를 사용해야 합니다. 이 튜토리얼은 호스트 클러스터와 하나의 멤버 클러스터에 멀티 클러스터 프로젝트를 생성합니다.
- DevOps 프로젝트에 사용자(예: 'project-regular')를 초대하고 'operator' 역할을 부여해야 합니다. 자세한 내용은 [작업 공간, 프로젝트, 사용자 및 역할 생성](../../../quick-start/create-workspace-and-project/), [멀티 클러스터 관리](../. ./../multicluster-management/) 및 [멀티 클러스터 프로젝트](../../../project-administration/project-and-multicluster-project/#multi-cluster-projects).

## 도커 허브 액세스 토큰 생성

1. [Docker Hub](https://hub.docker.com/)에 로그인한 후, 우측 상단의 내 계정을 클릭한 후, 메뉴에서 **Account Settings**을 선택합니다.

2. 왼쪽 탐색 창에서 **Security**을 클릭한 다음 **New Access Token**을 클릭합니다.

3. 표시된 대화 상자에서 토큰 이름(`go-project-token`)을 입력하고 **Create**를 클릭합니다.

4. **Copy and Close**를 클릭하고 액세스 토큰을 저장했는지 확인합니다.

## 자격 증명 생성

파이프라인이 이미지를 푸시하기 위해 Docker Hub와 상호 작용할 수 있도록 생성된 액세스 토큰에 대해 Kuberix Enterprise에서 자격 증명을 생성해야 합니다. 게다가 쿠버네티스 클러스터에 액세스하기 위한 kubeconfig 자격 증명도 생성해야 합니다.

1. Kuberix Enterprise의 웹 콘솔에 'project-regular'로 로그인합니다. DevOps 프로젝트에서 **DevOps 프로젝트 설정** 아래의 **자격 증명**으로 이동한 다음 **자격 증명** 페이지에서 **만들기**를 클릭합니다.

2. 표시된 대화 상자에서 나중에 Jenkinsfile에서 사용할 **이름**을 설정하고 **유형**에 **사용자 이름 및 암호**를 선택합니다. **Username**에 Docker Hub 계정 이름을 입력하고 **Password/Token**에 대해 방금 생성한 액세스 토큰을 입력합니다. 완료되면 **확인**을 클릭합니다.

   {{< notice tip >}}

   자격 증명 생성 방법에 대한 자세한 내용은 [자격 증명 관리](../../../devops-user-guide/how-to-use/devops-settings/credential-management/)를 참조하십시오.

   {{</ notice >}} 

3. Kuberix Enterprise 웹 콘솔에서 로그아웃하고 'project-admin'으로 다시 로그인합니다. DevOps 프로젝트로 이동하여 **Credentials**에서 **Create**를 클릭합니다. **Type**에 대해 **kubeconfig**를 선택합니다. Kuberix Enterprise는 현재 계정의 kubeconfig인 **Content** 필드를 자동으로 채웁니다. **Name**을 설정하고 **OK**을 클릭합니다.
   
   {{< notice note >}}
   
   향후 릴리스에서는 'project-regular' 계정을 멀티 클러스터 프로젝트에 초대하고 kubeconfig 자격 증명을 생성하는 데 필요한 역할을 부여할 수 있습니다.
   
   {{</ notice >}}

## 파이프라인 생성

위의 자격 증명이 준비되면 'project-regular' 사용자를 사용하여 아래와 같이 예제 Jenkinsfile로 파이프라인을 만들 수 있습니다.

1. 파이프라인을 생성하려면 **Pipelines** 페이지에서 **Create**을 클릭합니다.

2. 표시된 대화 상자에서 이름을 설정하고 **Next**을 클릭합니다.

3. 이 튜토리얼에서는 모든 필드에 기본값을 사용할 수 있습니다. **Advanced Settings** 탭에서 **Create**를 클릭합니다.

## 젠킨스 파일 수정

1. 파이프라인 목록에서 이 파이프라인을 클릭하여 세부 정보 페이지로 이동합니다. **Edit Jenkinsfile**을 클릭하여 Jenkinsfile을 정의하면 파이프라인이 이를 기반으로 실행됩니다.

2. 아래의 모든 콘텐츠를 파이프라인에 대한 예제 Jenkinsfile로 복사하여 표시된 대화 상자에 붙여넣습니다. `DOCKERHUB_USERNAME`, `DOCKERHUB_CREDENTIAL`, `KUBECONFIG_CREDENTIAL_ID`, `MULTI_CLUSTER_PROJECT_NAME` 및 `MEMBER_CLUSTER_NAME` 값을 자신의 값으로 바꿔야 합니다. 완료되면 **OK**을 클릭합니다.

   ```groovy
   pipeline {
     agent {
       label 'go'
     }
     
     environment {
       REGISTRY = 'docker.io'
       // Docker Hub username
       DOCKERHUB_USERNAME = 'Your Docker Hub username'
       APP_NAME = 'devops-go-sample'
       // ‘dockerhub’ is the Docker Hub credentials ID you created on the Kuberix Enterprise console
       DOCKERHUB_CREDENTIAL = credentials('dockerhub')
       // the kubeconfig credentials ID you created on the Kuberix Enterprise console
       KUBECONFIG_CREDENTIAL_ID = 'kubeconfig'
       // mutli-cluster project name under your own workspace
       MULTI_CLUSTER_PROJECT_NAME = 'demo-multi-cluster'
       // the name of the member cluster where you want to deploy your app
       // in this tutorial, the apps are deployed on host cluster and only one member cluster
       // for more member clusters, please edit manifest/multi-cluster-deploy.yaml
       MEMBER_CLUSTER_NAME = 'Your member cluster name'
     }  
     
     stages {
       stage('docker login') {
         steps {
           container('go') {
             sh 'echo $DOCKERHUB_CREDENTIAL_PSW  | docker login -u $DOCKERHUB_CREDENTIAL_USR --password-stdin'
           }
         }
       }
       
       stage('build & push') {
         steps {
           container('go') {
             sh 'git clone https://github.com/yuswift/devops-go-sample.git'
             sh 'cd devops-go-sample && docker build -t $REGISTRY/$DOCKERHUB_USERNAME/$APP_NAME .'
             sh 'docker push $REGISTRY/$DOCKERHUB_USERNAME/$APP_NAME'
           }
         }
       }
       
       stage('deploy app to multi cluster') {
         steps {
            container('go') {
               withCredentials([
                 kubeconfigFile(
                   credentialsId: env.KUBECONFIG_CREDENTIAL_ID,
                   variable: 'KUBECONFIG')
                 ]) {
                 sh 'envsubst < devops-go-sample/manifest/multi-cluster-deploy.yaml | kubectl apply -f -'
                 }
              }
           }
         }
       }
     }
   ```

   {{< notice note >}}

   파이프라인이 성공적으로 실행되면 이미지가 Docker Hub로 푸시됩니다. Harbor를 사용하는 경우 환경 변수가 있는 Jenkins 자격 증명을 통해 매개변수를 `docker login -u`에 전달할 수 없습니다. 이는 모든 Harbour 로봇 계정 사용자 이름에 `$` 문자가 포함되어 있기 때문입니다. 이 문자는 환경 변수에서 사용할 때 Jenkins에 의해 `$$`로 변환됩니다. [자세히 알아보기](https://number1.co.za/rancher-cannot-use-harbor-robot-account-imagepullbackoff-pull-access-denied/).

   {{</ notice >}} 

## 파이프라인 실행

Jenkinsfile을 저장한 후 **Run**을 클릭합니다. 모든 것이 잘 진행되면 다중 클러스터 프로젝트에 배포 워크로드가 표시됩니다.
