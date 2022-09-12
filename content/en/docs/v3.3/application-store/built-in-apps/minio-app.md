---
title: "Deploy MinIO on Kuberix Enterprise"
keywords: 'Kubernetes, Kuberix, Minio, app-store'
description: 'Learn how to deploy Minio from the App Store of Kuberix Enterprise and access its service.'
linkTitle: "Deploy MinIO on Kuberix Enterprise"
weight: 14240
---
[MinIO](https://min.io/) 오브젝트 스토리지는 고성능 및 S3 API를 위해 설계되었습니다. 보안 요구 사항이 엄격한 대규모 사설 클라우드 환경에 이상적이며 다양한 범위의 워크로드에서 미션 크리티컬한 가용성을 제공합니다.

이 튜토리얼은 Kuberix Enterprise의 앱 스토어에서 MinIO를 배포하는 예를 안내합니다.

## 전제 조건

- [OpenPitrix 시스템 활성화](../../../pluggable-components/app-store/)를 확인하십시오.
- 이 튜토리얼에서는 작업 공간, 프로젝트 및 사용자 계정(`project-regular`)을 생성해야 합니다. 계정은 플랫폼 일반 사용자여야 하며 '운영자' 역할을 가진 프로젝트 운영자로 초대되어야 합니다. 이 튜토리얼에서는 `project-regular`로 로그인하여 `demo-workspace` 작업 공간의 `demo-project` 프로젝트에서 작업합니다. 자세한 내용은 [작업 공간, 프로젝트, 사용자 및 역할 생성](../../../quick-start/create-workspace-and-project/)을 참조하십시오.

## 핸즈온 랩

### 1단계: 앱 스토어에서 MinIO 배포

1. `demo-project` 프로젝트의 **Overview** 페이지에서 왼쪽 상단의 **App Store**를 클릭합니다.

2. MinIO를 찾아 **App Information** 페이지에서 **Install**를 클릭합니다.

3. 이름을 설정하고 앱 버전을 선택합니다. MiniIO가 `demo-project`에 배포되었는지 확인하고 **Next**를 클릭합니다.

4. **App Settings**에서 기본 구성을 사용하거나 YAML 파일을 직접 편집하여 구성을 사용자 지정할 수 있습니다. 계속하려면 **Install**를 클릭하세요.

5. MinIO가 실행될 때까지 기다립니다.

### 2단계: MinIO 브라우저에 액세스

클러스터 외부에서 MinIO에 액세스하려면 먼저 NodePort를 통해 앱을 노출해야 합니다.

1. **Services**로 이동하여 MinIO의 서비스 이름을 클릭합니다.

2. **More**를 클릭하고 드롭다운 메뉴에서 **Edit External Access**을 선택합니다.

3. **Access Method**으로 **NodePort**를 선택하고 **OK**을 클릭합니다. 자세한 내용은 [프로젝트 게이트웨이](../../../project-administration/project-gateway/)를 참조하십시오.

4. **Services** 페이지에서 **MinIO**를 클릭합니다. 표시되는 페이지의 **Ports** 아래에서 포트가 노출된 것을 볼 수 있습니다.

5. MinIO 브라우저에 접속하기 위해서는 MinIO의 설정 파일에 명시된 `accessKey`와 `secretKey`가 필요합니다. **Apps**의 **Template-Based Apps**로 이동하여 MinIO를 클릭하면 **Chart Files** 탭에서 이 두 필드의 값을 찾을 수 있습니다.

6. `accessKey` 및 `secretKey`를 사용하여 `<NodeIP>:<NodePort>`를 통해 MinIO 브라우저에 액세스합니다.

   ![MinIO 브라우저](/images/docs/v3.3/appstore/built-in-apps/minio-app/minio-browser.png)

   ![MinIO 브라우저 인터페이스](/images/docs/v3.3/appstore/built-in-apps/minio-app/minio-browser-interface.png)

   {{< notice note >}}

   쿠버네티스 클러스터가 배포된 위치에 따라 보안 그룹에서 포트를 열고 관련 포트 전달 규칙을 구성해야 할 수 있습니다.

   {{</ notice >}}

7. MinIO에 대한 자세한 내용은 [MinIO 공식 문서](https://docs.min.io/)를 참조하세요.
