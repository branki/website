---
title: "OIDC Identity Provider"
keywords: "OIDC, identity provider"
description: "How to use an external OIDC identity provider."

linkTitle: "OIDC Identity Provider"
weight: 12221
---

## OIDC ID 제공자

[OpenID Connect](https://openid.net/connect/)는 OAuth 2.0 사양 제품군을 기반으로 하는 상호 운용 가능한 인증 프로토콜입니다. 단순한 REST/JSON 메시지 흐름을 사용하여 "단순한 것을 단순하고 복잡한 것을 가능하게 만든다"는 디자인 목표를 가지고 있습니다. Keycloak, Okta, Dex, Auth0, Gluu, Casdoor 등과 같은 이전의 ID 프로토콜과 비교하여 개발자가 통합하기가 매우 쉽습니다.

## 전제 조건

쿠버네티스 클러스터를 배포하고 클러스터에 Kuberix Enterprise를 설치해야 합니다. 자세한 내용은 [Linux에 설치](/docs/v3.3/installing-on-linux/) 및 [쿠버네티스에 설치](/docs/v3.3/installing-on-kubernetes/)를 참조하십시오.

## 절차

1. Kuberix Enterprise에 `admin`으로 로그인하고 커서를 <img src="/images/docs/v3.3/access-control-and-account-management/external-authentication/set-up-external-authentication"으로 이동합니다. /toolbox.png" width="20px" height="20px" alt="icon"> 오른쪽 하단의 **kubectl**을 클릭하고 다음 명령을 실행하여 CRD의 `ke-installer`를 편집합니다. '클러스터 구성':

   ```bash
   kubectl -n ke-system edit cc ke-installer
   ```

2. `spec.authentication.jwtSecret` 아래에 다음 필드를 추가합니다. 

   *사용예제 [Google Identity Platform](https://developers.google.com/identity/protocols/oauth2/openid-connect)*:

   ```yaml
   spec:
     authentication:
       jwtSecret: ''
       authenticateRateLimiterMaxTries: 10
       authenticateRateLimiterDuration: 10m0s
       oauthOptions:
         accessTokenMaxAge: 1h
         accessTokenInactivityTimeout: 30m
         identityProviders:
         - name: google
           type: OIDCIdentityProvider
           mappingMethod: auto
           provider:
             clientID: '********'
             clientSecret: '********'
             issuer: https://accounts.google.com
             redirectURL:  'https://ke-console/oauth/redirect/google'
   ```

   다음과 같이 매개변수에 대한 설명을 참조하십시오.:

   | Parameter            | Description                                                  |
   | -------------------- | ------------------------------------------------------------ |
   | clientID             | The OAuth2 client ID.                                        |
   | clientSecret         | The OAuth2 client secret.                                    |
   | redirectURL          | The redirected URL to ke-console in the following format: `https://<Domain name>/oauth/redirect/<Provider name>`. The `<Provider name>` in the URL corresponds to the value of `oauthOptions:identityProviders:name`. |
   | issuer               | Defines how Clients dynamically discover information about OpenID Providers. |
   | preferredUsernameKey | Configurable key which contains the preferred username claims. This parameter is optional. |
   | emailKey             | Configurable key which contains the email claims. This parameter is optional. |
   | getUserInfo          | GetUserInfo uses the userinfo endpoint to get additional claims for the token. This is especially useful where upstreams return "thin" ID tokens. This parameter is optional. |
   | insecureSkipVerify   | Used to turn off TLS certificate verification.               |

