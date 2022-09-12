---
title: "Deploy RadonDB MySQL on Kuberix Enterprise"
keywords: 'Kuberix, Kubernetes, Installation, RadonDB MySQL'
description: 'Learn how to deploy RadonDB MySQL from the App Store of Kuberix Enterprise and access its service.'
linkTitle: "Deploy RadonDB MySQL on Kuberix Enterprise"
weight: 14293
---

[RadonDB MySQL](https://github.com/radondb/radondb-mysql-kubernetes)은 [MySQL](https://MySQL.org) 데이터베이스를 기반으로 하는 오픈 소스, 클라우드 네이티브, 고가용성 클러스터 솔루션입니다. . Raft 프로토콜을 통해 RadonDB MySQL은 트랜잭션 손실 없이 빠른 장애 조치를 가능하게 합니다.

이 튜토리얼은 Kuberix Enterprise의 앱 스토어에서 RadonDB MySQL을 배포하는 방법을 보여줍니다.

{{< 공지사항 >}}

**앱 스토어**의 RadonDB MySQL 버전은 v1.0.0이며 더 이상 유지 관리되지 않습니다.

최신 버전의 RadonDB MySQL을 사용하는 것이 좋습니다. 배포 지침은 [RadonDB MySQL 운영자 및 클러스터 배포](../../external-apps/deploy-radondb-mysql/)를 참조하십시오.

{{</ 공지 >}}

## 전제 조건

- [OpenPitrix 시스템 활성화](../../../pluggable-components/app-store/)를 확인하십시오.
- 이 튜토리얼에서는 작업 공간, 프로젝트 및 사용자 계정(`project-regular`)을 생성해야 합니다. 계정은 플랫폼 일반 사용자여야 하며 '운영자' 역할을 가진 프로젝트 운영자로 초대되어야 합니다. 이 튜토리얼에서는 `project-regular`로 로그인하여 `demo-workspace` 작업 공간의 `demo-project` 프로젝트에서 작업합니다. 자세한 내용은 [작업 공간, 프로젝트, 사용자 및 역할 생성](../../../quick-start/create-workspace-and-project/)을 참조하십시오.

## 실습 (핸즈온)

### 1단계: 앱 스토어에서 RadonDB MySQL 배포

1. `demo-project` 프로젝트의 **Overview** 페이지에서 왼쪽 상단의 **App Store**를 클릭합니다.

2. RadonDB MySQL을 찾아 **App Information** 페이지에서 **Install**를 클릭합니다.

3. 이름을 설정하고 앱 버전을 선택합니다. RadonDB MySQL이 `demo-project`에 배포되었는지 확인하고 **Next**를 클릭합니다.

4. **App Settings**에서 기본 설정을 사용하거나 YAML 파일을 직접 편집하여 설정을 사용자 지정할 수 있습니다. 완료되면 **Install**를 클릭합니다.

5. RadonDB MySQL이 실행될 때까지 기다립니다.

### 2단계: RadonDB MySQL에 액세스

1. **Application Workloads** 아래의 **Services**에서 RadonDB MySQL의 서비스 이름을 클릭합니다.

2. **Pods** 아래에서 메뉴를 확장하여 컨테이너 세부 정보를 확인한 다음 **Terminal** 아이콘을 클릭합니다.

3. 팝업창에서 터미널에 직접 명령어를 입력하여 앱을 사용합니다.

   ![RadonDB MySQL 접근](/images/docs/v3.3/appstore/built-in-apps/radondb-mysql-app/radondb-mysql-service-terminal.png)

4. 클러스터 외부에서 RadonDB MySQL에 접근하고 싶다면 [RadonDB MySQL 오픈소스 프로젝트](https://github.com/radondb/radondb-mysql-kubernetes)를 참조한다.
