---
title: "Use an OAuth 2.0 Identity Provider"
keywords: 'Kubernetes, Kuberix, OAuth2, Identity Provider'
description: 'How to use an external OAuth2 identity provider.'
linkTitle: "Use an OAuth 2.0 Identity Provider"
weight: 12230
---
이 문서에서는 OAuth 2.0 프로토콜을 기반으로 외부 ID 공급자를 사용하는 방법을 설명합니다.

다음 그림은 Kuberix Enterprise와 외부 OAuth 2.0 ID 제공자 간의 인증 프로세스를 보여줍니다.

![oauth2](/images/docs/v3.3/access-control-and-account-management/external-authentication/use-an-oauth2-identity-provider/oauth2.svg)

## 전제 조건

쿠버네티스 클러스터를 배포하고 클러스터에 Kuberix Enterprise를 설치해야 합니다. 자세한 내용은 [Linux에 설치](/docs/v3.3/installing-on-linux/) 및 [쿠버네티스에 설치](/docs/v3.3/installing-on-kubernetes/)를 참조하십시오.

## OAuth 2.0 플러그인 개발

{{< notice note >}}

Kuberix Enterprise provides two built-in OAuth 2.0 plugins: [GitHubIdentityProvider](https://github.com/KuberixEnterprise/blob/release-3.1/pkg/apiserver/authentication/identityprovider/github/github.go) for GitHub. 내장된 플러그인에 따라 다른 플러그인을 개발할 수 있습니다.

{{</ notice >}}

1. 로컬 머신에서 [Kuberix Enterprise 저장소](https://github.com/KuberixEnterprise)를 복제하고 로컬 Kuberix Enterprise 저장소로 이동한 다음 `/pkg/apiserver/authentication/ identityprovider/` 디렉토리.

2. 플러그인 패키지에서 다음 인터페이스를 구현합니다.

   ```go
   // /pkg/apiserver/authentication/identityprovider/oauth_provider.go
   type OAuthProvider interface {
   	// Exchange identity with a remote server.
   	IdentityExchange(code string) (Identity, error)
   }
   
   type OAuthProviderFactory interface {
   	// Return the identity provider type.
   	Type() string
   	// Apply settings from ke-config.
   	Create(options oauth.DynamicOptions) (OAuthProvider, error)
   }
   ```

   ```go
   // /pkg/apiserver/authentication/identityprovider/identity_provider.go
   type Identity interface {
     // (Mandatory) Return the identifier of the user at the identity provider.
   	GetUserID() string
     // (Optional) Return the name of the user to be referred as on Kuberix Enterprise.
   	GetUsername() string
     // (Optional) Return the email address of the user.
   	GetEmail() string
   }
   ```

3. 플러그인 패키지의 'init()' 함수에 플러그인을 등록합니다.

   ```go
   // Custom plugin package
   func init() {
     // Change <StructName> to the actual name of the struct that
     // implements the OAuthProviderFactory interface.
   	identityprovider.RegisterOAuthProvider(&<StructName>{})
   }
   ```

4. `/pkg/apiserver/authentication/options/authenticate_options.go`에서 플러그인 패키지를 가져옵니다.

   ```go
   // Change <CustomPackage> to the actual name of your plugin package.
   import (
   	...
   	_ "kuberix.io/kuberixEnterprise/pkg/apiserver/authentication/identityprovider/<CustomPackage>"
   	...
   	)
   ```

5. [ke-apiserver 이미지 빌드](https://github.com/kuberixEnterprise/community/blob/104bab42f67094930f2ca87c603b7c6365cd092a/developer-guide/development/quickstart.md) 및 클러스터에 배포합니다.

## Kuberix Enterprise와 ID 공급자 통합

1. Kuberix Enterprise에 `admin`으로 로그인하고 커서를 <img src="/images/docs/v3.3/access-control-and-account-management/external-authentication/set-up-external-authentication"으로 이동합니다. /toolbox.png" width="20px" height="20px" alt="icon"> 오른쪽 하단의 **kubectl**을 클릭하고 다음 명령을 실행하여 CRD의 `ke-installer`를 편집합니다. '클러스터 구성':

   ```bash
   kubectl -n ke-system edit cc ke-installer
   ```

2. `spec:authentication` 섹션의 `oauthOptions:identityProviders`가 아닌 다른 항목. 자세한 내용은 [외부 인증 설정](../set-up-external-authentication/)을 참조하십시오.

3. 개발한 ID 제공자 플러그인에 따라 'oauthOptions:identityProviders' 섹션의 필드를 구성합니다.

    다음은 GitHub를 외부 ID 공급자로 사용하는 구성 예입니다. 자세한 내용은 [공식 GitHub 문서](https://docs.github.com/en/developers/apps/building-oauth-apps) 및 [GitHubIdentityProvider 소스 코드](https://github.com /kuberixEnterprise/blob/release-3.1/pkg/apiserver/authentication/identityprovider/github/github.go) 플러그인을 참고하십시오.

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
         - name: github
           type: GitHubIdentityProvider
           mappingMethod: auto
           provider:
             clientID: '******'
             clgure fields ientSecret: '******'
             redirectURL: 'https://ke-console/oauth/redirect/github'
   ```
  

4. 필드가 구성되면 변경 사항을 저장하고 ke-installer 재시작이 완료될 때까지 기다립니다.

   {{< notice note >}}
   
   Kuberix Enterprise 웹 콘솔은 ke-installer를 다시 시작하는 동안 사용할 수 없습니다. 재시작이 완료될 때까지 기다리십시오.

   {{</ notice >}}

5. Kuberix Enterprise 로그인 페이지로 이동하여 **Log In with XXX**(예: **Log In with GitHub**)를 클릭합니다.

6. 외부 ID 제공자의 로그인 페이지에서 Kuberix Enterprise에 로그인하기 위해 ID 제공자에 구성된 사용자의 사용자 이름과 비밀번호를 입력하십시오.

   ![github-login-page](/images/docs/v3.3/access-control-and-account-management/external-authentication/use-an-oauth2-identity-provider/github-login-page.png)

