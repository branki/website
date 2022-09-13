---
title: "Build and Deploy a Go Project"
keywords: 'Kubernetes, kuberix, docker, DevOps, Jenkins, Go'
description: 'Learn how to build and deploy a Go project using a Kuberix Enterprise pipeline.'
linkTitle: "Build and Deploy a Go Project"
weight: 11410
---

## 전제 조건

- [Kuberix Enterprise DevOps System을 활성화](../../../pluggable-components/devops/)해야 합니다.
- [Docker Hub](https://hub.docker.com/) 계정이 있어야 합니다.
- Workspace, DevOps 프로젝트, 프로젝트, 사용자(`project-regular`)를 생성해야 합니다. 이 계정은 DevOps 프로젝트와 'operator' 역할로 워크로드를 배포하는 프로젝트에 초대되어야 합니다. 자세한 내용은 [작업 공간, 프로젝트, 사용자 및 역할 생성](../../../quick-start/create-workspace-and-project/)을 참조하십시오.

## 도커 허브 액세스 토큰 생성

1. [Docker Hub](https://hub.docker.com/)에 로그인한 후, 우측 상단의 내 계정을 클릭한 후, 메뉴에서 **계정 설정**을 선택합니다.

2. 왼쪽 탐색 창에서 **보안**을 클릭한 다음 **새 액세스 토큰**을 클릭합니다.

3. 표시된 대화 상자에서 토큰 이름(`go-project-token`)을 입력하고 **만들기**를 클릭합니다.

4. **복사 후 닫기**를 클릭하고 액세스 토큰을 저장했는지 확인합니다.

## 자격 증명 생성

파이프라인이 이미징 푸시를 위해 Docker Hub와 상호 작용할 수 있도록 생성된 액세스 토큰에 대해 Kuberix Enterprise에서 자격 증명을 생성해야 합니다. 또한 쿠버네티스 클러스터에 액세스하기 위한 kubeconfig 자격 증명도 생성합니다.

1. Kuberix Enterprise의 웹 콘솔에 'project-regular'로 로그인합니다. DevOps 프로젝트에서 **DevOps Project Settings** 아래의 **Credentials**으로 이동한 다음 **Credentials** 페이지에서 **Create**를 클릭합니다.

2. 표시된 대화 상자에서 나중에 Jenkinsfile에서 사용할 **Name**을 설정하고 **Type**에 **Username and password**를 선택합니다. **Username**에 Docker Hub 계정 이름을 입력하고 **Password/Token**에 대해 방금 생성한 액세스 토큰을 입력합니다. 완료되면 **OK**을 클릭합니다.

   {{< notice tip >}}

자격 증명 생성 방법에 대한 자세한 내용은 [자격 증명 관리](../../../devops-user-guide/how-to-use/devops-settings/credential-management/)를 참조하십시오.

   {{</ notice >}}

3. **Create**를 다시 클릭하고 **Type**에 대해 **kubeconfig**를 선택합니다. Kuberix Enterprise는 현재 사용자 계정의 kubeconfig인 **Content** 필드를 자동으로 채웁니다. **Name**을 설정하고 **OK**을 클릭합니다.

## 파이프라인 생성

위의 자격 증명이 준비되면 아래와 같이 Jenkinsfile 예제를 사용하여 파이프라인을 만들 수 있습니다.

1. 파이프라인을 생성하려면 **Pipelines** 페이지에서 **Create**을 클릭합니다.

2. 표시된 대화 상자에서 이름을 설정하고 **Next**을 클릭합니다.

3. 이 튜토리얼에서는 모든 필드에 기본값을 사용할 수 있습니다. **Advanced Settings** 탭에서 **Create**를 클릭합니다.

## 젠킨스 파일 수정

1. 파이프라인 목록에서 파이프라인 이름을 클릭하여 세부 정보 페이지로 이동합니다. **Jenkinsfile 편집**을 클릭하여 Jenkinsfile을 정의하면 파이프라인이 이를 기반으로 실행됩니다.

2. 아래의 모든 콘텐츠를 파이프라인에 대한 예제 Jenkinsfile로 복사하여 표시된 대화 상자에 붙여넣습니다. `DOCKERHUB_USERNAME`, `DOCKERHUB_CREDENTIAL`, `KUBECONFIG_CREDENTIAL_ID` 및 `PROJECT_NAME` 값을 자신의 값으로 바꿔야 합니다. 완료되면 **OK**을 클릭합니다.

  ```groovy
  pipeline {
     agent {
       label 'go'
     }

     environment {
       // the address of your Docker Hub registry
       REGISTRY = 'docker.io'
       // your Docker Hub username
       DOCKERHUB_USERNAME = 'Docker Hub Username'
       // Docker image name
       APP_NAME = 'devops-go-sample'
       // 'dockerhubid' is the credentials ID you created in Kuberix Enterprise with Docker Hub Access Token
       DOCKERHUB_CREDENTIAL = credentials('dockerhubid')
       // the kubeconfig credentials ID you created in Kuberix Enterprise
       KUBECONFIG_CREDENTIAL_ID = 'go'
       // the name of the project you created in Kuberix Enterprise, not the DevOps project name
       PROJECT_NAME = 'devops-go'
     }
   
     stages {
       stage('docker login') {
         steps{
           container ('go') {
             sh 'echo $DOCKERHUB_CREDENTIAL_PSW  | docker login -u $DOCKERHUB_CREDENTIAL_USR --password-stdin'
           }
         }
       }
   
       stage('build & push') {
         steps {
           container ('go') {
             sh 'git clone https://github.com/yuswift/devops-go-sample.git'
             sh 'cd devops-go-sample && docker build -t $REGISTRY/$DOCKERHUB_USERNAME/$APP_NAME .'
             sh 'docker push $REGISTRY/$DOCKERHUB_USERNAME/$APP_NAME'
           }
         }
       }
       stage ('deploy app') {
         steps {
            container ('go') {
               withCredentials([
                 kubeconfigFile(
                   credentialsId: env.KUBECONFIG_CREDENTIAL_ID,
                   variable: 'KUBECONFIG')
                 ]) {
                 sh 'envsubst < devops-go-sample/manifest/deploy.yaml | kubectl apply -f -'
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

1. Jenkinsfile을 완료하면 대시보드에 그래픽 패널이 표시되는 것을 볼 수 있습니다. **Run**을 클릭하여 파이프라인을 실행합니다.

2. **Run Records**에서 파이프라인의 상태를 볼 수 있습니다. 성공적으로 실행되기까지 다소 시간이 걸릴 수 있습니다.


## 결과 확인

1. 파이프라인이 성공적으로 실행되면 Jenkinsfile에 지정된 프로젝트에 **Deployment**가 생성됩니다.

2. Docker Hub에 푸시된 이미지를 확인합니다.
   
   
