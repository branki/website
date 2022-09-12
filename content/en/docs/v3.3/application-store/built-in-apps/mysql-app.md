---
title: "Deploy MySQL on Kuberix Enterprise"
keywords: 'Kuberix, Kubernetes, Installation, MySQL'
description: 'Learn how to deploy MySQL from the App Store of Kuberix Enterprise and access its service.'

link title: "Deploy MySQL"
weight: 14260
---
[MySQL](https://www.mysql.com/)은 오픈 소스 관계형 데이터베이스 관리 시스템(RDBMS)으로 가장 일반적으로 사용되는 데이터베이스 관리 언어인 SQL(Structured Query Language)을 데이터베이스 관리에 사용합니다. 세계에서 가장 인기 있는 오픈 소스 데이터베이스를 사용하여 클라우드 네이티브 애플리케이션을 배포할 수 있는 완전 관리형 데이터베이스 서비스를 제공합니다.

이 튜토리얼은 Kuberix Enterprise의 앱 스토어에서 MySQL을 배포하는 예를 안내합니다.

## 전제 조건

- [OpenPitrix 시스템 활성화](../../../pluggable-components/app-store/)를 확인하십시오.
- 이 튜토리얼에서는 작업 공간, 프로젝트 및 사용자 계정을 생성해야 합니다. 계정은 플랫폼 일반 사용자여야 하며 '운영자' 역할을 가진 프로젝트 운영자로 초대되어야 합니다. 이 튜토리얼에서는 `project-regular`로 로그인하여 `demo-workspace` 작업 공간의 `demo-project` 프로젝트에서 작업합니다. 자세한 내용은 [작업 공간, 프로젝트, 사용자 및 역할 생성](../../../quick-start/create-workspace-and-project/)을 참조하십시오.

## 실습 (핸즈온)

### 1단계: 앱 스토어에서 MySQL 배포

1. `demo-project` 프로젝트의 **Overview** 페이지에서 왼쪽 상단의 **App Store**를 클릭합니다.

2. MySQL을 찾아 **App Information** 페이지에서 **Install**를 클릭합니다.

3. 이름을 설정하고 앱 버전을 선택합니다. MySQL이 `demo-project`에 배포되었는지 확인하고 **Next**를 클릭합니다.

4. **App Settings**에서 `mysqlRootPassword` 필드의 주석을 제거하고 비밀번호를 사용자 지정합니다. 계속하려면 **Install**를 클릭하세요.

5. MySQL이 실행될 때까지 기다리십시오.

### 2단계: MySQL 터미널에 액세스

1. **Workloads**로 이동하여 MySQL의 워크로드 이름을 클릭합니다.

2. **Pods** 아래에서 메뉴를 확장하여 컨테이너 세부 정보를 확인한 다음 **Terminal** 아이콘을 클릭합니다.

3. 터미널에서 `mysql -uroot -ptesting`을 실행하여 루트 사용자로 MySQL에 로그인합니다.

   ![mysql 로그인](/images/docs/v3.3/appstore/built-in-apps/mysql-app/log-in-mysql.png)

### 3단계: 클러스터 외부의 MySQL 데이터베이스에 액세스

클러스터 외부에서 MySQL에 액세스하려면 먼저 NodePort를 통해 앱을 노출해야 합니다.

1. **Services**로 이동하여 MySQL의 서비스 이름을 클릭합니다.

2. **More**를 클릭하고 드롭다운 목록에서 **Edit External Access**을 선택합니다.

3. **Access Method**으로 **NodePort**를 선택하고 **OK**을 클릭합니다. 자세한 내용은 [프로젝트 게이트웨이](../../../project-administration/project-gateway/)를 참조하십시오.

4. **Ports**에서 포트가 노출된 것을 볼 수 있습니다. 포트와 공용 IP 주소는 다음 단계에서 MySQL 데이터베이스에 액세스하는 데 사용됩니다.

5. MySQL 데이터베이스에 액세스하려면 MySQL 클라이언트를 사용하거나 연결을 위해 SQLPro Studio와 같은 타사 응용 프로그램을 설치해야 합니다. 다음 예제는 SQLPro Studio를 통해 MySQL 데이터베이스에 액세스하는 방법을 보여줍니다.

   ![로그인](/images/docs/v3.3/appstore/built-in-apps/mysql-app/login.png)

   ![access-mysql-success](/images/docs/v3.3/appstore/built-in-apps/mysql-app/access-mysql-success.png)

   {{< notice note >}}

   쿠버네티스 클러스터가 배포된 위치에 따라 보안 그룹에서 포트를 열고 관련 포트 전달 규칙을 구성해야 할 수 있습니다.

   {{</ notice >}}

6. MySQL에 대한 자세한 내용은 [MySQL 공식 문서](https://dev.mysql.com/doc/)를 참조하십시오.
