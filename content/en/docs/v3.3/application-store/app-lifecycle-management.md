---
title: "Kubernetes Application Lifecycle Management"
keywords: 'Kubernetes, Kuberix, app-store'
description: 'Manage your app across the entire lifecycle, including submission, review, test, release, upgrade and removal.'
linkTitle: 'Application Lifecycle Management'
weight: 14100
---

Kuberix Enterprise는 오픈 소스 멀티 클라우드 애플리케이션 관리 플랫폼인 [OpenPitrix](https://github.com/openpitrix/openpitrix)를 통합하여 전체 수명 주기 동안 쿠버네티스 애플리케이션을 관리하는 앱 스토어를 설정합니다. 앱 스토어는 두 가지 종류의 애플리케이션 배포를 지원합니다.

- **템플릿 기반 앱**은 개발자 및 ISV(독립 소프트웨어 공급업체)가 작업 공간에서 사용자와 애플리케이션을 공유할 수 있는 방법을 제공합니다. 작업 공간 내에서 타사 앱 리포지토리를 가져올 수도 있습니다.
- **구성된 앱**은 사용자가 여러 마이크로서비스를 사용하여 완전한 애플리케이션을 신속하게 구축할 수 있도록 도와줍니다. Kuberix Enterprise는 사용자가 기존 서비스를 선택하거나 새로운 서비스를 생성하여 원스톱 콘솔에서 구성된 앱을 생성할 수 있도록 합니다.

[Redis](https://redis.io/)를 예제 애플리케이션으로 사용하는 이 튜토리얼은 제출, 검토, 테스트, 릴리스, 업그레이드 및 제거를 포함한 전체 수명 주기 동안 쿠버네티스 앱을 관리하는 방법을 보여줍니다.

## 전제 조건

- [Kuberix Enterprise 앱 스토어(OpenPitrix)](../../pluggable-components/app-store/)를 활성화해야 합니다.
- 작업 공간, 프로젝트 및 사용자(`project-regular`)를 생성해야 합니다. 자세한 내용은 [작업 공간, 프로젝트, 사용자 및 역할 생성](../../quick-start/create-workspace-and-project/)을 참조하십시오.

## 핸즈온 랩

### 1단계: 사용자 지정 역할 및 두 명의 사용자 생성

먼저 두 명의 사용자를 생성해야 합니다. 하나는 ISV(`isv`)용이고 다른 하나(`reviewer`)는 앱 기술 검토자용입니다.

1. 사용자 `admin`으로 Kuberix Enterprise 콘솔에 로그인합니다. 왼쪽 상단 모서리에서 **Platform**을 클릭하고 **Access Control**를 선택합니다. **Role**에서 **Create**를 클릭합니다.

2. 'app-review'와 같은 역할 이름을 설정하고 **Change Access**을 클릭합니다.

3. **App Management**의 권한 목록에서 **App template management**와 **View App template**를 선택한 후 **OK**을 클릭합니다.

   {{< notice note >}}

   '앱 리뷰' 역할이 부여된 사용자는 플랫폼에서 앱 스토어를 보고 리뷰 및 제거를 포함한 앱을 관리할 수 있는 권한이 있습니다.

   {{</ notice >}} 

4. 이제 역할이 준비되었으므로 사용자를 만들고 'app-review' 역할을 부여해야 합니다. **User**에서 **Create**를 클릭합니다. 필요한 정보를 제공하고 **OK**을 클릭합니다.

5. 마찬가지로 다른 사용자 `isv`를 만들고 `platform-regular` 역할을 부여합니다.

6. 위에서 생성한 두 사용자를 `demo-workspace`와 같은 기존 작업 공간에 초대하고 `workspace-admin` 역할을 부여합니다.

### 2단계: 신청서 업로드 및 제출

1. Kuberix Enterprise에 'isv'로 로그인하고 작업 공간으로 이동합니다. 나중에 사용할 수 있도록 예제 앱 Redis를 이 작업 공간에 업로드해야 합니다. 먼저 [Redis 11.3.4](https://github.com/kubesphere/tutorial/raw/master/tutorial%205%20-%20app-store/redis-11.3.4.tgz) 앱을 다운로드하고 *를 클릭합니다. **APp Template**의 *업로드 템플릿**.

   {{< notice note >}}

   이 예제에서는 업그레이드 기능을 시연하기 위해 새 버전의 Redis가 나중에 업로드됩니다.

   {{</ notice >}} 

2. 표시되는 대화 상자에서 **Helm Chart Upload**를 클릭하여 차트 파일을 업로드합니다. 계속하려면 **OK**을 클릭하세요.

3. **App Information** 아래에 앱의 기본 정보가 표시됩니다. 앱 아이콘을 업로드하려면 **Upload Icon**을 클릭하세요. 건너뛰고 **OK**을 직접 클릭할 수도 있습니다.

   {{< notice note >}}

   앱 아이콘의 최대 허용 해상도는 96 x 96픽셀입니다.

   {{</ notice >}} 

4. 앱은 성공적으로 업로드된 후 템플릿 목록에 **Developing** 상태로 표시됩니다. 이는 이 앱이 개발 중임을 의미합니다. 업로드된 앱은 동일한 작업 공간의 모든 구성원이 볼 수 있습니다.

5. 목록에서 Redis를 클릭하여 앱 템플릿의 상세 페이지로 이동합니다. **Modify**을 클릭하면 이 앱의 기본 정보를 수정할 수 있습니다.

6. 팝업 창에서 필드를 지정하여 앱의 기본 정보를 사용자 정의할 수 있습니다.

7. **OK**을 클릭하여 변경 사항을 저장한 다음 쿠버네티스에 배포하여 이 애플리케이션을 테스트할 수 있습니다. 초안 버전을 클릭하여 메뉴를 확장하고 **설치**를 클릭합니다.

   {{< notice note >}} 

   앱을 테스트하고 싶지 않다면 직접 제출하여 검토를 받을 수 있습니다. 그러나 특히 프로덕션 환경에서 검토를 위해 제출하기 전에 먼저 앱 배포 및 기능을 테스트하는 것이 좋습니다. 이를 통해 문제를 미리 감지하고 검토 프로세스를 가속화할 수 있습니다.

   {{</ notice >}} 

8. 앱을 배포할 클러스터 및 프로젝트를 선택하고 앱에 대해 다른 구성을 설정한 다음 **Install**를 클릭합니다.

   {{< notice note >}}

   일부 앱은 양식에 설정된 모든 구성으로 배포할 수 있습니다. 토글 스위치를 사용하여 양식에 지정해야 하는 모든 매개변수가 포함된 YAML 파일을 볼 수 있습니다.

   {{</ notice >}} 

9. 몇 분 동안 기다린 다음 **App Instance** 탭으로 전환합니다. Redis가 성공적으로 배포되었음을 알 수 있습니다.

10. 문제가 발견되지 않은 상태로 앱을 테스트한 후 **Submmit to Release**을 클릭하여 이 애플리케이션을 제출하여 출시할 수 있습니다.

    {{< notice note >}}

버전 번호는 숫자로 시작하고 소수점을 포함해야 합니다.

{{</ notice >}}

11. 앱이 제출되면 앱 상태가 **Submitted**으로 변경됩니다. 이제 앱 리뷰어가 릴리스할 수 있습니다.

### 3단계: 애플리케이션 출시

1. Kuberix Enterprise에서 로그아웃하고 'app-reviewer'로 다시 로그인합니다. 왼쪽 상단 모서리에서 **Platform**을 클릭하고 **App Store Management**를 선택합니다. **App Release** 페이지에서 이전 단계에서 제출된 앱은 **Unreleased** 탭 아래에 표시됩니다.

2. 이 앱을 출시하려면 해당 앱을 클릭하여 팝업 창에서 앱 정보, 소개, 차트 파일 및 업데이트 로그를 확인합니다.

3. 검토자는 앱이 앱 스토어의 출시 기준을 충족하는지 여부를 결정해야 합니다. **Pass**를 클릭하여 승인하거나 **Cancel**를 클릭하여 앱 제출을 거부합니다.

### 4단계: 앱 스토어에 애플리케이션 출시

앱이 승인되면 'isv'는 Redis 애플리케이션을 앱 스토어에 출시하여 플랫폼의 모든 사용자가 이 애플리케이션을 찾고 배포할 수 있도록 합니다.

1. Kuberix Enterprise에서 로그아웃하고 'isv'로 다시 로그인합니다. 작업 공간으로 이동하여 **Template-Based Apps** 페이지에서 Redis를 클릭합니다. 세부 정보 페이지에서 버전 메뉴를 확장한 다음 **Release to Store**를 클릭합니다. 팝업 프롬프트에서 **OK**을 클릭하여 확인합니다.

2. **App Release**에서 앱 상태를 확인할 수 있습니다. **Activated**은 앱 스토어에서 사용할 수 있음을 의미합니다.

3. **View On Store**를 클릭하여 앱 스토어의 **Version** 페이지로 이동합니다. 또는 왼쪽 상단의 **App Store**를 클릭하면 앱을 볼 수도 있습니다.

   {{< notice note >}}

   앱 스토어에서 두 개의 Redis 앱을 볼 수 있으며 그 중 하나는 쿠버네티스에 내장된 앱입니다. 새로 출시된 앱은 앱 스토어의 목록 시작 부분에 표시됩니다.

   {{</ notice >}} 

4. 이제 작업 공간의 사용자는 앱 스토어에서 Redis를 설치할 수 있습니다. 앱을 쿠버네티스에 설치하려면 앱을 클릭하여 **App Information** 페이지로 이동하고 **Install**를 클릭합니다.
   
   {{< notice note >}}
   
   응용 프로그램을 설치하는 데 문제가 있고 **Status** 열에 **Failed**가 표시되는 경우 **Failed** 아이콘 위로 커서를 가져가면 오류 메시지가 표시됩니다.
   
   {{</ notice >}}

### 5단계: 애플리케이션 카테고리 생성

'app-reviewer'는 기능과 용도에 따라 다양한 유형의 애플리케이션에 대해 여러 범주를 만들 수 있습니다. 태그를 설정하는 것과 유사하며 카테고리는 앱스토어에서 빅데이터, 미들웨어, IoT 등의 필터로 사용할 수 있습니다.

1. Kuberix Enterprise에 '앱 리뷰어'로 로그인합니다. 카테고리를 만들려면 **App Store Management** 페이지로 이동하여 <img src="/images/docs/v3.3/appstore/application-lifecycle-management/plus.png" height="20px">를 클릭하세요. **App Categories**에서.

2. 대화 상자에서 범주의 이름과 아이콘을 설정한 다음 **OK**을 클릭합니다. Redis의 경우 **Name** 필드에 '데이터베이스'를 입력할 수 있습니다.

   {{< notice note >}}

   일반적으로 앱 리뷰어는 사전에 필요한 카테고리를 생성하고 ISV는 리뷰를 위해 제출하기 전에 앱이 표시될 카테고리를 선택합니다. 새로 생성된 카테고리에는 앱이 없습니다.

   {{</ notice >}} 

3. 카테고리가 생성되면 앱에 카테고리를 할당할 수 있습니다. **Uncategorized**에서 Redis를 선택하고 **Change Category**을 클릭합니다.

4. 대화 상자의 드롭다운 목록에서 범주(**Database**)를 선택하고 **OK**을 클릭합니다.

5. 앱이 예상대로 카테고리에 표시됩니다.

### 6단계: 새 버전 추가

작업 공간 사용자가 앱을 업그레이드할 수 있도록 하려면 먼저 Kuberix Enterprise에 새 앱 버전을 추가해야 합니다. 예제 앱의 새 버전을 추가하려면 아래 단계를 따르세요.

1. Kuberix Enterprise 에 'isv'로 다시 로그인하고 **Template-Based Apps**로 이동합니다. 목록에서 앱 Redis를 클릭합니다.

2. [Redis 12.0.0](https://github.com/KuberixEnterprise/tutorial/raw/master/tutorial%205%20-%20app-store/redis-12.0.0.tgz)을 다운로드합니다. 이 튜토리얼의 데모용 Redis 버전. **Versions** 탭에서 오른쪽에 있는 **New Version**을 클릭하여 방금 다운로드한 패키지를 업로드합니다.

3. **Upload Helm Chart**를 클릭하고 업로드된 후 **OK**을 클릭합니다.

4. 새 앱 버전이 버전 목록에 표시됩니다. 클릭하여 메뉴를 확장하고 새 버전을 테스트할 수 있습니다. 또한 위에 표시된 단계와 동일하게 검토를 위해 제출하고 앱 스토어에 출시할 수도 있습니다.

### 7단계: 애플리케이션 업그레이드

새 버전이 앱 스토어에 출시된 후 모든 사용자는 이 응용 프로그램을 새 버전으로 업그레이드할 수 있습니다.

{{< notice note >}}

아래 단계를 따르려면 먼저 이전 버전 중 하나의 앱을 배포해야 합니다. 이 예에서 Redis 11.3.4는 이미 'demo-project' 프로젝트에 배포되었으며 새 버전 12.0.0이 앱 스토어에 출시되었습니다.

{{</ notice >}} 

1. Kuberix Enterprise에 `project-regular`로 로그인하고 프로젝트의 **Apps** 페이지로 이동한 후 업그레이드할 앱을 클릭합니다.

2. **More**를 클릭하고 드롭다운 목록에서 **Edit Settings**을 선택합니다.

3. 나타나는 창에서 응용 프로그램 구성의 YAML 파일을 볼 수 있습니다. 오른쪽의 드롭다운 목록에서 새 버전을 선택합니다. 새 버전의 YAML 파일을 사용자 지정할 수 있습니다. 이 가이드에서 **Update**를 클릭하여 기본 구성을 직접 사용합니다.

   {{< notice note >}}

   오른쪽의 드롭다운 목록에서 왼쪽과 동일한 버전을 선택하여 YAML 파일을 통해 현재 애플리케이션 구성을 사용자 지정할 수 있습니다.

   {{</ notice >}}

4. **Apps** 페이지에서 앱이 업그레이드 중임을 확인할 수 있습니다. 업그레이드가 완료되면 상태가 **Running**으로 변경됩니다.

### 8단계: 지원 중단

앱 스토어에서 앱을 완전히 제거하거나 특정 앱 버전을 일시 중지하도록 선택할 수 있습니다.

1. Kuberix Enterprise에 'App-Reviewer'로 로그인합니다. 왼쪽 상단 모서리에서 **Platform**을 클릭하고 **App Store Management**를 선택합니다. **App Store** 페이지에서 Redis를 클릭합니다. 

2. 세부 정보 페이지에서 **Suspend App**을 클릭하고 대화 상자에서 **OK**을 선택하여 앱 스토어에서 앱을 제거하는 작업을 확인합니다. 

   {{< notice note >}}

   앱 스토어에서 앱을 제거해도 앱을 사용하는 테넌트에는 영향을 미치지 않습니다.

   {{</ notice >}} 

3. 앱 스토어에서 앱을 다시 사용할 수 있게 하려면 **Activate App**를 클릭합니다.

4. 특정 앱 버전을 일시 중지하려면 버전 메뉴를 확장하고 **Suspend Version**를 클릭합니다. 표시되는 대화 상자에서 **OK**을 클릭하여 확인합니다.

   {{< notice note >}}

   앱 버전이 일시 중단된 후에는 이 버전을 앱 스토어에서 사용할 수 없습니다. 앱 버전을 일시 중단해도 이 버전을 사용하는 테넌트에는 영향을 미치지 않습니다.

   {{</ notice >}}

5. 앱 스토어에서 앱 버전을 다시 사용할 수 있게 하려면 **Activate Version**를 클릭합니다.   

