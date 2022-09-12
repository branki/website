---
title: "Deploy PostgreSQL on Kuberix Enterprise"
keywords: 'Kubernetes, Kuberix, PostgreSQL, app-store'
description: 'Learn how to deploy PostgreSQL from the App Store of Kuberix Enterprise and access its service.'
linkTitle: "Deploy PostgreSQL on Kuberix Enterprise"
weight: 14280
---

[PostgreSQL](https://www.postgresql.org/)은 신뢰성, 기능 견고성 및 성능으로 유명한 강력한 오픈 소스 객체 관계형 데이터베이스 시스템입니다.

이 튜토리얼은 Kuberix Enterprise의 앱 스토어에서 PostgreSQL을 배포하는 방법의 예를 안내합니다.

## 전제 조건

- [OpenPitrix 시스템 활성화](../../../pluggable-components/app-store/)를 확인하십시오.
- 이 튜토리얼에서는 작업 공간, 프로젝트 및 사용자 계정(`project-regular`)을 생성해야 합니다. 계정은 플랫폼 일반 사용자여야 하며 '운영자' 역할을 가진 프로젝트 운영자로 초대되어야 합니다. 이 튜토리얼에서는 `project-regular`로 로그인하여 `demo-workspace` 작업 공간의 `demo-project` 프로젝트에서 작업합니다. 자세한 내용은 [작업 공간, 프로젝트, 사용자 및 역할 생성](../../../quick-start/create-workspace-and-project/)을 참조하십시오.

## 실습 (핸즈온)

### 1단계: 앱 스토어에서 PostgreSQL 배포

1. `demo-project` 프로젝트의 **Overview** 페이지에서 왼쪽 상단의 **App Store**를 클릭합니다.

2. PostgreSQL을 찾아 **App Information** 페이지에서 **Install**를 클릭합니다.

3. 이름을 설정하고 앱 버전을 선택합니다. PostgreSQL이 `demo-project`에 배포되었는지 확인하고 **Next**를 클릭합니다.

4. **App Settings**에서 앱의 영구 볼륨을 지정하고 나중에 앱에 액세스하는 데 사용할 사용자 이름과 비밀번호를 기록합니다. 완료되면 **Install**를 클릭합니다.

   {{< notice note >}}

   PostgreSQL에 더 많은 값을 지정하려면 토글 스위치를 사용하여 앱의 매니페스트를 YAML 형식으로 보고 구성을 편집합니다.

   {{</ notice >}}

5. PostgreSQL이 실행될 때까지 기다립니다.

### 2단계: PostgreSQL 데이터베이스에 액세스

클러스터 외부에서 PostgreSQL에 액세스하려면 먼저 NodePort를 통해 앱을 노출해야 합니다.

1. **Services**로 이동하여 PostgreSQL의 서비스 이름을 클릭합니다.

2. **More**를 클릭하고 드롭다운 목록에서 **Edit External Access**을 선택합니다.

3. **Access Method**으로 **NodePort**를 선택하고 **OK**을 클릭합니다. 자세한 내용은 [프로젝트 게이트웨이](../../../project-administration/project-gateway/)를 참조하십시오.

4. **Ports** 아래에서 포트가 노출되어 있는 것을 볼 수 있습니다. 이 포트는 PostgreSQL 데이터베이스에 액세스하기 위해 다음 단계에서 사용됩니다.

5. **Pods** 아래의 Pod 메뉴를 확장하고 **Terminal** 아이콘을 클릭합니다. 팝업 창에서 직접 명령을 입력하여 데이터베이스에 액세스합니다.

   ![postgresql-출력](/images/docs/v3.3/appstore/built-in-apps/postgresql-app/postgresql-output.png)

   {{< notice note >}}

   SQLPro Studio와 같은 타사 응용 프로그램을 사용하여 데이터베이스에 연결할 수도 있습니다. 쿠버네티스 클러스터가 배포된 위치에 따라 보안 그룹에서 포트를 열고 관련 포트 전달 규칙을 구성해야 할 수 있습니다.

   {{</ notice >}}

6. 자세한 내용은 [PostgreSQL 공식 문서](https://www.postgresql.org/docs/)를 참조하세요.
