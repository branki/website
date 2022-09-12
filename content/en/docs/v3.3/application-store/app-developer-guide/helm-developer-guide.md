---
title: "Helm Developer Guide"
keywords: 'Kubernetes, Kuberix, helm, development'
description: 'Develop your own Helm-based app.'
linkTitle: "Helm Developer Guide"
weight: 14410
---

필요한 권한이 있는 테넌트가 배포할 수 있도록 앱의 Helm 차트를 Kuberix Enterprise에 업로드할 수 있습니다. 이 튜토리얼은 NGINX를 예로 사용하여 Helm 차트를 준비하는 방법을 보여줍니다.

## Helm 설치

Kuberix Enterprise를 이미 설치했다면 Helm이 환경에 배포됩니다. 아니면 [Helm 문서](https://helm.sh/docs/intro/install/)를 참고하여 먼저 Helm을 설치하세요.

## 로컬 저장소 생성

다음 명령을 실행하여 컴퓨터에 리포지토리를 만듭니다.

```bash
mkdir helm-repo
```

```bash
cd helm-repo
```

## 앱 만들기

`helm create`를 사용하여 `nginx`라는 폴더를 만들면 앱에 대한 YAML 템플릿과 디렉터리가 자동으로 생성됩니다. 일반적으로 최상위 디렉토리의 파일 및 디렉토리 이름은 변경하지 않는 것이 좋습니다.

```bash
$ helm create nginx
$ tree nginx/
nginx/
├── charts
├── Chart.yaml
├── templates
│   ├── deployment.yaml
│   ├── _helpers.tpl
│   ├── ingress.yaml
│   ├── NOTES.txt
│   └── service.yaml
└── values.yaml
```

'Chart.yaml'은 이름, API, 앱 버전 등 차트의 기본 정보를 정의하는 데 사용됩니다. 자세한 내용은 [Chart.yaml 파일](../helm-specification/#chartyaml-file)을 참조하십시오.

`Chart.yaml` 파일의 예:

```yaml
apiVersion: v1
appVersion: "1.0"
description: A Helm chart for Kubernetes
name: nginx
version: 0.1.0
```

Helm 기반 앱을 쿠버네티스에 배포할 때 Kuberix Enterprise 콘솔에서 'values.yaml' 파일을 직접 편집할 수 있습니다.

`values.yaml` 파일의 예:

```yaml
# Default values for test.
# This is a YAML-formatted file.
# Declare variables to be passed into your templates.

replicaCount: 1

image:
  repository: nginx
  tag: stable
  pullPolicy: IfNotPresent

nameOverride: ""
fullnameOverride: ""

service:
  type: ClusterIP
  port: 80

ingress:
  enabled: false
  annotations: {}
    # kubernetes.io/ingress.class: nginx
    # kubernetes.io/tls-acme: "true"
  path: /
  hosts:
    - chart-example.local
  tls: []
  #  - secretName: chart-example-tls
  #    hosts:
  #      - chart-example.local

resources: {}
  # We usually recommend not to specify default resources and to leave this as a conscious
  # choice for the user. This also increases chances charts run on environments with little
  # resources, such as Minikube. If you do want to specify resources, uncomment the following
  # lines, adjust them as necessary, and remove the curly braces after 'resources:'.
  # limits:
  #  cpu: 100m
  #  memory: 128Mi
  # requests:
  #  cpu: 100m
  #  memory: 128Mi

nodeSelector: {}

tolerations: []

affinity: {}
```

[Helm 사양](../helm-specification/)을 참조하여 `nginx' 폴더에 있는 파일을 편집하고 편집이 끝나면 저장합니다.

## 인덱스 파일 생성(선택 사항)

Kuberix Enterprise에서 HTTP 또는 HTTPS URL이 있는 저장소를 추가하려면 'index.yaml' 파일을 미리 오브젝트 스토리지에 업로드해야 합니다. Helm을 사용하여 이전 디렉토리 `nginx`에서 다음 명령어를 실행하여 인덱스 파일을 생성합니다.

```bash
helm repo index .
```

```bash
$ ls
index.yaml  nginx
```

{{< notice note >}}

- 저장소 URL이 S3 스타일인 경우 저장소에 앱을 추가할 때 개체 저장소에 인덱스 파일이 자동으로 생성됩니다.

- Kuberix Enterprise에 리포지토리를 추가하는 방법에 대한 자세한 내용은 [Helm 리포지토리 가져오기](../../../workspace-administration/app-repository/import-helm-repository/)를 참조하세요.

{{</ notice >}}

## 차트 패키지

`nginx`의 이전 디렉토리로 이동하여 다음 명령을 실행하여 .tgz 패키지를 생성하는 차트를 패키징합니다.

```bash
helm package nginx
```

```bash
$ ls
nginx  nginx-0.1.0.tgz
```

## 앱 업로드

이제 Helm 기반 앱이 준비되었으므로 Kuberix Enterprise에 로드하고 플랫폼에서 테스트할 수 있습니다.

## 또한보십시오

[헬름 사양](../helm-specification/)

[Helm 저장소 가져오기](../../../workspace-administration/app-repository/import-helm-repository/)
