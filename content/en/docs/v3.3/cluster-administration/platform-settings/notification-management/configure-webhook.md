---
title: "Configure Webhook Notifications"
keywords: 'Kuberix, Kubernetes, custom, platform, webhook'
description: 'Configure a webhook server to receive platform notifications through the webhook.'
linkTitle: "Configure Webhook Notifications"
weight: 8726
---

웹훅은 앱이 특정 이벤트에 의해 트리거되는 알림을 보내는 방법입니다. 실시간으로 다른 애플리케이션에 정보를 전달하여 사용자가 즉시 알림을 받을 수 있도록 합니다.

이 자습서에서는 플랫폼 알림을 수신하도록 웹훅 서버를 구성하는 방법을 설명합니다.

## 전제 조건

`platform-admin` 역할이 부여된 사용자를 준비해야 합니다. 자세한 내용은 [작업 공간, 프로젝트, 사용자 및 역할 생성](../../../../quick-start/create-workspace-and-project/)을 참조하십시오.

## 웹훅 서버 구성

1. Kuberix Enterprise 웹 콘솔에 `platform-admin` 사용자로 로그인합니다.

2. 왼쪽 상단의 **Platform**을 클릭하고 **Platform Settings**을 선택합니다.

3. 왼쪽 탐색 창에서 **Notification Management** 아래의 **Notification Configuration**을 클릭하고 **Webhook**을 선택합니다.

4. **Webhook** 탭 페이지에서 다음 매개변수를 설정합니다.

   - **Webhook URL**: Webhook 서버의 URL입니다.

   - **Verification Type**: 웹훅 인증 방식입니다.
     - **No authentication**: 인증을 건너뜁니다. 모든 알림은 URL로 보낼 수 있습니다.
     - **Bearer token**: 인증을 위해 토큰을 사용합니다.
     - **Basic authentication**: 사용자 이름과 비밀번호를 사용하여 인증합니다.

   {{< notice note >}}
   
   현재 Kuberix Enterprise는 TLS 연결(HTTPS)을 지원하지 않습니다. HTTPS URL을 사용하는 경우 **Skip TLS verification (insecure)**를 선택해야 합니다.

   {{</notice>}}

5. **Notification Conditions** 왼쪽의 체크박스를 선택하여 알림 조건을 설정합니다.

    - **Label**: 알림 정책의 이름, 심각도 또는 모니터링 대상입니다. 레이블을 선택하거나 레이블을 사용자 정의할 수 있습니다.
    - **Operator**: 레이블과 값 간의 매핑. 연산자에는 **Includes values**, **Does not include values**, **Exists** 및 **Does not exist**이 포함됩니다.
    - **Values**: 레이블과 관련된 값입니다.
    
    {{< notice note >}}

   - 연산자 **Includes values** 및 **Does not include values**에는 하나 이상의 레이블 값이 필요합니다. 캐리지 리턴을 사용하여 값을 구분합니다.
   - 연산자 **Exists** 및 **Does not exist**는 레이블이 있는지 여부를 결정하며 레이블 값이 필요하지 않습니다.

    {{</ notice >}}

6. **Add**를 클릭하여 알림 조건을 추가하거나 <img src="/images/docs/v3.3/common-icons/trashcan.png" width='25' height='25' alt를 클릭할 수 있습니다. ="icon" /> 알림 조건 오른쪽에 있는 조건을 삭제합니다.

7. 구성이 완료되면 **Send Test Message**를 클릭하여 확인할 수 있습니다.

8. 오른쪽 상단 모서리에서 **Disabled** 토글을 켜서 알림을 활성화하거나 **Enabled** 토글을 꺼서 비활성화할 수 있습니다.

9. 완료한 후 **OK**을 클릭합니다.

   {{< notice note >}}

   - 알림 조건이 설정되면 수신자는 조건에 맞는 알림만 받게 됩니다.
   - 기존 설정을 변경할 경우 **OK**을 눌러 적용해야 합니다.

   {{</ notice >}} 
