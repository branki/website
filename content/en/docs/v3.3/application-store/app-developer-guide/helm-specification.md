---
title: "Helm Specifications"
keywords: 'Kubernetes, Kuberix, Helm, specifications'
description: 'Understand the chart structure and specifications.'
linkTitle: "Helm Specifications"
weight: 14420
---

Helm 차트는 패키징 형식으로 사용됩니다. 차트는 관련 쿠버네티스 리소스 집합을 설명하는 파일 모음입니다. 자세한 내용은 [Helm 문서](https://helm.sh/docs/topics/charts/)를 참조하세요.

## 구조

차트의 모든 관련 파일은 일반적으로 다음을 포함하는 디렉토리에 저장됩니다.

```text
chartname/
  Chart.yaml          # A YAML file containing basic information about the chart, such as version and name.
  LICENSE             # (Optional) A plain text file containing the license for the chart.
  README.md           # (Optional) The description of the app and how-to guide.
  values.yaml         # The default configuration values for this chart.
  values.schema.json  # (Optional) A JSON Schema for imposing a structure on the values.yaml file.
  charts/             # A directory containing any charts upon which this chart depends.
  crds/               # Custom Resource Definitions.
  templates/          # A directory of templates that will generate valid Kubernetes configuration files with corresponding values provided.
  templates/NOTES.txt # (Optional) A plain text file with usage notes.
```

## Chart.yaml 파일

차트에 대한 `chart.yaml` 파일을 제공해야 합니다. 다음은 각 필드에 대한 설명이 있는 파일의 예입니다.

```yaml
apiVersion: (Required) The chart API version. 
name: (Required) The name of the chart.
version: (Required) The version, following the SemVer 2 standard. 
kubeVersion: (Optional) The compatible Kubernetes version, following the SemVer 2 standard.
description: (Optional) A single-sentence description of the app.
type: (Optional) The type of the chart.
keywords:
  - (Optional) A list of keywords about the app.
home: (Optional) The URL of the app.
sources:
  - (Optional) A list of URLs to source code for this app.
dependencies: (Optional) A list of the chart requirements.
  - name: The name of the chart, such as nginx.
    version: The version of the chart, such as "1.2.3".
    repository: The repository URL ("https://example.com/charts") or alias ("@repo-name").
    condition: (Optional) A yaml path that resolves to a boolean, used for enabling/disabling charts (for example, subchart1.enabled ).
    tags: (Optional)
      - Tags can be used to group charts for enabling/disabling together.
    import-values: (Optional)
      - ImportValues holds the mapping of source values to parent key to be imported. Each item can be a string or pair of child/parent sublist items.
    alias: (Optional) Alias to be used for the chart. It is useful when you have to add the same chart multiple times.
maintainers: (Optional)
  - name: (Required) The maintainer name.
    email: (Optional) The maintainer email.
    url: (Optional) A URL for the maintainer.
icon: (Optional) A URL to an SVG or PNG image to be used as an icon.
appVersion: (Optional) The app version. This needn't be SemVer.
deprecated: (Optional, boolean) Whether this chart is deprecated.
annotations:
  example: (Optional) A list of annotations keyed by name.
```

{{< notice note >}}

- 'dependencies' 필드는 'v1' 차트에 대해 별도의 파일 'requirements.yaml'에 있는 차트 종속성을 정의하는 데 사용됩니다. 자세한 내용은 [차트 종속성](https://helm.sh/docs/topics/charts/#chart-dependencies)을 참조하세요.
- 'type' 필드는 차트의 유형을 정의하는 데 사용됩니다. 허용되는 값은 'application' 및 'library'입니다. 자세한 내용은 [차트 유형](https://helm.sh/docs/topics/charts/#chart-types)을 참조하세요.

{{</ notice >}} 

## Values.yaml 및 템플릿

[Go 템플릿 언어](https://golang.org/pkg/text/template/)로 작성된 Helm 차트 템플릿은 차트의 'templates' 폴더에 저장됩니다. 템플릿에 값을 제공하는 방법에는 두 가지가 있습니다.

1. 참조할 수 있는 기본값을 사용하여 차트 내부에 `values.yaml` 파일을 만듭니다.
2. 필요한 값이 포함된 YAML 파일을 만들고 `helm install`로 명령줄을 통해 파일을 사용합니다.

다음은 `templates` 폴더에 있는 템플릿의 예입니다.

```yaml
apiVersion: v1
kind: ReplicationController
metadata:
  name: deis-database
  namespace: deis
  labels:
    app.kubernetes.io/managed-by: deis
spec:
  replicas: 1
  selector:
    app.kubernetes.io/name: deis-database
  template:
    metadata:
      labels:
        app.kubernetes.io/name: deis-database
    spec:
      serviceAccount: deis-database
      containers:
        - name: deis-database
          image: {{.Values.imageRegistry}}/postgres:{{.Values.dockerTag}}
          imagePullPolicy: {{.Values.pullPolicy}}
          ports:
            - containerPort: 5432
          env:
            - name: DATABASE_STORAGE
              value: {{default "minio" .Values.storage}}
```

위의 예는 쿠버네티스에서 ReplicationController 템플릿을 정의합니다. `values.yaml`에 정의된 일부 값이 참조됩니다.

- `imageRegistry`: The Docker image registry.
- `dockerTag`: The Docker image tag.
- `pullPolicy`: The image pulling policy.
- `storage`: The storage backend. It defaults to `minio`.

An example `values.yaml` file:

```text
imageRegistry: "quay.io/deis"
dockerTag: "latest"
pullPolicy: "Always"
storage: "s3"
```

## 참조

[Helm 문서](https://helm.sh/docs/)

[Chart 문서](https://helm.sh/docs/topics/charts/)
