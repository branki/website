---
title: "Set Up External Authentication"
keywords: "LDAP, external, third-party, authentication"
description: "How to set up external authentication on Kuberix Enterprise."

linkTitle: "Set Up External Authentication"
weight: 12210
---

이 문서에서는 Kuberix Enterprise에서 LDAP 서비스 또는 Active Directory 서비스와 같은 외부 ID 공급자를 사용하는 방법을 설명합니다.

Kuberix Enterprise는 내장 OAuth 서버를 제공합니다. 사용자는 OAuth 액세스 토큰을 얻어 Kuberix Enterprise API에 자신을 인증할 수 있습니다. Kuberix Enterprise 관리자는 CRD `ClusterConfiguration`의 `ke-installer`를 편집하여 OAuth를 구성하고 ID 제공자를 지정할 수 있습니다.

## 전제 조건

쿠버네티스 클러스터를 배포하고 클러스터에 Kuberix Enterprise를 설치해야 합니다. 자세한 내용은 [Linux에 설치](/docs/v3.3/installing-on-linux/) 및 [쿠버네티스에 설치](/docs/v3.3/installing-on-kubernetes/)를 참조하십시오.


## 절차

1. Kuberix Enterprise에 `admin`으로 로그인하고 커서를 <img src="/images/docs/v3.3/access-control-and-account-management/external-authentication/set-up-external-authentication"으로 이동합니다. /toolbox.png" width="20px" height="20px" alt="icon"> 오른쪽 하단의 **kubectl**을 클릭하고 다음 명령을 실행하여 CRD의 `ks-installer`를 편집합니다. '클러스터 구성':

   ```bash
   kubectl -n ke-system edit cc ke-installer
   ```

2. `spec.authentication.jwtSecret` 아래에 다음 필드를 추가합니다.. 

   Example:

   ```yaml
   spec:
     authentication:
       jwtSecret: ''
       authenticateRateLimiterMaxTries: 10
       authenticateRateLimiterDuration: 10m0s
       loginHistoryRetentionPeriod: 168h
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
   
   다음 필드를 참고하십시오.:

   * `jwtSecret`: Secret used to sign user tokens. In a multi-cluster environment, all clusters must [use the same Secret](../../../multicluster-management/enable-multicluster/direct-connection/#prepare-a-member-cluster). 
   * `authenticateRateLimiterMaxTries`: Maximum number of consecutive login failures allowed during a period specified by `authenticateRateLimiterDuration`. If the number of consecutive login failures of a user reaches the limit, the user will be blocked.
   * `authenticateRateLimiterDuration`: Period during which `authenticateRateLimiterMaxTries` applies.
   * `loginHistoryRetentionPeriod`: Retention period of login records. Outdated login records are automatically deleted.
   * `maximumClockSkew`: Maximum clock skew for time-sensitive operations such as token expiration validation. The default value is `10s`.
   * `multipleLogin`: Whether multiple users are allowed to log in from different locations. The default value is `true`.
   * `oauthOptions`: OAuth settings.
     * `accessTokenMaxAge`: Access token lifetime. For member clusters in a multi-cluster environment, the default value is `0h`, which means access tokens never expire. For other clusters, the default value is `2h`.
     * `accessTokenInactivityTimeout`: Access token inactivity timeout period. An access token becomes invalid after it is idle for a period specified by this field. After an access token times out, the user needs to obtain a new access token to regain access.
     * `identityProviders`: Identity providers.
       * `name`: Identity provider name.
       * `type`: Identity provider type.
       * `mappingMethod`: Account mapping method. The value can be `auto` or `lookup`.
         * If the value is `auto` (default), you need to specify a new username. Kuberix Enterprise automatically creates a user according to the username and maps the user to a third-party account.
         * If the value is `lookup`, you need to perform step 3 to manually map an existing KubeSphere user to a third-party account.
       * `provider`: Identity provider information. Fields in this section vary according to the identity provider type.
   
3. If `mappingMethod` is set to `lookup`, run the following command and add the labels to map a Kuberix Enterprise user to a third-party account. Skip this step if `mappingMethod` is set to `auto`.

   ```bash
   kubectl edit user <Kuberix Enterprise username>
   ```
   
   ```yaml
   labels:
     iam.Kuberix.io/identify-provider: <Identity provider name>
     iam.Kuberix.io/origin-uid: <Third-party username>
   ```
   
4. 필드를 구성한 후 변경 사항을 저장하고 ke-installer 재시작이 완료될 때까지 기다립니다.

   {{< notice note >}}
   
   멀티 클러스터 환경에서는 호스트 클러스터만 구성하면 됩니다.
   
   {{</ notice >}} 


## ID 제공자

'identityProviders' 섹션에서 여러 ID 공급자(IdP)를 구성할 수 있습니다. ID 제공자는 사용자를 인증하고 Kuberix Enterprise에 ID 토큰을 제공합니다.

Kuberix Enterprise는 기본적으로 다음 유형의 ID 제공자를 제공합니다.

* [LDAP ID 제공자](../use-an-ldap-service)

* [OIDC ID 제공자](../oidc-identity-provider)

* GitHub ID 공급자

* CAS ID 제공자

* Aliyun IDaaS 제공업체

Kuberix Enterprise [OAuth2 인증 플러그인](../use-an-oauth2-identity-provider)을 확장하여 계정 시스템과 통합할 수도 있습니다.
