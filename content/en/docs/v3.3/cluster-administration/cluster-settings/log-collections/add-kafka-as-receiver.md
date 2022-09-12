---
title: "Add Kafka as a Receiver"
keywords: 'Kubernetes, log, kafka, pod, container, fluentbit, output'
description: 'Learn how to add Kafka to receive container logs, resource events, or audit logs.'
linkTitle: "Add Kafka as a Receiver"
weight: 8623
---
Elasticsearch, Kafka 및 Fluentd를 Kuberix Enterprise에서 로그 수신기로 사용할 수 있습니다. 이 튜토리얼에서는 다음을 보여줍니다.

- [strimzi-kafka-operator](https://github.com/strimzi/strimzi-kafka-operator)를 배포한 다음 'Kafka' 및 'KafkaTopic' CRD를 생성하여 Kafka 클러스터 및 Kafka 주제를 생성합니다.
- Fluent Bit에서 보낸 로그를 수신하려면 Kafka를 로그 수신기로 추가합니다.
- [Kafkacat](https://github.com/edenhill/kafkacat)을 사용하여 Kafka 클러스터가 로그를 수신하는지 확인합니다.

## 전제 조건

- **클러스터 관리** 권한을 포함한 역할을 부여받은 사용자가 필요합니다. 예를 들어 콘솔에 직접 'admin'으로 로그인하거나 권한이 있는 새 역할을 생성하여 사용자에게 할당할 수 있습니다.
- 로그 수신기를 추가하기 전에 'logging', 'events' 또는 'auditing' 구성 요소를 활성화해야 합니다. 자세한 내용은 [플러그 가능 구성 요소 활성화](../../../../pluggable-components/)를 참조하십시오. 이 튜토리얼에서는 '로깅'을 예로 사용합니다.

## 1단계: Kafka 클러스터 및 Kafka 주제 생성

[strimzi-kafka-operator](https://github.com/strimzi/strimzi-kafka-operator)를 사용하여 Kafka 클러스터 및 Kafka 주제를 생성할 수 있습니다. 이미 Kafka 클러스터가 있는 경우 다음 단계부터 시작할 수 있습니다.

1. `default` 네임스페이스에 [strimzi-kafka-operator](https://github.com/strimzi/strimzi-kafka-operator)를 설치합니다.

    ```bash
    helm repo add strimzi https://strimzi.io/charts/
    ```

    ```bash
    helm install --name kafka-operator -n default strimzi/strimzi-kafka-operator
    ```


2. 다음 명령을 실행하여 'default' 네임스페이스에 Kafka 클러스터와 Kafka 주제를 생성합니다. 이 명령은 데모용으로 'emptyDir'인 스토리지 유형이 '임시'인 Kafka 및 Zookeeper 클러스터를 생성합니다. 프로덕션 환경의 다른 스토리지 유형은 [kafka-persistent](https://github.com/strimzi/strimzi-kafka-operator/blob/0.19.0/examples/kafka/kafka-persistent.yaml)를 참조하세요.

    ```yaml
    cat <<EOF | kubectl apply -f -
    apiVersion: kafka.strimzi.io/v1beta1
    kind: Kafka
    metadata:
      name: my-cluster
      namespace: default
    spec:
      kafka:
        version: 2.5.0
        replicas: 3
        listeners:
          plain: {}
          tls: {}
        config:
          offsets.topic.replication.factor: 3
          transaction.state.log.replication.factor: 3
          transaction.state.log.min.isr: 2
          log.message.format.version: '2.5'
        storage:
          type: ephemeral
      zookeeper:
        replicas: 3
        storage:
          type: ephemeral
      entityOperator:
        topicOperator: {}
        userOperator: {}
    ---
    apiVersion: kafka.strimzi.io/v1beta1
    kind: KafkaTopic
    metadata:
      name: my-topic
      namespace: default
      labels:
        strimzi.io/cluster: my-cluster
    spec:
      partitions: 3
      replicas: 3
      config:
        retention.ms: 7200000
        segment.bytes: 1073741824
    EOF
    ```

3. 다음 명령을 실행하여 Pod 상태를 확인하고 Kafka와 Zookeeper가 모두 실행될 때까지 기다립니다.

    ```bash
    $ kubectl -n default get pod 
    NAME                                         READY   STATUS    RESTARTS   AGE
    my-cluster-entity-operator-f977bf457-s7ns2   3/3     Running   0          69m
    my-cluster-kafka-0                           2/2     Running   0          69m
    my-cluster-kafka-1                           2/2     Running   0          69m
    my-cluster-kafka-2                           2/2     Running   0          69m
    my-cluster-zookeeper-0                       1/1     Running   0          71m
    my-cluster-zookeeper-1                       1/1     Running   1          71m
    my-cluster-zookeeper-2                       1/1     Running   1          71m
    strimzi-cluster-operator-7d6cd6bdf7-9cf6t    1/1     Running   0          104m
    ```

    다음 명령을 실행하여 Kafka 클러스터의 메타데이터를 확인합니다.

    ```bash
    kafkacat -L -b my-cluster-kafka-0.my-cluster-kafka-brokers.default.svc:9092,my-cluster-kafka-1.my-cluster-kafka-brokers.default.svc:9092,my-cluster-kafka-2.my-cluster-kafka-brokers.default.svc:9092
    ```

## 2단계: Kafka를 로그 수신기로 추가

1. Kuberix Enterprise에 'admin'으로 로그인합니다. 왼쪽 상단 모서리에서 **Platform**을 클릭하고 **Cluster Management**를 선택합니다.

    {{< notice note >}}

    [멀티 클러스터 기능](../../../../multicluster-management/)을 활성화한 경우 특정 클러스터를 선택할 수 있습니다.

    {{</ notice >}}

2. **Cluster Management** 페이지에서 **Cluster Settings**의 **Log Receiver**로 이동합니다.

3. **Add Log Receiver**를 클릭하고 **Kafka**를 선택합니다. Kafka 서비스 주소와 포트 번호를 입력한 다음 **OK**을 클릭하여 계속합니다.

   | Service Address                                         | Port Number |
   | ------------------------------------------------------- | ---- |
   | my-cluster-kafka-0.my-cluster-kafka-brokers.default.svc | 9092 |
   | my-cluster-kafka-1.my-cluster-kafka-brokers.default.svc | 9092 |
   | my-cluster-kafka-2.my-cluster-kafka-brokers.default.svc | 9092 |

4. 다음 명령을 실행하여 Kafka 클러스터가 Fluent Bit에서 보낸 로그를 수신하는지 확인합니다.

   ```bash
   # Start a util container
   kubectl run --rm utils -it --generator=run-pod/v1 --image arunvelsriram/utils bash
   # Install Kafkacat in the util container
   apt-get install kafkacat
   # Run the following command to consume log messages from kafka topic: my-topic
   kafkacat -C -b my-cluster-kafka-0.my-cluster-kafka-brokers.default.svc:9092,my-cluster-kafka-1.my-cluster-kafka-brokers.default.svc:9092,my-cluster-kafka-2.my-cluster-kafka-brokers.default.svc:9092 -t my-topic
   ```
   
   
