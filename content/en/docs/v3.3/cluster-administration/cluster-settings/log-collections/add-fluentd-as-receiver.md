---
title: "Add Fluentd as a Receiver"
keywords: 'Kubernetes, log, fluentd, pod, container, fluentbit, output'
description: 'Learn how to add Fluentd to receive logs, events or audit logs.'
linkTitle: "Add Fluentd as a Receiver"
weight: 8624
---
Elasticsearch, Kafka 및 Fluentd를 Kuberix Enterprise에서 로그 수신기로 사용할 수 있습니다. 이 튜토리얼에서는 다음을 보여줍니다.

- Fluentd를 배포로 배포하고 해당 서비스 및 ConfigMap을 만드는 방법.
- Fluent Bit에서 보낸 로그를 받아 stdout으로 출력하기 위해 Fluentd를 로그 수신기로 추가하는 방법.
- Fluentd가 성공적으로 로그를 수신하는지 확인하는 방법.

## 전제 조건

- **Cluster Management** 권한을 포함한 역할을 부여받은 사용자가 필요합니다. 예를 들어 콘솔에 직접 'admin'으로 로그인하거나 권한이 있는 새 역할을 생성하여 사용자에게 할당할 수 있습니다.

- 로그 수신기를 추가하기 전에 'logging', 'events' 또는 'auditing' 구성 요소를 활성화해야 합니다. 자세한 내용은 [플러그 가능 구성 요소 활성화](../../../../pluggable-components/)를 참조하십시오. 이 튜토리얼에서는 '로깅'을 예로 사용합니다.

## 1단계: Fluentd를 배포로 배포

일반적으로 Fluentd는 쿠버네티스에서 DaemonSet으로 배포되어 각 노드의 컨테이너 로그를 수집합니다. Kuberix Enterprise는 메모리 사용량이 적기 때문에 Fluent Bit를 선택합니다. 게다가 Fluentd에는 수많은 출력 플러그인이 있습니다. 따라서 Kuberix Enterprise는 Fluent Bit에서 수신한 로그를 S3, MongoDB, Cassandra, MySQL, syslog 및 Splunk와 같은 더 많은 대상으로 전달하기 위해 Fluentd를 배포로 배포하기로 선택합니다.

다음 명령을 실행합니다.

{{< notice note >}}

- 다음 명령은 'default' 네임스페이스에 Fluentd 배포, 서비스 및 ConfigMap을 만들고 Fluentd ConfigMap에 필터를 추가하여 Fluent Bit 및 Fluentd 루프 로그 수집을 방지하기 위해 'default' 네임스페이스에서 로그를 제외합니다.
- Fluentd를 다른 네임스페이스에 배포하려면 네임스페이스를 변경합니다.

{{</ notice >}}

```yaml
cat <<EOF | kubectl apply -f -
apiVersion: v1
kind: ConfigMap
metadata:
  name: fluentd-config
  namespace: default
data:
  fluent.conf: |-
    # Receive logs sent from Fluent Bit on port 24224
    <source>
      @type forward
      port 24224
    </source>

    # Because this will send logs Fluentd received to stdout,
    # to avoid Fluent Bit and Fluentd loop logs collection,
    # add a filter here to avoid sending logs from the default namespace to stdout again
    <filter **>
      @type grep
      <exclude>
        key $.kubernetes.namespace_name
        pattern /^default$/
      </exclude>
    </filter>

    # Send received logs to stdout for demo/test purpose only
    # Various output plugins are supported to output logs to S3, MongoDB, Cassandra, MySQL, syslog, Splunk, etc.
    <match **>
      @type stdout
    </match>
---
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: fluentd
  name: fluentd
  namespace: default
spec:
  replicas: 1
  selector:
    matchLabels:
      app: fluentd
  template:
    metadata:
      labels:
        app: fluentd
    spec:
      containers:
      - image: fluentd:v1.9.1-1.0
        imagePullPolicy: IfNotPresent
        name: fluentd
        ports:
        - containerPort: 24224
          name: forward
          protocol: TCP
        - containerPort: 5140
          name: syslog
          protocol: TCP
        volumeMounts:
        - mountPath: /fluentd/etc
          name: config
          readOnly: true
      volumes:
      - configMap:
          defaultMode: 420
          name: fluentd-config
        name: config
---
apiVersion: v1
kind: Service
metadata:
  labels:
    app: fluentd
  name: fluentd
  namespace: default
spec:
  ports:
  - name: forward
    port: 24224
    protocol: TCP
    targetPort: forward
  selector:
    app: fluentd
  sessionAffinity: None
  type: ClusterIP
EOF
```

## 2단계: Fluentd를 로그 수신기로 추가

1. Kuberix Enterprise에 'admin'으로 로그인합니다. 왼쪽 상단 모서리에서 **Platform**을 클릭하고 **Cluster Management**를 선택합니다.

   {{< notice note >}}

   [멀티 클러스터 기능](../../../../multicluster-management/)을 활성화한 경우 특정 클러스터를 선택할 수 있습니다.

   {{</ notice >}}

2. **Cluster Management** 페이지에서 **Cluster Settings**의 **Log Receivers***로 이동합니다.

3. **Add Log Receiver**를 클릭하고 **Fluentd**를 선택합니다.

4. Fluentd 서비스 주소와 포트 번호를 제공합니다.

5. Fluentd는 **Log Receivers** 페이지의 수신자 목록에 표시되며 상태는 **Collecting**입니다.


## 3단계: Fluentd가 Fluent Bit에서 보낸 로그를 수신하고 있는지 확인

1. **Cluster Management** 페이지에서 **Application Workloads**를 클릭합니다.

2. **Workloads**를 선택한 다음 **Deployments** 탭에서 `default` 프로젝트를 선택합니다.

3. **fluentd** 항목을 클릭한 다음 **fluentd-xxxxxxxxx-xxxxx** 포드를 선택합니다.

4. **fluentd** 컨테이너를 클릭합니다.

5. **fluentd** 컨테이너 페이지에서 **Container Logs** 탭을 선택합니다.

6. 로그가 계속 위로 스크롤되기 시작하는 것을 볼 수 있습니다.

