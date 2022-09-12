---
title: "Deploy Redis on Kuberix Enterprise"
keywords: 'Kuberix, Kubernetes, Installation, Redis'
description: 'Learn how to deploy Redis from the App Store of Kuberix Enterprise and access its service.'
linkTitle: "Deploy Redis on Kuberix Enterprise"
weight: 14291
---

[Redis](https://redis.io/)는 오픈 소스(BSD 라이선스), 인메모리 데이터 구조 저장소로 데이터베이스, 캐시 및 메시지 브로커로 사용됩니다.

이 튜토리얼은 Kuberix Enterprise의 앱 스토어에서 Redis를 배포하는 예를 안내합니다.

## 전제 조건

- [OpenPitrix 시스템 활성화](../../../pluggable-components/app-store/)를 확인하십시오.
- 이 튜토리얼에서는 작업 공간, 프로젝트 및 사용자 계정(`project-regular`)을 생성해야 합니다. 계정은 플랫폼 일반 사용자여야 하며 '운영자' 역할을 가진 프로젝트 운영자로 초대되어야 합니다. 이 튜토리얼에서는 `project-regular`로 로그인하여 `demo-workspace` 작업 공간의 `demo-project` 프로젝트에서 작업합니다. 자세한 내용은 [작업 공간, 프로젝트, 사용자 및 역할 생성](../../../quick-start/create-workspace-and-project/)을 참조하십시오.

## 실습 (핸즈온)

### 1단계: 앱 스토어에서 Redis 배포

1. `demo-project` 프로젝트의 **Overview** 페이지에서 왼쪽 상단의 **App Store**를 클릭합니다.

2. Redis를 찾아 **App Information** 페이지에서 **Install**를 클릭합니다.

3. 이름을 설정하고 앱 버전을 선택합니다. Redis가 `demo-project`에 배포되었는지 확인하고 **Next**를 클릭합니다.

4. **App Settings**에서 앱의 영구 볼륨과 비밀번호를 지정합니다. 완료되면 **Install**를 클릭합니다.

   {{< notice note >}}

   Redis에 더 많은 값을 지정하려면 토글 스위치를 사용하여 앱의 매니페스트를 YAML 형식으로 보고 설정을 편집합니다.

   {{</ notice >}}

5. Redis가 실행될 때까지 기다립니다.

### 2단계: Redis 터미널에 액세스

1. **Services**로 이동하여 Redis의 서비스 이름을 클릭합니다.

2. **Pods** 아래에서 메뉴를 확장하여 컨테이너 세부 정보를 확인한 다음 **Terminal** 아이콘을 클릭합니다.

3. 팝업창에서 터미널의 'redis-cli' 명령어를 이용하여 앱을 사용합니다.

   ![redis 사용](/images/docs/v3.3/appstore/built-in-apps/redis-app/use-redis.png)

4. 자세한 내용은 [Redis 공식 문서](https://redis.io/documentation)를 참조하세요.
