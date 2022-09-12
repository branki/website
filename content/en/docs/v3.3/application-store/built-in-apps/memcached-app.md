---
title: "Deploy Memcached on Kuberix Enterprise"
keywords: 'Kubernetes, Kuberix, Memcached, app-store'
description: 'Learn how to deploy Memcached from the App Store of Kuberix Enterprise and access its service.'
linkTitle: "Deploy Memcached on Kuberix Enterprise"
weight: 14230
---
[Memcached](https://memcached.org/)는 데이터베이스 호출, API 호출 또는 페이지 렌더링 결과에서 가져온 임의 데이터(문자열, 개체)의 작은 청크를 위한 메모리 내 키-값 저장소입니다. API는 대부분의 인기 있는 언어에서 사용할 수 있습니다.

이 튜토리얼은 Kuberix Enterprise의 앱 스토어에서 Memcached를 배포하는 예를 안내합니다.

## 전제 조건

- [OpenPitrix 시스템 활성화](../../../pluggable-components/app-store/)를 확인하십시오.
- 이 튜토리얼에서는 작업 공간, 프로젝트 및 사용자 계정(`project-regular`)을 생성해야 합니다. 계정은 플랫폼 일반 사용자여야 하며 '운영자' 역할을 가진 프로젝트 운영자로 초대되어야 합니다. 이 튜토리얼에서는 `project-regular`로 로그인하여 `demo-workspace` 작업 공간의 `demo-project` 프로젝트에서 작업합니다. 자세한 내용은 [작업 공간, 프로젝트, 사용자 및 역할 생성](../../../quick-start/create-workspace-and-project/)을 참조하십시오.

## 핸즈온 랩

### 1단계: 앱 스토어에서 Memcached 배포

1. `demo-project` 프로젝트의 **Overview** 페이지에서 왼쪽 상단의 **App Store**를 클릭합니다.

2. Memcached를 찾아 **App Information** 페이지에서 **Install**를 클릭합니다.

3. 이름을 설정하고 앱 버전을 선택합니다. Memcached가 `demo-project`에 배포되었는지 확인하고 **Next**를 클릭합니다.

4. **App Settings**에서 기본 구성을 사용하거나 YAML 파일을 직접 편집하여 구성을 사용자 지정할 수 있습니다. 계속하려면 **Install**를 클릭하세요.

5. Memcached가 실행될 때까지 기다립니다.

### 2단계: Memcached 액세스

1. **Services**로 이동하여 Memcached의 서비스 이름을 클릭합니다.

2. 세부 정보 페이지의 **Ports** 및 **Pods**에서 각각 포트 번호와 Pod의 IP 주소를 찾을 수 있습니다.

3. Memcached 서비스는 헤드리스이므로 Pod IP 및 포트 번호를 통해 클러스터 내부에 액세스합니다. Memcached `telnet` 명령의 기본 구문은 `telnet HOST PORT`입니다. 예를 들어:

   ```bash
   # telnet 10.10.235.3 11211
   Trying 10.10.235.3...
   Connected to 10.10.235.3.
   Escape character is '^]'.
   set runoob 0 900 9
   memcached
   STORED
   ```

4. 자세한 내용은 [Memcached](https://memcached.org/)를 참조하세요.
