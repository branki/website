---
title: "Build and Deploy a Maven Project"
keywords: 'Kubernetes, Kuberix, Docker, DevOps, Jenkins, Maven'
description: 'Learn how to build and deploy a Maven project using a Kuberix Enterprise pipeline.'
linkTitle: "Build and Deploy a Maven Project"
weight: 11430
---

## 전제 조건

- [Kuberix Enterprise DevOps 시스템 활성화](../../../pluggable-components/devops/)가 필요합니다.
- [Docker Hub](https://www.dockerhub.com/) 계정이 있어야 합니다.
- Workspace, DevOps 프로젝트, 사용자 계정을 생성해야 하며, 이 사용자는 'operator' 역할로 DevOps 프로젝트에 초대받아야 합니다. 자세한 내용은 [작업 공간, 프로젝트, 사용자 및 역할 생성](../../../quick-start/create-workspace-and-project/)을 참조하십시오.

## Maven 프로젝트의 워크플로

아래 그래프에서 볼 수 있듯이 Kuberix Enterprise DevOps에는 Jenkins 파이프라인을 사용하여 Maven 프로젝트를 빌드하고 배포하는 Maven 프로젝트에 대한 워크플로가 있습니다. 모든 단계는 파이프라인에서 정의됩니다.

![maven-project-jenkins](/images/docs/v3.3/devops-user-guide/examples/build-and-deploy-a-maven-project/maven-project-jenkins.png)

먼저 Jenkins Master는 파이프라인을 실행할 Pod를 생성합니다. 쿠버네티스는 Jenkins Master의 에이전트로 Pod를 생성하며 파이프라인이 완료된 후 Pod가 소멸됩니다. 주요 프로세스에는 코드 복제, 이미지 빌드 및 푸시, 워크로드 배포가 포함됩니다.

## Jenkins의 기본 구성

### 메이븐 버전

Maven 빌더 컨테이너에서 다음 명령을 실행하여 버전 정보를 가져옵니다.

```bash
mvn --version

Apache Maven 3.5.3 (3383c37e1f9e9b3bc3df5050c29c8aff9f295297; 2018-02-24T19:49:05Z)
Maven home: /opt/apache-maven-3.5.3
Java version: 1.8.0_232, vendor: Oracle Corporation
Java home: /usr/lib/jvm/java-1.8.0-openjdk-1.8.0.232.b09-0.el7_7.i386/jre
Default locale: en_US, platform encoding: UTF-8
```

### 메이븐 캐시

Jenkins 에이전트는 노드의 Docker 볼륨별로 디렉터리를 마운트합니다. 파이프라인은 Maven 빌드에 사용되는 `/root/.m2`와 같은 일부 특수 디렉토리와 Kuberix Enterprise DevOps의 Maven 도구에 대한 기본 캐시 디렉토리를 캐시할 수 있으므로 종속성 패키지가 노드에 다운로드되고 캐시됩니다.

### Jenkins 에이전트의 글로벌 Maven 설정

Maven 설정의 기본 파일 경로는 `maven`이고 구성 파일 경로는 `/opt/apache-maven-3.5.3/conf/settings.xml`입니다. 다음 명령을 실행하여 Maven 설정의 내용을 가져옵니다.

```bash
kubectl get cm -n kuberixEnterprise-devops-worker ke-devops-agent -o yaml
```

### Maven Pod의 네트워크

'maven'이라고 표시된 Pod는 docker-in-docker 네트워크를 사용하여 파이프라인을 실행합니다. 즉, 노드의 `/var/run/docker.sock`이 Maven 컨테이너에 마운트됩니다.

## Maven 파이프라인 예제

### Maven 프로젝트 준비

- 개발 장치에서 Maven 프로젝트를 성공적으로 빌드했는지 확인합니다.
- Dockerfile을 프로젝트 저장소에 추가하여 이미지를 빌드합니다. 자세한 내용은 <https://github.com/ke/devops-maven-sample/blob/master/Dockerfile-online>을 참조하세요.
- 프로젝트 리포지토리에 YAML 파일을 추가하여 워크로드를 배포합니다. 자세한 내용은 <https://github.com/ke/devops-maven-sample/tree/master/deploy/dev-ol>을 참조하세요. 다른 환경이 있는 경우 여러 배포 파일을 준비해야 합니다.

### 자격 증명 생성

| Credential ID   | Type                | Where to Use                 |
| --------------- | ------------------- | ---------------------------- |
| dockerhub-id    | Username and password | Registry, such as Docker Hub |
| demo-kubeconfig | kubeconfig          | Workload deployment         |

### 워크로드용 프로젝트 만들기

이 예에서 모든 워크로드는 'kuberixEnterprise-sample-dev'에 배포됩니다. 'kuberixEnterprise-sample-dev' 프로젝트를 미리 생성해야 합니다.

### Maven 프로젝트를 위한 파이프라인 생성

1. DevOps 프로젝트의 **Pipelines**으로 이동하고 **Create**을 클릭하여 `maven`이라는 파이프라인을 생성합니다. 자세한 내용은 [파이프라인 생성 - 그래픽 편집 패널 사용](../../how-to-use/pipelines/create-a-pipeline-using-graphical-editing-panel/)을 참조하십시오.

2. 파이프라인의 세부 정보 페이지로 이동하여 **Jenkinsfile Edit**을 클릭합니다.

3. 다음 내용을 복사하여 표시된 대화 상자에 붙여넣습니다. 'DOCKERHUB_NAMESPACE' 값을 자신의 값으로 바꿔야 합니다. 편집이 끝나면 **OK**을 클릭하여 Jenkinsfile을 저장합니다.

   ```groovy
   pipeline {
       agent {
           label 'maven'
       }
   
       parameters {
           string(name:'TAG_NAME',defaultValue: '',description:'')
       }
   
       environment {
           DOCKER_CREDENTIAL_ID = 'dockerhub-id'
           KUBECONFIG_CREDENTIAL_ID = 'demo-kubeconfig'
           REGISTRY = 'docker.io'
           // need to replace by yourself dockerhub namespace
           DOCKERHUB_NAMESPACE = 'Docker Hub Namespace'
           APP_NAME = 'devops-maven-sample'
           BRANCH_NAME = 'dev'
           PROJECT_NAME = 'kuberixEnterprise-sample-dev'
       }
   
       stages {
           stage ('checkout scm') {
               steps {
                   // Please avoid committing your test changes to this repository
                   git branch: 'master', url: "https://github.com/ke/devops-maven-sample.git"
               }
           }
   
           stage ('unit test') {
               steps {
                   container ('maven') {
                       sh 'mvn clean test'
                   }
               }
           }
   
           stage ('build & push') {
               steps {
                   container ('maven') {
                       sh 'mvn -Dmaven.test.skip=true clean package'
                       sh 'docker build -f Dockerfile-online -t $REGISTRY/$DOCKERHUB_NAMESPACE/$APP_NAME:SNAPSHOT-$BRANCH_NAME-$BUILD_NUMBER .'
                       withCredentials([usernamePassword(passwordVariable : 'DOCKER_PASSWORD' ,usernameVariable : 'DOCKER_USERNAME' ,credentialsId : "$DOCKER_CREDENTIAL_ID" ,)]) {
                           sh 'echo "$DOCKER_PASSWORD" | docker login $REGISTRY -u "$DOCKER_USERNAME" --password-stdin'
                           sh 'docker push  $REGISTRY/$DOCKERHUB_NAMESPACE/$APP_NAME:SNAPSHOT-$BRANCH_NAME-$BUILD_NUMBER'
                       }
                   }
               }
           }
   
           stage('deploy to dev') {
                steps {
                    container ('maven') {
                         withCredentials([
                             kubeconfigFile(
                             credentialsId: env.KUBECONFIG_CREDENTIAL_ID,
                             variable: 'KUBECONFIG')
                             ]) {
                             sh 'envsubst < deploy/all-in-one/devops-sample.yaml | kubectl apply -f -'
                         }
                    }
                }
           }
       }
   }
   ```

4. 그래픽 편집 패널에서 단계와 단계가 자동으로 생성되는 것을 볼 수 있습니다.

### 실행 및 테스트

1. **Run**을 클릭하고 표시된 대화 상자에서 **TAG_NAME**에 대해 `v1`을 입력한 다음 **OK**를 클릭하여 파이프라인을 실행합니다.

2. 파이프라인이 성공적으로 실행되면 **Run Records** 탭으로 이동하여 세부 정보를 볼 수 있습니다.

3. `kuberixEnterprise-sample-dev` 프로젝트에서 새로운 워크로드가 생성되었습니다.

4. **Services** 페이지에서 생성된 서비스에 대한 외부 액세스 정보를 확인합니다.
