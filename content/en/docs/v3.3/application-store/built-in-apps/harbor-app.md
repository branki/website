---
title: "Deploy Harbor on Kuberix Enterprise"
keywords: 'Kubernetes, Kuberix, Harbor, app-store'
description: 'Learn how to deploy Harbor from the App Store of KubeSphere and access its service.'
linkTitle: "Deploy Harbor on Kuberix Enterprise"
weight: 14220
---
[Harbor](https://goharbor.io/)는 정책 및 역할 기반 액세스 제어로 아티팩트를 보호하고, 이미지가 스캔되고 취약성이 없는지 확인하고, 이미지를 신뢰할 수 있는 것으로 서명하는 오픈 소스 레지스트리입니다.

이 튜토리얼은 Kuberix Enterprise의 앱 스토어에서 [Harbor](https://goharbor.io/)를 배포하는 예를 안내합니다.

## 전제 조건

- [OpenPitrix 시스템 활성화](../../../pluggable-components/app-store/)를 확인하십시오.
- 이 튜토리얼에서는 작업 공간, 프로젝트 및 사용자 계정을 생성해야 합니다. 계정은 플랫폼 일반 사용자여야 하며 '운영자' 역할을 가진 프로젝트 운영자로 초대되어야 합니다. 이 튜토리얼에서는 `project-regular`로 로그인하여 `demo-workspace` 작업 공간의 `demo-project` 프로젝트에서 작업합니다. 자세한 내용은 [작업 공간, 프로젝트, 사용자 및 역할 생성](../../../quick-start/create-workspace-and-project/)을 참조하십시오.

## 실습 (핸즈온)

### 1단계: 앱 스토어에서 Harbor 배포

1. `demo-project` 프로젝트의 **Overview** 페이지에서 왼쪽 상단의 **App Store**를 클릭합니다.

2. Harbor를 찾아 **App Information** 페이지에서 **Install**를 클릭합니다.

3. 이름을 설정하고 앱 버전을 선택합니다. Harbor가 `demo-project`에 배포되었는지 확인하고 **Next**를 클릭합니다.

4. **App Settings** 페이지에서 Harbour의 설정 파일을 수정합니다. 다음 필드에 주의하십시오.

   `type`: 하버 서비스에 액세스하는 데 사용하는 방법입니다. 이 예제에서는 `nodePort`를 사용합니다.

   `tls`: HTTPS를 활성화할지 여부를 지정합니다. 대부분의 경우 '거짓'으로 설정합니다.

   `externalURL`: 테넌트에게 노출되는 URL입니다.

   {{< notice note >}}

   - 'externalURL'을 지정하는 것을 잊지 마십시오. 이 필드는 하버에 액세스하는 데 문제가 있는 경우 매우 유용할 수 있습니다.

   - 이 튜토리얼에서 HTTP 프로토콜과 해당 'nodePort'를 사용하고 있는지 확인하십시오. 자세한 내용은 FAQ의 [구성 예시](#faq)를 참조하세요.

   {{</ notice >}} 

   구성 편집을 마치면 **설치**를 클릭하여 계속합니다.

5. 항구가 가동될 때까지 기다리십시오.

### 2단계: Harbor 액세스

1. 구성 파일에 설정한 'expose.type' 필드에 따라 접근 방식이 다를 수 있습니다. 이 예제에서는 `nodePort`를 사용하여 Harbor에 액세스하므로 이전 단계에서 설정한 대로 `http://<NodeIP>:30002`로 이동합니다.

   ![harbor-login](/images/docs/v3.3/appstore/built-in-apps/harbor-app/harbor-login.jpg)

   {{< notice note >}}

   쿠버네티스 클러스터가 배포된 위치에 따라 보안 그룹에서 포트를 열고 관련 포트 전달 규칙을 구성해야 할 수 있습니다.

   {{</ notice >}} 

2. 기본 계정과 비밀번호(`admin/Harbor12345`)를 사용하여 Harbor에 로그인합니다. 비밀번호는 구성 파일의 'harborAdminPassword' 필드에 정의되어 있습니다.

   ![하버 대시보드](/images/docs/v3.3/appstore/built-in-apps/harbor-app/harbor-dashboard.jpg)

## 자주하는 질문

1. HTTP 로그인을 활성화하는 방법은 무엇입니까?

   위의 1단계에서 `tls.enabled`를 `false`로 설정합니다. `externalURL`의 프로토콜은 `expose.nodePort.ports`와 동일해야 합니다.

   도커 로그인을 사용하는 경우 'externalURL'을 'daemon.json'의 'insecure-registries' 중 하나로 설정한 다음 도커를 다시 로드하세요.

   다음은 참조용 구성 파일의 예입니다. 댓글에 특히 주의하세요.
   
   ```yaml
   ## NOTICE 192.168.0.9 is the example IP address and you must use your own.
   expose:
     type: nodePort
     tls:
       enabled: false
       secretName: ""
       notarySecretName: ""
       commonName: "192.168.0.9"  # Change commonName to your own.
     nodePort:
       # The name of NodePort service
       name: harbor
       ports:
         http:
           # The service port Harbor listens on when serving with HTTP
           port: 80
           # The node port Harbor listens on when serving with HTTP
           nodePort: 30002
         https:
           # The service port Harbor listens on when serving with HTTPS
           port: 443
           # The node port Harbor listens on when serving with HTTPS
           nodePort: 30003
         # Only needed when notary.enabled is set to true
         notary:
           # The service port Notary listens on
           port: 4443
           # The node port Notary listens on
           nodePort: 30004
   
   externalURL: http://192.168.0.9:30002 # Use your own IP address.
   
   # The initial password of Harbor admin. Change it from portal after launching Harbor
   harborAdminPassword: "Harbor12345"
   # The secret key used for encryption. Must be a string of 16 chars.
   secretKey: "not-a-secure-key"
   ```

2. HTTPS 로그인을 활성화하는 방법은 무엇입니까?

    ㅏ. 자체 서명된 인증서를 사용합니다.
      * 1단계의 구성 파일에서 `tls.enabled`를 `true`로 설정하고 그에 따라 `externalURL`을 수정합니다.
      * Pod `harbor-core` \의 `/etc/core/ca`에 저장된 CA 인증서를 호스트에 복사합니다.
      * 먼저 호스트의 CA 인증서를 신뢰한 다음 Docker를 다시 시작하십시오.

    비. 공개 SSL을 사용합니다.
      * 인증서를 비밀로 추가합니다.
      * 1단계의 구성 파일에서 `tls.enabled`를 `true`로 설정하고 그에 따라 `externalURL`을 수정합니다.
      * `tls.secretName`을 수정합니다.

자세한 내용은 [하버 문서](https://goharbor.io/docs/2.1.0/)를 참조하세요.
