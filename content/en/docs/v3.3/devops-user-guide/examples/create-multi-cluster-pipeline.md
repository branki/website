---
title: "Create a Multi-cluster Pipeline"
keywords: 'Kuberix, Kubernetes, Multi-cluster, Pipeline, DevOps'
description: 'Learn how to create a multi-cluster pipeline on Kuberix Enterprise.'
linkTitle: "Create a Multi-cluster Pipeline"
weight: 11440
---

클라우드 제공업체는 다양한 호스팅 쿠버네티스 서비스를 제공하므로 DevOps 파이프라인은 여러 쿠버네티스 클러스터가 관련된 사용 사례를 처리해야 합니다.

이 튜토리얼은 Kuberix Enterprise에서 멀티 클러스터 파이프라인을 생성하는 방법을 보여줍니다.

## 전제 조건

- Kuberix Enterprise가 설치된 3개의 쿠버네티스 클러스터가 필요합니다. 하나의 클러스터를 호스트 클러스터로 선택하고 다른 두 개를 구성원 클러스터로 선택합니다. 클러스터 역할 및 Kuberix Enterprise에서 멀티 클러스터 환경 구축 방법에 대한 자세한 내용은 [멀티 클러스터 관리](../../../multicluster-management/)를 참조하십시오.
- 구성원 클러스터를 [공개 클러스터](../../../cluster-administration/cluster-settings/cluster-visibility-and-authorization/#make-a-cluster-public)로 설정해야 합니다. 또는 [작업 공간이 생성된 후 클러스터 가시성을 설정](../../../cluster-administration/cluster-settings/cluster-visibility-and-authorization/#set-cluster-visibility-after-a -작업공간이 생성됨).
- 호스트 클러스터에서 [Kuberix Enterprise DevOps 시스템을 활성화](../../../pluggable-components/devops/)해야 합니다.
- SonarQube를 파이프라인에 통합해야 합니다. 자세한 내용은 [SonarQube를 파이프라인에 통합](../../how-to-integrate/sonarqube/)을 참조하세요.
- 호스트 클러스터에 `ws-manager`, `ws-admin`, `project-admin` 및 `project-regular`의 4개 계정을 만들고 이 계정에 다른 역할을 부여해야 합니다. 자세한 내용은 [작업 공간, 프로젝트, 사용자 및 역할 생성](../../../quick-start/create-workspace-and-project/#step-1-create-an-account)을 참조하세요.

## 워크플로 개요

이 자습서에서는 세 개의 클러스터를 사용하여 워크플로에서 세 개의 격리된 환경을 제공합니다. 아래 도표를 참조하십시오.

![멀티 클러스터에 대한 사용 사례](/images/docs/v3.3/devops-user-guide/examples/create-multi-cluster-pipeline/use-case-for-multi-cluster.png)

3개의 클러스터는 각각 개발, 테스트 및 프로덕션에 사용됩니다. 코드가 Git 리포지토리에 제출되면 파이프라인이 '단위 테스트', 'SonarQube 분석', '빌드 및 푸시' 및 '개발 클러스터에 배포' 단계를 통해 실행되도록 트리거됩니다. 개발자는 자체 테스트 및 검증을 위해 개발 클러스터를 사용합니다. 개발자가 승인하면 파이프라인은 엄격한 검증을 위해 '테스트 클러스터에 배포' 단계로 진행됩니다. 마지막으로 필요한 승인이 준비된 파이프라인은 '프로덕션 클러스터에 배포' 단계에 도달하여 외부에서 서비스를 제공합니다.

## 실습 (핸즈온)

### 1단계: 클러스터 준비

각 클러스터의 역할은 아래 표를 참조하십시오.

| Cluster Name | Cluster Role   | Usage       |
| ------------ | -------------- | ----------- |
| host         | Host cluster   | Testing     |
| shire        | Member cluster | Production  |
| rohan        | Member cluster | Development |

{{< notice note >}}

이러한 쿠버네티스 클러스터는 다양한 클라우드 제공업체에서 호스팅될 수 있으며 해당 쿠버네티스 버전도 다를 수 있습니다. Kuberix Enterprise 3.3.0에 권장되는 쿠버네티스 버전: v1.19.x, v1.20.x, v1.21.x, v1.22.x 및 v1.23.x(실험 지원).

{{</ notice >}}

### 2단계: 작업 공간 만들기

1. 호스트 클러스터의 웹 콘솔에 `ws-manager`로 로그인합니다. **Workspaces** 페이지에서 **Create**를 클릭합니다.

2. **Basic Information** 페이지에서 작업 공간의 이름을 `devops-multicluster`로 지정하고 **Administrator**에 대해 `ws-admin`을 선택한 후 **Next**을 클릭합니다.

3. **Cluster Settings** 페이지에서 세 개의 클러스터를 모두 선택하고 **Create**를 클릭합니다.

4. 생성된 작업 공간이 목록에 표시됩니다. 콘솔에서 로그아웃하고 `ws-admin`으로 다시 로그인하여 `project-admin`과 `project-regular`를 작업공간에 초대하고 이들에게 `workspace-self-provisioner` 및 `workspace' 역할을 부여해야 합니다. -viewer`. 자세한 내용은 [작업 공간, 프로젝트, 사용자 및 역할 만들기](../../../quick-start/create-workspace-and-project/#step-2-create-a-workspace)를 참조하세요.

### 3단계: DevOps 프로젝트 만들기

1. 콘솔에서 로그아웃하고 'project-admin'으로 다시 로그인합니다. **DevOps Projects** 페이지로 이동하여 **Create**를 클릭합니다.

2. 표시된 대화 상자에서 **Name**에 `multicluster-demo`를 입력하고 **Cluster Settings**에 대해 **host**를 선택한 다음 **OK**을 클릭합니다.

   {{< notice note >}}

   DevOps 구성 요소가 활성화된 클러스터만 드롭다운 목록에서 사용할 수 있습니다.

   {{</ notice >}}

3. 생성된 DevOps 프로젝트가 목록에 표시됩니다. 'project-regular' 사용자를 이 프로젝트에 초대하고 'operator' 역할을 할당했는지 확인하세요. 자세한 내용은 [작업 공간, 프로젝트, 사용자 및 역할 생성](../../../quick-start/create-workspace-and-project/#step-5-create-a-devops-project -선택 과목).

### 4단계: 클러스터에서 프로젝트 만들기

사전에 아래 표와 같이 프로젝트를 생성해야 합니다. 이 프로젝트에 `project-regular` 사용자를 초대하고 `operator` 역할을 할당했는지 확인하세요. 프로젝트 생성 방법에 대한 자세한 내용은 [작업 공간, 프로젝트, 사용자 및 역할 생성](../../../quick-start/create-workspace-and-project/#step-3-create -a-프로젝트).

| Cluster Name | Usage       | Project Name                  |
| ------------ | ----------- | ----------------------------- |
| host         | Testing     | kuberixEnterprise-sample-prod |
| shire        | Production  | kuberixEnterprise-sample-prod |
| rohan        | Development | kuberixEnterprise-sample-dev  |

### 5단계: 자격 증명 만들기

1. 콘솔에서 로그아웃하고 'project-regular'로 다시 로그인합니다. **DevOps Projects** 페이지에서 DevOps 프로젝트 'multicluster-demo'를 클릭합니다.

2. **Credentials** 페이지에서 아래 표와 같이 자격 증명을 생성해야 합니다. 자격 증명 생성 방법에 대한 자세한 내용은 [자격 증명 관리](../../how-to-use/devops-settings/credential-management/#create-credentials) 및 [Jenkinsfile을 사용하여 파이프라인 만들기]를 참조하세요. (../../how-to-use/pipelines/create-a-pipeline-using-jenkinsfile/#step-1-create-credentials).

   | Credential ID | Type                | Where to Use                       |
   | ------------- | ------------------- | ---------------------------------- |
   | host          | kubeconfig          | The host cluster for testing       |
   | shire         | kubeconfig          | The member cluster for production  |
   | rohan         | kubeconfig          | The member cluster for development |
   | dockerhub-id  | Username and password | Docker Hub                         |
   | sonar-token   | Access token        | SonarQube                          |

   {{< notice note >}}

   kubeconfig 자격 증명 `shire` 및 `rohan`을 생성할 때 구성원 클러스터의 kubeconfig를 수동으로 입력해야 합니다. 호스트 클러스터가 구성원 클러스터의 API 서버 주소에 액세스할 수 있는지 확인하십시오.

   {{</ notice >}}

3. 총 5개의 자격 증명이 생성됩니다.

### 6단계: 파이프라인 생성

1. **Pipelines** 페이지로 이동하여 **Create**를 클릭합니다. 표시된 대화 상자에서 **Name**에 `build-and-deploy-application`을 입력하고 **Next**를 클릭합니다.

2. **Advanced Settings** 탭에서 **Create**를 클릭하여 기본 설정을 사용합니다.

3. 생성된 파이프라인이 목록에 표시됩니다. 이름을 클릭하면 세부 정보 페이지로 이동합니다.

4. **Edit Jenkinsfile**을 클릭하고 다음 내용을 복사하여 붙여넣습니다. `DOCKERHUB_NAMESPACE` 값을 자신의 값으로 바꾼 다음 **OK**을 클릭합니다.

   ```groovy
   pipeline {
     agent {
       node {
         label 'maven'
       }
   
     }
     parameters {
           string(name:'BRANCH_NAME',defaultValue: 'master',description:'')
       }
     environment {
           DOCKER_CREDENTIAL_ID = 'dockerhub-id'
           PROD_KUBECONFIG_CREDENTIAL_ID = 'shire'
           TEST_KUBECONFIG_CREDENTIAL_ID = 'host'
           DEV_KUBECONFIG_CREDENTIAL_ID = 'rohan'
   
           REGISTRY = 'docker.io'
           DOCKERHUB_NAMESPACE = 'your Docker Hub account ID'
           APP_NAME = 'devops-maven-sample'
           SONAR_CREDENTIAL_ID = 'sonar-token'
           TAG_NAME = "SNAPSHOT-$BRANCH_NAME-$BUILD_NUMBER"
       }
     stages {
       stage('checkout') {
         steps {
           container('maven') {
             git branch: 'master', url: 'https://github.com/ke/devops-maven-sample.git'
           }
         }
       }
       stage('unit test') {
         steps {
           container('maven') {
             sh 'mvn clean test'
           }
         }
       }
       stage('sonarqube analysis') {
         steps {
           container('maven') {
             withCredentials([string(credentialsId: "$SONAR_CREDENTIAL_ID", variable: 'SONAR_TOKEN')]) {
               withSonarQubeEnv('sonar') {
                 sh "mvn sonar:sonar -Dsonar.login=$SONAR_TOKEN"
               }
   
             }
           }
   
         }
       }
       stage('build & push') {
         steps {
           container('maven') {
             sh 'mvn -Dmaven.test.skip=true clean package'
             sh 'docker build -f Dockerfile-online -t $REGISTRY/$DOCKERHUB_NAMESPACE/$APP_NAME:SNAPSHOT-$BRANCH_NAME-$BUILD_NUMBER .'
             withCredentials([usernamePassword(passwordVariable : 'DOCKER_PASSWORD' ,usernameVariable : 'DOCKER_USERNAME' ,credentialsId : "$DOCKER_CREDENTIAL_ID" ,)]) {
               sh 'echo "$DOCKER_PASSWORD" | docker login $REGISTRY -u "$DOCKER_USERNAME" --password-stdin'
               sh 'docker push  $REGISTRY/$DOCKERHUB_NAMESPACE/$APP_NAME:SNAPSHOT-$BRANCH_NAME-$BUILD_NUMBER'
             }
           }
         }
       }
       stage('push latest') {
         steps {
           container('maven') {
             sh 'docker tag  $REGISTRY/$DOCKERHUB_NAMESPACE/$APP_NAME:SNAPSHOT-$BRANCH_NAME-$BUILD_NUMBER $REGISTRY/$DOCKERHUB_NAMESPACE/$APP_NAME:latest '
             sh 'docker push  $REGISTRY/$DOCKERHUB_NAMESPACE/$APP_NAME:latest '
           }
         }
       }
       stage('deploy to dev') {
         steps {
            container('maven') {
               withCredentials([
                   kubeconfigFile(
                   credentialsId: env.DEV_KUBECONFIG_CREDENTIAL_ID,
                   variable: 'KUBECONFIG')
                   ]) {
                   sh 'envsubst < deploy/dev-all-in-one/devops-sample.yaml | kubectl apply -f -'
               }
            }
         }
       }
       stage('deploy to staging') {
         steps {
            container('maven') {
               input(id: 'deploy-to-staging', message: 'deploy to staging?')
               withCredentials([
                   kubeconfigFile(
                   credentialsId: env.TEST_KUBECONFIG_CREDENTIAL_ID,
                   variable: 'KUBECONFIG')
                   ]) {
                   sh 'envsubst < deploy/prod-all-in-one/devops-sample.yaml | kubectl apply -f -'
               }
            }
         }
       }
       stage('deploy to production') {
         steps {
            container('maven') {
               input(id: 'deploy-to-production', message: 'deploy to production?')
               withCredentials([
                   kubeconfigFile(
                   credentialsId: env.PROD_KUBECONFIG_CREDENTIAL_ID,
                   variable: 'KUBECONFIG')
                   ]) {
                   sh 'envsubst < deploy/prod-all-in-one/devops-sample.yaml | kubectl apply -f -'
               }
            }
         }
       }
     }
   }
   ```

   {{< notice note >}}

   `mvn` 명령의 `-o` 플래그는 오프라인 모드가 활성화되었음을 나타냅니다. 관련 maven 종속성과 캐시가 로컬로 준비되어 있는 경우 오프라인 모드를 유지하여 시간을 절약할 수 있습니다.

   {{</ notice >}}

5. 파이프라인이 생성된 후 그래픽 편집 패널에서도 해당 단계와 단계를 볼 수 있습니다.

### 7단계: 파이프라인 실행 및 결과 확인

1. **Run**을 클릭하여 파이프라인을 실행합니다. 파이프라인은 개발을 위해 리소스가 클러스터에 배포되었으므로 **deploy to stagin** 단계에 도달하면 일시 중지됩니다. 테스트 클러스터 `host`와 프로덕션 클러스터 `shire`에 리소스를 배포하려면 수동으로 **Proceed**를 두 번 클릭해야 합니다.

2. 잠시 후 **Successful**으로 표시된 파이프라인 상태를 볼 수 있습니다.

3. 오른쪽 상단의 **View Logs**를 클릭하여 파이프라인 실행 로그를 확인합니다. 각 단계에 대해 클릭하여 추가 분석을 위해 로컬 시스템에 다운로드할 수 있는 로그를 검사합니다.

4. 파이프라인이 성공적으로 실행되면 **Code Check**를 클릭하여 SonarQube를 통해 결과를 확인합니다.

5. **Projects** 페이지로 이동하고 드롭다운 목록에서 특정 클러스터를 선택하여 클러스터 전체에서 서로 다른 프로젝트에 배포된 리소스를 볼 수 있습니다.

   



