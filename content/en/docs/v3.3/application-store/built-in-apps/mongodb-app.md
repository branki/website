---
title: "Deploy MongoDB on Kuberix Enterprise"
keywords: 'Kuberix, Kubernetes, Installation, MongoDB'
description: 'Learn how to deploy MongoDB from the App Store of KubeSphere and access its service.'
linkTitle: "Deploy MongoDB on Kuberix Enterprise"
weight: 14250
---

[MongoDB](https://www.mongodb.com/)는 최신 애플리케이션 개발자와 클라우드 시대를 위해 구축된 범용 문서 기반 분산 데이터베이스입니다.

이 튜토리얼은 Kuberix Enterprise의 앱 스토어에서 MongoDB를 배포하는 예를 안내합니다.

## 전제 조건

- [OpenPitrix 시스템 활성화](../../../pluggable-components/app-store/)를 확인하십시오.
- 이 튜토리얼에서는 작업 공간, 프로젝트 및 사용자 계정(`project-regular`)을 생성해야 합니다. 계정은 플랫폼 일반 사용자여야 하며 '운영자' 역할을 가진 프로젝트 운영자로 초대되어야 합니다. 이 튜토리얼에서는 `project-regular`로 로그인하여 `demo-workspace` 작업 공간의 `demo-project` 프로젝트에서 작업합니다. 자세한 내용은 [작업 공간, 프로젝트, 사용자 및 역할 생성](../../../quick-start/create-workspace-and-project/)을 참조하십시오.

## 실습 (핸즈온)

### 1단계: 앱 스토어에서 MongoDB 배포

1. `demo-project` 프로젝트의 **Overview** 페이지에서 왼쪽 상단의 **App Store**를 클릭합니다.

2. MongoDB를 찾아 **App Information** 페이지에서 **Install**를 클릭합니다.

3. 이름을 설정하고 앱 버전을 선택합니다. MongoDB가 `demo-project`에 배포되었는지 확인하고 **Next**를 클릭합니다.

4. **App Settings**에서 앱의 영구 볼륨을 지정하고 앱에 액세스하는 데 사용할 사용자 이름과 비밀번호를 기록합니다. 완료되면 **Install**를 클릭합니다.

   {{< notice note >}}

   MongoDB에 대해 더 많은 값을 지정하려면 토글 스위치를 사용하여 앱의 매니페스트를 YAML 형식으로 보고 구성을 편집합니다.

   {{</ notice >}}

5. MongoDB가 실행될 때까지 기다립니다.

### 2단계: MongoDB 터미널에 액세스

1. **Services**로 이동하여 MongoDB의 서비스 이름을 클릭합니다.

2. **Pods** 아래에서 메뉴를 확장하여 컨테이너 세부 정보를 확인한 다음 **Terminal** 아이콘을 클릭합니다.

3. 팝업창에서 터미널에 직접 명령어를 입력하여 앱을 사용합니다.

   ![mongodb-service-terminal](/images/docs/v3.3/appstore/built-in-apps/mongodb-app/mongodb-service-terminal.jpg)

   {{< notice note >}}

   클러스터 외부에서 MongoDB에 액세스하려면 **More**를 클릭하고 **Edit External Access**을 선택합니다. 표시되는 대화 상자에서 **NodePort**를 액세스 모드로 선택합니다. 노출된 후 포트 번호를 사용하여 MongoDB에 액세스합니다. 쿠버네티스 클러스터가 배포된 위치에 따라 보안 그룹에서 포트를 열고 관련 포트 전달 규칙을 구성해야 할 수 있습니다.

   {{</ notice >}}

4. 자세한 내용은 [MongoDB 공식 문서](https://docs.mongodb.com/manual/)를 참조하세요.
