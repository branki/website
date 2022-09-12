---
title: "Deploy NGINX on Kuberix Enterprise"
keywords: 'Kuberix, Kubernetes, Installation, NGINX'
description: 'Learn how to deploy NGINX from the App Store of Kuberix Enterprise and access its service.'
linkTitle: "Deploy NGINX on Kuberix Enterprise"
weight: 14270
---

[NGINX](https://www.nginx.com/)는 웹 서비스, 역방향 프록시, 캐싱, 로드 밸런싱, 미디어 스트리밍 등을 위한 오픈 소스 소프트웨어 애플리케이션입니다.

이 튜토리얼은 Kuberix Enterprise의 앱 스토어에서 NGINX를 배포하는 예를 안내합니다.

## 전제 조건

- [OpenPitrix 시스템 활성화](../../../pluggable-components/app-store/)를 확인하십시오.
- 이 튜토리얼에서는 작업 공간, 프로젝트 및 사용자 계정(`project-regular`)을 생성해야 합니다. 계정은 플랫폼 일반 사용자여야 하며 '운영자' 역할을 가진 프로젝트 운영자로 초대되어야 합니다. 이 튜토리얼에서는 `project-regular`로 로그인하여 `demo-workspace` 작업 공간의 `demo-project` 프로젝트에서 작업합니다. 자세한 내용은 [작업 공간, 프로젝트, 사용자 및 역할 생성](../../../quick-start/create-workspace-and-project/)을 참조하십시오.

## 핸즈온 랩

### 1단계: 앱 스토어에서 NGINX 배포

1. `demo-project` 프로젝트의 **Overview** 페이지에서 왼쪽 상단의 **App Store**를 클릭합니다.

2. NGINX를 찾아 **App Information** 페이지에서 **Install**를 클릭합니다.

3. 이름을 설정하고 앱 버전을 선택합니다. NGINX가 `demo-project`에 배포되었는지 확인하고 **Next**를 클릭합니다.

4. **App Settings**에서 앱에 배포할 복제본 수를 지정하고 필요에 따라 Ingress를 활성화합니다. 완료되면 **Install**를 클릭합니다.

   {{< 공지사항 >}}

   NGINX에 대해 더 많은 값을 지정하려면 토글 스위치를 사용하여 앱의 매니페스트를 YAML 형식으로 보고 구성을 편집합니다.

   {{</ 공지 >}}

5. NGINX가 실행될 때까지 기다립니다.

### 2단계: NGINX에 액세스

클러스터 외부에서 NGINX에 액세스하려면 먼저 NodePort를 통해 앱을 노출해야 합니다.

1. **Services**로 이동하여 NGINX의 서비스 이름을 클릭합니다.

2. 서비스 세부 정보 페이지에서 **More**를 클릭하고 드롭다운 목록에서 **Edit External Access**을 선택합니다.

3. **Access Method**으로 **NodePort**를 선택하고 **OK**을 클릭합니다. 자세한 내용은 [프로젝트 게이트웨이](../../../project-administration/project-gateway/)를 참조하십시오.

4. **Ports**에서 포트가 노출된 것을 볼 수 있습니다.

5. `<NodeIP>:<NodePort>`를 통해 NGINX에 접속합니다.

   ![액세스-nginx](/images/docs/v3.3/appstore/built-in-apps/nginx-app/access-nginx.png)

   {{< notice note >}}

   쿠버네티스 클러스터가 배포된 위치에 따라 보안 그룹에서 포트를 열고 관련 포트 전달 규칙을 구성해야 할 수 있습니다.

   {{</ notice  >}}

6. 자세한 내용은 [NGINX 공식 문서](https://docs.nginx.com/?_ga=2.48327718.1445131049.1605510038-1186152749.1605510038)를 참조한다.
