---
title: "Use Nexus in Pipelines"
keywords: 'Kuberix, Kubernetes, Pipeline, Nexus, Jenkins'
description: 'Learn how to use Nexus in pipelines on Kuberix Enterprise.'
linkTitle: "Use Nexus in Pipelines"
weight: 11450
---

[Nexus](https://www.sonatype.com/products/repository-oss)는 아티팩트를 저장, 정리, 배포하는 저장소 관리자입니다. Nexus를 사용하면 개발자는 개발 프로세스에 필요한 아티팩트를 더 잘 제어할 수 있습니다.

이 튜토리얼은 Kuberix Enterprise의 파이프라인에서 Nexus를 사용하는 방법을 보여줍니다.

## 전제 조건

- [Kuberix Enterprise DevOps 시스템 활성화](../../../pluggable-components/devops/)가 필요합니다.
- [Nexus 인스턴스 준비](https://help.sonatype.com/repomanager3/installation)가 필요합니다.
- [GitHub](https://github.com/) 계정이 있어야 합니다.
- 작업 공간, DevOps 프로젝트(예: `demo-devops`) 및 사용자(예: `project-regular`)를 생성해야 합니다. 이 계정은 '운영자' 역할로 DevOps 프로젝트에 초대되어야 합니다. 자세한 내용은 [작업 공간, 프로젝트, 사용자 및 역할 생성](../../../quick-start/create-workspace-and-project/)을 참조하십시오.

## 실습 (핸즈온)

### 1단계: Nexus에서 저장소 URL 가져오기

1. Nexus 콘솔에 `admin`으로 로그인하고 <img src="/images/docs/v3.3/devops-user-guide/examples/use-nexus-in-pipeline/gear.png" height=를 클릭합니다. "18px" alt="icon" /> 상단 탐색 모음.

2. **Repositories** 페이지로 이동하면 Nexus에서 3가지 유형의 저장소를 제공하는 것을 볼 수 있습니다.

   - `proxy`: 캐시로 Nexus에 리소스를 다운로드하고 저장하기 위한 원격 저장소의 프록시입니다.
   - `hosted`: Nexus에 아티팩트를 저장하는 저장소입니다.
   - `group`: 구성된 Nexus 리포지토리의 그룹입니다.

3. 리포지토리를 클릭하여 세부 정보를 볼 수 있습니다. 예를 들어 **maven-public**을 클릭하여 세부 정보 페이지로 이동하면 **URL**을 볼 수 있습니다.

### 2단계: GitHub 저장소에서 `pom.xml` 수정

1. GitHub에 로그인합니다. [예제 리포지토리](https://github.com/devops-ws/learn-pipeline-java)를 자신의 GitHub 계정으로 분기합니다.

2. **learn-pipeline-java**의 GitHub 리포지토리에서 루트 디렉터리의 `pom.xml` 파일을 클릭합니다.

3. <img src="/images/docs/v3.3/devops-user-guide/examples/use-nexus-in-pipeline/github-edit-icon.png" height="18px" alt="icon " /> 파일에서 `<distributionManagement>`의 코드 세그먼트를 수정합니다. `<id>`를 설정하고 자체 Nexus 저장소의 URL을 사용합니다.

   ![modify-pom](/images/docs/v3.3/devops-user-guide/examples/use-nexus-in-pipeline/modify-pom.png)

4. 완료되면 페이지 하단의 **Commit changes**을 클릭합니다.

### 3단계: ConfigMap 수정

1. Kuberix Enterprise 웹 콘솔에 `admin`으로 로그인하고 왼쪽 상단 모서리에서 **Platform**을 클릭한 다음 **Cluster Management**를 선택합니다.

2. **Configuration**에서 **ConfigMaps**를 선택합니다. **ConfigMaps** 페이지의 드롭다운 목록에서 `kuberixEnterprise-devops-worker`를 선택하고 `ke-devops-agent`를 클릭합니다.

3. 세부정보 페이지의 **More** 드롭다운 메뉴에서 **Edit YAML**을 클릭합니다.

4. 표시된 대화 상자에서 아래로 스크롤하여 `<servers>`의 코드 세그먼트를 찾아 다음 코드를 입력합니다.

   ```yaml
   <servers>
     <server>
       <id>nexus</id>
       <username>admin</username>
       <password>admin</password>
     </server>
   </servers>
   ```

   ![enter-server-code](/images/docs/v3.3/devops-user-guide/examples/use-nexus-in-pipeline/enter-server-code.png)

   {{< notice note >}}

   `<id>`는 2단계에서 Nexus에 대해 설정한 고유 식별자입니다. `<username>`은 Nexus 사용자 이름입니다. `<password>`는 Nexus 비밀번호입니다. 또한 Nexus에서 'NuGet API 키'를 구성하고 더 나은 보안을 위해 여기에서 사용할 수 있습니다.

   {{</ notice >}}

5. 계속해서 `<mirrors>`의 코드 세그먼트를 찾아 다음 코드를 입력합니다.:

   ```yaml
   <mirrors>
     <mirror>
       <id>nexus</id>
       <name>maven-public</name>
       <url>http://135.68.37.85:8081/repository/maven-public/</url>
       <mirrorOf>*</mirrorOf>
     </mirror>
   </mirrors>
   ```

   ![enter-mirror-code](/images/docs/v3.3/devops-user-guide/examples/use-nexus-in-pipeline/enter-mirror-code.png)

   {{< notice note >}}

   `<id>`는 2단계에서 Nexus에 대해 설정한 고유 식별자입니다. `<name>`은 Nexus 저장소 이름입니다. `<url>`은 Nexus 저장소의 URL입니다. `<mirrorOf>`는 미러링할 Maven 저장소입니다. 이 튜토리얼에서는 `*`를 입력하여 모든 Maven 저장소를 미러링합니다. 자세한 내용은 [Repositories용 미러 사용](https://maven.apache.org/guides/mini/guide-mirror-settings.html)을 참조하십시오.

   {{</ notice >}}

6. 완료되면 **OK**을 클릭합니다.

### 4단계: 파이프라인 생성

1. KubeSphere 웹 콘솔에서 로그아웃하고 'project-regular'로 다시 로그인합니다. DevOps 프로젝트로 이동하고 **Pipelines** 페이지에서 **Create**를 클릭합니다.

2. **Basic Information** 탭에서 파이프라인 이름(예: `nexus-pipeline`)을 설정하고 **Next**을 클릭합니다.

3. **Advanced Settings** 탭에서 **Create**를 클릭하여 기본 설정을 사용합니다.

4. 파이프라인 이름을 클릭하여 세부 정보 페이지로 이동하고 **Edit Jenkinsfile**을 클릭합니다.

5. 표시된 대화 상자에서 다음과 같이 Jenkinsfile을 입력합니다. 완료되면 **OK**을 클릭합니다.

   ```groovy
   pipeline {
       agent {
         node {
           label 'maven'
         }
       }
       stages {
           stage ('clone') {
               steps {
                   git 'https://github.com/Felixnoo/learn-pipeline-java.git'
               }
           }
           
           stage ('build') {
               steps {
                   container ('maven') {
                       sh 'mvn clean package'
                   }
               }  
           }
           
           stage ('deploy to Nexus') {
               steps {
                   container ('maven') {
                       sh 'mvn deploy -DaltDeploymentRepository=nexus::default::http://135.68.37.85:8081/repository/maven-snapshots/'
                   }   
               }
           }
           stage ('upload') {
               steps {
                   archiveArtifacts artifacts: 'target/*.jar', followSymlinks: false
               }
           }
       }
   }
   ```

   {{< notice note >}}

   GitHub 리포지토리 주소를 자신의 것으로 바꿔야 합니다. `nexus에 배포` 단계의 명령에서 `nexus`는 ConfigMap의 `<id>` 및 `http://135.68.37.85:8081/repository/maven-snapshots/'에 설정한 이름입니다. `는 Nexus 저장소의 URL입니다.
   
   {{</ notice >}}

### 5단계: 파이프라인 실행 및 결과 확인

1. 그래픽 편집 패널에 표시된 모든 단계와 단계를 볼 수 있습니다. **Run**을 클릭하여 파이프라인을 실행합니다.

2. 잠시 후 **Successful**으로 표시된 파이프라인 상태를 볼 수 있습니다. 세부정보를 보려면 **Successful** 레코드를 클릭하세요.

3. **로그 보기**를 클릭하면 자세한 로그를 볼 수 있습니다.

4. Nexus에 로그인하고 **Browse**를 클릭합니다. **maven-public**을 클릭하면 모든 종속성이 다운로드된 것을 볼 수 있습니다.

   ![maven-public](/images/docs/v3.3/devops-user-guide/examples/use-nexus-in-pipeline/maven-public.png)

5. **Browse** 페이지로 돌아가 **maven-snapshots**를 클릭합니다. JAR 패키지가 저장소에 업로드된 것을 볼 수 있습니다.

   ![maven-snapshots](/images/docs/v3.3/devops-user-guide/examples/use-nexus-in-pipeline/maven-snapshots.png)



