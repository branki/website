---
title: "Alerting Policies (Node Level)"
keywords: 'Kuberix, Kubernetes, Node, Alerting, Policy, Notification'
description: 'Learn how to set alerting policies for nodes.'
linkTitle: "Alerting Policies (Node Level)"
weight: 8530
---

Kuberix Enterprise는 노드 및 워크로드에 대한 알림 정책을 제공합니다. 이 자습서에서는 클러스터의 노드에 대한 알림 정책을 만드는 방법을 보여줍니다. [경고 정책(작업 부하 수준)](../../../project-user-guide/alerting/alerting-policy/)을 참조하여 작업 부하에 대한 알림 정책을 구성하는 방법을 알아보세요.

Kuberix Enterprise에는 이러한 정책에 대해 정의된 조건이 충족될 경우 경고를 트리거하는 기본 제공 정책도 있습니다. **Built-in Policies** 탭에서 정책을 클릭하여 세부 정보를 볼 수 있습니다. 콘솔에서 직접 삭제하거나 편집할 수 없습니다.

## 전제 조건

- [Kuberix Enterprise Alerting](../../../pluggable-components/alerting/)을 활성화했습니다.
- 경고 알림을 받기 위해서는 미리 [알림 채널](../../../cluster-administration/platform-settings/notification-management/configure-email/)을 설정해야 합니다.
- 사용자(`cluster-admin`)를 생성하고 `clusters-admin` 역할을 부여해야 합니다. 자세한 내용은 [작업 공간, 프로젝트, 사용자 및 역할 생성](../../../quick-start/create-workspace-and-project/#step-4-create-a-role)을 참조하십시오.
- 클러스터에 워크로드가 있습니다. 준비되지 않은 경우 [Bookinfo 배포 및 액세스](../../../quick-start/deploy-bookinfo-to-k8s/)를 참조하여 샘플 앱을 만듭니다.

## 알림 정책 만들기

1. 콘솔에 `cluster-admin`으로 로그인합니다. 왼쪽 상단 모서리에서 **Platform**을 클릭한 다음 **Cluster Management**를 클릭합니다.

2. **Monitoring & Alerting** 아래의 **Alerting Policies**으로 이동한 다음 **Create**를 클릭합니다.

3. 표시된 대화 상자에서 다음과 같이 기본 정보를 제공합니다. 계속하려면 **Next**을 클릭하세요.

   - **Name**. `node-alert`와 같은 고유 식별자로 간결하고 명확한 이름.
   - **Alias**. 알림 정책을 더 잘 구분할 수 있습니다.
   - **Threshold Duration (min)**. 알림 규칙에 구성된 조건의 기간이 임계값에 도달하면 알림 정책의 상태가 실행 중이 됩니다.
   - **Severity**. 허용되는 값에는 경고의 심각도를 나타내는 **Warning**, **Error** 및 **Critical**이 포함됩니다.
   - **Description**. 알림 정책에 대한 간략한 소개입니다.

4. **Rule Settings** 탭에서 규칙 템플릿을 사용하거나 사용자 지정 규칙을 만들 수 있습니다. 템플릿을 사용하려면 다음 매개변수를 설정하고 **Next**을 클릭하여 계속합니다.

   - **Monitoring Targets**. 모니터링을 위해 클러스터에서 최소한 노드를 선택하십시오.
   - **Alerting Rule**. 알림 정책에 대한 규칙을 정의합니다. 드롭다운 목록에 제공된 규칙은 Prometheus 표현식을 기반으로 하며 조건이 충족되면 경고가 트리거됩니다. CPU 및 메모리와 같은 개체를 모니터링할 수 있습니다.

   {{< notice note >}}

   **Monitoring Metrics** 필드(자동 완성 지원)에 표현식을 입력하여 PromQL로 사용자 정의 규칙을 생성할 수 있습니다. 자세한 내용은 [Prometheus 쿼리](https://prometheus.io/docs/prometheus/latest/querying/basics/)를 참조하세요.

   {{</ notice >}}

5. **Message Settings** 탭에서 경고 메시지의 요약 및 세부 정보를 입력한 다음 **Create**를 클릭합니다.

6. 알림 정책은 방금 생성되었을 때 **Inactive** 상태가 됩니다. 규칙 표현식의 조건이 충족되면 먼저 **Pending**에 도달한 다음 주어진 시간 범위에서 조건이 계속 충족되면 **Firing**으로 바뀝니다.

## 알림 정책 수정

알림 정책이 생성된 후 편집하려면 **알림 정책** 페이지에서 <img src="/images/docs/v3.3/cluster-administration/cluster-wide-alerting-and-notification/alerting"을 클릭합니다. -policies-node-level/edit-policy.png" height="25px" alt="icon"> 경고 정책 오른쪽에 있습니다.

1. 드롭다운 목록에서 **Edit**을 클릭하고 생성과 동일한 단계에 따라 경고 정책을 편집합니다. **Message Settings** 페이지에서 **OK**을 클릭하여 저장합니다.

2. 드롭다운 목록에서 **Delete**를 클릭하여 경고 정책을 삭제합니다.

## 알림 정책 보기

알림 규칙 및 알림 기록을 포함한 세부 정보를 보려면 **Alerting Policies** 페이지에서 알림 정책의 이름을 클릭합니다. 알림 정책을 생성할 때 사용하는 템플릿을 기반으로 하는 규칙 표현식도 볼 수 있습니다.

**Monitoring** 아래의 **Alert Monitoring** 차트는 시간 경과에 따른 실제 사용량 또는 리소스 양을 보여줍니다. **Alerting Messages**는 알림에서 설정한 맞춤 메시지를 표시합니다.

{{< notice note >}}

<img src="/images/docs/v3.3/cluster-administration/cluster-wide-alerting-and-notification/alerting-policies-node-level/drop-down-list.png" width='를 클릭할 수 있습니다. 20' alt="icon" /> 오른쪽 상단 모서리에 있는 경고 모니터링 차트의 시간 범위를 선택하거나 사용자 지정합니다.

<img src="/images/docs/v3.3/zh-cn/cluster-administration/cluster-wide-alerting-and-notification/alerting-policy-node-level/refresh.png" width=를 클릭할 수도 있습니다. '25' alt="icon" /> 오른쪽 상단 모서리에 있는 경고 모니터링 차트를 수동으로 새로 고칩니다.

{{</ notice >}}
