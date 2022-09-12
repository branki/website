---
title: "Deploy Tomcat on Kuberix Enterprise"
keywords: 'Kuberix, Kubernetes, Installation, Tomcat'
description: 'Learn how to deploy Tomcat from the App Store of Kuberix Enterprise and access its service.'
linkTitle: "Deploy Tomcat on Kuberix Enterprise"
weight: 14292
---
[Apache Tomcat](https://tomcat.apache.org/index.html)은 다양한 산업 및 조직에 걸쳐 수많은 대규모 미션 크리티컬 웹 애플리케이션을 지원합니다. Tomcat은 Java 코드를 실행할 수 있는 순수한 Java HTTP 웹 서버 환경을 제공합니다.

이 튜토리얼은 Kuberix Enterprise의 앱 스토어에서 Tomcat을 배포하는 예를 안내합니다.

## 전제 조건

- [OpenPitrix 시스템 활성화](../../../pluggable-components/app-store/)를 확인하십시오.
- 이 튜토리얼에서는 작업 공간, 프로젝트 및 사용자 계정을 생성해야 합니다. 계정은 플랫폼 일반 사용자여야 하며 '운영자' 역할을 가진 프로젝트 운영자로 초대되어야 합니다. 이 튜토리얼에서는 `project-regular`로 로그인하여 `demo-workspace` 작업 공간의 `demo-project` 프로젝트에서 작업합니다. 자세한 내용은 [작업 공간, 프로젝트, 사용자 및 역할 생성](../../../quick-start/create-workspace-and-project/)을 참조하십시오.

## 실습 (핸즈온)

### 1단계: 앱 스토어에서 Tomcat 배포

1. `demo-project` 프로젝트의 **Overview** 페이지에서 왼쪽 상단의 **App Store**를 클릭합니다.

2. Tomcat을 찾아 **App Information** 페이지에서 **Install**를 클릭합니다.

1. 이름을 설정하고 앱 버전을 선택합니다. Tomcat이 `demo-project`에 배포되었는지 확인하고 **Next**를 클릭합니다.

2. **App Settings**에서 기본 설정을 사용하거나 YAML 파일을 직접 편집하여 설정을 사용자 지정할 수 있습니다. 계속하려면 **Install**를 클릭하세요.

3. Tomcat이 실행될 때까지 기다리십시오.

### 2단계: Tomcat 터미널에 액세스

1. **Services**로 이동하여 Tomcat의 서비스 이름을 클릭합니다.

2. **Pods** 아래에서 메뉴를 확장하여 컨테이너 세부 정보를 확인한 다음 **Terminal** 아이콘을 클릭합니다.

3. 배포된 프로젝트는 `/usr/local/tomcat/webapps`에서 볼 수 있습니다.

   ![view-project](/images/docs/v3.3/appstore/built-in-apps/tomcat-app/view-project.png)

### 3단계: 브라우저에서 Tomcat 프로젝트에 액세스

클러스터 외부의 Tomcat 프로젝트에 액세스하려면 먼저 NodePort를 통해 앱을 노출해야 합니다.

1. **Services**로 이동하여 Tomcat의 서비스 이름을 클릭합니다.

2. **More**를 클릭하고 드롭다운 목록에서 **Edit External Access**을 선택합니다.

3. **Access Method**으로 **NodePort**를 선택하고 **OK**을 클릭합니다. 자세한 내용은 [프로젝트 게이트웨이](../../../project-administration/project-gateway/)를 참조하십시오.

4. **Ports**에서 포트가 노출된 것을 볼 수 있습니다.

5. 브라우저에서 `<NodeIP>:<NodePort>/sample`을 통해 샘플 Tomcat 프로젝트에 액세스합니다.

   ![access-tomcat-browser](/images/docs/v3.3/appstore/built-in-apps/tomcat-app/access-tomcat-browser.png)

   {{< notice note >}}

   쿠버네티스 클러스터가 배포된 위치에 따라 보안 그룹에서 포트를 열고 관련 포트 전달 규칙을 구성해야 할 수 있습니다.

   {{</ notice >}}

6. Tomcat에 대한 자세한 내용은 [Tomcat 공식 문서](https://tomcat.apache.org/index.html)를 참조하십시오.
