---
title: "Introduction to Log Receivers"
keywords: 'Kubernetes, log, elasticsearch, kafka, fluentd, pod, container, fluentbit, output'
description: 'Learn the basics of cluster log receivers, including tools, and general steps.'
linkTitle: "Introduction"
weight: 8621
---

Kuberix Enterprise는 유연한 로그 수신기 구성 방법을 제공합니다. [FluentBit Operator](https://github.com/kubesphere/fluentbit-operator/)를 통해 사용자는 Elasticsearch, Kafka 및 Fluentd 수신기를 쉽게 추가, 수정, 삭제, 활성화 또는 비활성화할 수 있습니다. 수신자가 추가되면 이 수신자에게 로그가 전송됩니다.

이 튜토리얼은 Kuberix Enterprise에서 로그 수신기를 추가하는 일반적인 단계에 대한 간략한 소개를 제공합니다.

## 전제 조건

- **Cluster Management** 권한을 포함한 역할을 부여받은 사용자가 필요합니다. 예를 들어 콘솔에 직접 'admin'으로 로그인하거나 권한이 있는 새 역할을 생성하여 사용자에게 할당할 수 있습니다.

- 로그 수신기를 추가하기 전에 'Logging', 'Events' 또는 'Auditing' 구성 요소를 활성화해야 합니다. 자세한 내용은 [플러그 가능 구성 요소 활성화](../../../../pluggable-components/)를 참조하십시오.

## 컨테이너 로그에 대한 로그 수신기 추가

로그 수신기를 추가하려면:

1. Kuberix Enterprise의 웹 콘솔에 `admin`으로 로그인합니다.

2. 왼쪽 상단 모서리에서 **Platform**을 클릭하고 **Cluster Management**를 선택합니다.

   {{< notice note >}}

   [멀티 클러스터 기능](../../../../multicluster-management/)을 활성화한 경우 특정 클러스터를 선택할 수 있습니다.

   {{</ notice >}}

3. 사이드바의 **Cluster Settings** 아래에 있는 **Log Receiver**로 이동합니다.

4. 로그 수신자 목록 페이지에서 **Add Log Receiver**를 클릭합니다.

   {{< notice note >}}

- 각 수신기 유형에 대해 최대 1개의 수신기를 추가할 수 있습니다.
- 다른 유형의 수신기를 동시에 추가할 수 있습니다.

   {{</ notice >}}

### Elasticsearch를 로그 수신기로 추가

[ClusterConfiguration](https://github.com/kubesphere/kubekey/blob/에서 'logging', 'events' 또는 'auditing'이 활성화된 경우 서비스 주소가 Elasticsearch 클러스터로 설정된 기본 Elasticsearch 수신기가 추가됩니다. 릴리스-2.2/docs/config-example.md).

[ClusterConfiguration](https://github.com/kubesphere/kubekey/blob/release-2.2/docs/config-example)에 'externalElasticsearchHost'와 'externalElasticsearchPort'가 모두 지정되지 않은 경우 내부 Elasticsearch 클러스터가 쿠버네티스 클러스터에 배포됩니다. .md) 'logging', 'events' 또는 'auditing'이 활성화된 경우. 내부 Elasticsearch 클러스터는 테스트 및 개발 전용입니다. 프로덕션용으로 외부 Elasticsearch 클러스터를 구성하는 것이 좋습니다.

로그 검색은 구성된 내부 또는 외부 Elasticsearch 클러스터에 의존합니다.

기본 Elasticsearch 로그 수신기가 삭제된 경우 [Elasticsearch를 수신기로 추가](../add-es-as-receiver/)를 참조하여 새로 추가하세요.

### Kafka를 로그 수신기로 추가

Kafka는 종종 로그를 수신하는 데 사용되며 Spark와 같은 다른 처리 시스템에 대한 브로커 역할을 합니다. [카프카를 수신자로 추가](../add-kafka-as-receiver/)는 쿠버네티스 로그를 수신하기 위해 Kafka를 추가하는 방법을 보여줍니다.

### Fluentd를 로그 수신기로 추가

Elasticsearch나 Kafka가 아닌 다른 곳으로 로그를 출력해야 하는 경우 Fluentd를 로그 수신기로 추가할 수 있습니다. Fluentd에는 S3, MongoDB, Cassandra, MySQL, syslog 및 Splunk와 같은 다양한 대상으로 로그를 전달할 수 있는 수많은 출력 플러그인이 있습니다. [Fluentd를 수신기로 추가](../add-fluentd-as-receiver/)는 쿠버네티스 로그를 수신하기 위해 Fluentd를 추가하는 방법을 보여줍니다.

## 리소스 이벤트 또는 감사 로그에 대한 로그 수신기 추가

Kuberix Enterprise는 리소스 이벤트 및 감사 로그를 컨테이너 로그와 동일한 방식으로 보관할 수 있습니다. **Log Receivers** 페이지의 **Resource Events** 또는 **Audit Logs** 탭은 [ClusterConfiguration](https://github.com/kubePOP/blob/release-2.2/docs/config-example.md). 해당 탭으로 이동하여 리소스 이벤트 또는 감사 로그에 대한 로그 수신기를 구성할 수 있습니다.

컨테이너 로그, 리소스 이벤트, 감사 로그는 Kuberix Enterprise에서 검색할 다른 Elasticsearch 인덱스에 저장되어야 합니다. 인덱스는 <인덱스 접두사>-<년-월-일> 형식으로 자동 생성됩니다.

## 로그 수신기 켜기 또는 끄기

로그 수신기를 추가하거나 삭제하지 않고 켜거나 끌 수 있습니다. 로그 수신기를 켜거나 끄려면:

1. **Log Receivers** 페이지에서 로그 수신자를 클릭하고 수신자의 세부 정보 페이지로 이동합니다.
2. **More**를 클릭하고 **Change Status**을 선택합니다.

3. **Collecting** 또는 **Disabled**를 선택하여 로그 수신기를 켜거나 끕니다.

4. 끄면 로그 수신자의 상태가 **Disabled**로 변경되고, 그렇지 않으면 **Log Receivers** 페이지에서 상태가 **Collecting**으로 변경됩니다.


## 로그 수신자 편집 또는 삭제

로그 수신기를 편집하거나 삭제할 수 있습니다.

1. **Log Receivers** 페이지에서 로그 수신자를 클릭하고 수신자의 세부 정보 페이지로 이동합니다.
2. 드롭다운 목록에서 **Edit** 또는 **Edit YAML**을 클릭하여 로그 수신기를 편집합니다.

3. **Delete**를 클릭하여 로그 수신자를 삭제합니다.
