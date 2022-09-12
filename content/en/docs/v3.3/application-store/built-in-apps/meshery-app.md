---
title: "Deploy Meshery on Kuberix Enterprise"
keywords: 'Kubernetes, Kuberix, Meshery,Serive Mesh, Layer5, app-store'
description: 'Learn how to deploy Meshery from the App Store of Kuberix Enterprise and access its service.'
linkTitle: "Deploy Meshery on Kuberix Enterprise"
weight: 14240
---
[Meshery](https://meshery.io/)는 쿠버네티스, 모든 서비스 메시 및 해당 워크로드의 채택, 운영 및 관리를 가능하게 하는 오픈 소스, 클라우드 네이티브 관리 평면입니다.

이 튜토리얼은 Kuberix Enterprise의 앱 스토어에서 Meshery를 배포하는 예를 안내합니다.

## 전제 조건

- [OpenPitrix 시스템 활성화](../../../pluggable-components/app-store/)를 확인하십시오.
- 이 튜토리얼에서는 작업 공간, 프로젝트 및 사용자 계정(`project-regular`)을 생성해야 합니다. 계정은 플랫폼 일반 사용자여야 하며 '운영자' 역할을 가진 프로젝트 운영자로 초대되어야 합니다. 이 튜토리얼에서는 `project-regular`로 로그인하여 `demo-workspace` 작업 공간의 `demo-project` 프로젝트에서 작업합니다. 자세한 내용은 [작업 공간, 프로젝트, 사용자 및 역할 생성](../../../quick-start/create-workspace-and-project/)을 참조하십시오.

## 핸즈온 랩

### 1단계: 앱 스토어에서 Meshery 배포

1. `demo-project` 프로젝트의 **Overview** 페이지에서 왼쪽 상단의 **App Store**를 클릭합니다.
2. 앱스토어에서 **Meshery**를 검색하고, 검색결과를 클릭하여 앱으로 진입합니다.

    ![meshery-app](/images/docs/v3.3/appstore/built-in-apps/meshery-app/meshery-app.png)

3. **App Information** 페이지에서 오른쪽 상단의 **Install**를 클릭합니다.

    ![meshery-install](/images/docs/v3.3/appstore/built-in-apps/meshery-app/Meshery-install.png)

4. 앱 설정 페이지에서 애플리케이션 **Name**, **Location**(네임스페이스로 사용), 앱 버전을 설정하고 오른쪽 상단에서 다음을 클릭합니다.

    ![meshery-info](/images/docs/v3.3/appstore/built-in-apps/meshery-app/Meshery-info.png)

5. 필요에 따라 **values.yaml** 파일을 구성하거나 **Install**를 클릭하여 기본 구성을 사용합니다.

    ![meshery-yaml](/images/docs/v3.3/appstore/built-in-apps/meshery-app/Meshery-yaml.png)

6. 배포가 완료될 때까지 기다립니다. 완료되면 **Meshery**가 Kuberix Enterprise에서 **Running**으로 표시됩니다.

    ![meshery-app-running](/images/docs/v3.3/appstore/built-in-apps/meshery-app/Meshery-app-running.png)

### 2단계: Meshery 대시보드에 액세스

1. **서비스**로 이동하여 Meshery의 서비스 이름을 클릭합니다.
2. **Resource Status** 페이지에서 Meshery의 **NodePort**를 복사합니다.

    ![meshery-service](/images/docs/v3.3/appstore/built-in-apps/meshery-app/Meshery-service.png)

3. 브라우저에 **${NodeIP}:${NODEPORT}**를 입력하여 Meshery 대시보드에 액세스합니다.

    ![meshery-dashboard](/images/docs/v3.3/appstore/built-in-apps/meshery-app/meshery-dashboard.png)

4. Meshery에 대한 자세한 내용은 [Meshery 공식 문서](https://docs.meshery.io/)를 참조하세요.
