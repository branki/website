---
title: "Use an LDAP Service"
keywords: "LDAP, identity provider, external, authentication"
description: "How to use an LDAP service."

linkTitle: "Use an LDAP Service"
weight: 12220
---

이 문서에서는 LDAP 서비스를 외부 ID 공급자로 사용하는 방법을 설명합니다. 이를 통해 LDAP 서비스에 대해 사용자를 인증할 수 있습니다.

## 전제 조건

* 쿠버네티스 클러스터를 배포하고 클러스터에 Kuberix Enterprise를 설치해야 합니다. 자세한 내용은 [Linux에 설치](/docs/v3.3/installing-on-linux/) 및 [쿠버네티스에 설치](/docs/v3.3/installing-on-kubernetes/)를 참조하십시오.
* LDAP 서비스의 관리자 고유 이름(DN)과 관리자 비밀번호를 얻어야 합니다.

## 절차

1. Kuberix Enterprise에 `admin`으로 로그인하고 커서를 <img src="/images/docs/v3.3/access-control-and-account-management/external-authentication/set-up-external-authentication"으로 이동합니다. /toolbox.png" width="20px" height="20px" alt="icon"> 오른쪽 하단의 **kubectl**을 클릭하고 다음 명령을 실행하여 CRD의 `ke-installer`를 편집합니다. '클러스터 구성':

   ```bash
   kubectl -n ke-system edit cc ke-installer
   ```

   Example:

   ```yaml
   spec:
     authentication:
       jwtSecret: ''
       maximumClockSkew: 10s
       multipleLogin: true
       oauthOptions:
         accessTokenMaxAge: 1h
         accessTokenInactivityTimeout: 30m
         identityProviders:
         - name: LDAP
           type: LDAPIdentityProvider
           mappingMethod: auto
           provider:
             host: 192.168.0.2:389
             managerDN: uid=root,cn=users,dc=nas
             managerPassword: ********
             userSearchBase: cn=users,dc=nas
             loginAttribute: uid
             mailAttribute: mail
   ```
   
2. `spec:authentication` 섹션에서 `oauthOptions:identityProviders` 이외의 필드를 구성합니다. 자세한 내용은 [외부 인증 설정](../set-up-external-authentication/)을 참조하십시오.

3. 'oauthOptions:identityProviders' 섹션에서 필드를 구성합니다.

   * `name`: 사용자 정의 LDAP 서비스 이름입니다.
   * `type`: LDAP 서비스를 ID 공급자로 사용하려면 값을 `LDAPIdentityProvider`로 설정해야 합니다.
   * `mappingMethod`: 계정 매핑 방법입니다. 값은 'auto' 또는 'lookup'일 수 있습니다.
     * 값이 'auto'(기본값)인 경우 새 사용자 이름을 지정해야 합니다. Kuberix Enterprise는 사용자 이름에 따라 자동으로 사용자를 생성하고 사용자를 LDAP 사용자에 매핑합니다.
     * 값이 'lookup'인 경우 4단계를 수행하여 기존 Kuberix Enterprise 사용자를 LDAP 사용자에 수동으로 매핑해야 합니다.
   * `제공자`:
     * `host`: LDAP 서비스의 주소 및 포트 번호입니다.
     * `managerDN`: LDAP 디렉토리에 바인딩하는 데 사용되는 DN입니다.
     * `managerPassword`: `managerDN`에 해당하는 비밀번호입니다.
     * `userSearchBase`: 사용자 검색 기반. 모든 LDAP 사용자를 찾을 수 있는 디렉토리 레벨의 DN으로 값을 설정하십시오.
     * `loginAttribute`: LDAP 사용자를 식별하는 속성입니다.
     * `mailAttribute`: LDAP 사용자의 이메일 주소를 식별하는 속성입니다.
   
4. 'mappingMethod'가 'lookup'으로 설정된 경우 다음 명령을 실행하고 레이블을 추가하여 Kuberix Enterprise 사용자를 LDAP 사용자에 매핑합니다. 'mappingMethod'가 'auto'로 설정된 경우 이 단계를 건너뜁니다.

   ```bash
   kubectl edit user <KuberixEnterprise username>
   ```

   ```yaml
   labels:
     iam.kuberix.io/identify-provider: <LDAP service name>
     iam.kuberix.io/origin-uid: <LDAP username>
   ```

5. 필드가 구성되면 변경 사항을 저장하고 ke-installer 재시작이 완료될 때까지 기다립니다.

   {{< notice note >}}
   
   Kuberix Enterprise 웹 콘솔은 ke-installer를 다시 시작하는 동안 사용할 수 없습니다. 재시작이 완료될 때까지 기다리십시오.
   
   {{</ notice >}}
   
6. Kuberix Enterprise을 사용하는 경우 LDAP를 구성한 후 다음 명령을 실행하고 `ke-installer`가 실행될 때까지 기다립니다.

   ```bash
   kubectl -n ke-system set image deployment/ke-apiserver *=KuberixEnterprise/ke-apiserver:v3.2.1
   ```
   
   {{< notice note >}}
   
   Kuberix Enterprise 3.2.1을 사용하는 경우 이 단계를 건너뛰십시오.
   
   {{</ notice >}}
   
7. Kuberix Enterprise 로그인 페이지로 이동하여 로그인할 LDAP 사용자의 사용자 이름과 비밀번호를 입력합니다.

   {{< notice note >}}

   LDAP 사용자의 사용자 이름은 'loginAttribute'로 지정된 속성 값입니다.

   {{</ notice >}}
