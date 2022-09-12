---
title: "Node Management"
keywords: "Kubernetes, Kuberix, taints, nodes, labels, requests, limits"
description: "Monitor node status and learn how to add node labels or taints."

linkTitle: "Node Management"
weight: 8100
---

쿠버네티스는 노드에서 실행할 컨테이너를 Pod에 배치하여 워크로드를 실행합니다. 노드는 클러스터에 따라 가상 또는 물리적 시스템일 수 있습니다. 각 노드에는 컨트롤 플레인에서 관리하는 Pod를 실행하는 데 필요한 서비스가 포함되어 있습니다. 노드에 대한 자세한 내용은 [쿠버네티스 공식 문서](https://kubernetes.io/docs/concepts/architecture/nodes/)를 참조하십시오.

이 자습서는 클러스터 관리자가 클러스터 내의 노드를 보고 수행할 수 있는 작업을 보여줍니다.

## 전제 조건

**클러스터 관리** 권한을 포함하여 역할이 부여된 사용자가 필요합니다. 예를 들어 콘솔에 'admin'으로 직접 로그인하거나 권한이 있는 새 역할을 생성하여 사용자에게 할당할 수 있습니다.

## 노드 상태

클러스터 노드는 클러스터 관리자만 액세스할 수 있습니다. 일부 노드 메트릭은 클러스터에 매우 중요합니다. 따라서 이러한 숫자를 감시하고 노드를 사용할 수 있는지 확인하는 것은 관리자의 책임입니다. 노드 상태를 보려면 아래 단계를 따르십시오.

1. 왼쪽 상단 모서리에서 **Platform**을 클릭하고 **Cluster Management**를 선택합니다.

2. 가져온 멤버 클러스터와 함께 [멀티 클러스터 기능](../../multicluster-management/)을 활성화한 경우 특정 클러스터를 선택하여 해당 노드를 볼 수 있습니다. 기능을 활성화하지 않은 경우 다음 단계를 직접 참조하십시오.

3. **Nodes** 아래에서 **Cluster Nodes**를 선택하면 노드 상태에 대한 자세한 정보를 볼 수 있습니다.

    - **Name**: 노드 이름 및 서브넷 IP 주소입니다.
    - **Status**: 노드의 현재 상태로, 노드가 사용 가능한지 여부를 나타냅니다.
    - **Role**: 노드가 작업자인지 제어 평면인지를 나타내는 노드의 역할입니다.
    - **CPU Usage**: 노드의 실시간 CPU 사용량입니다.
    - **Memory Usage**: 노드의 실시간 메모리 사용량입니다.
    - **Pods**: 노드에서 Pod의 실시간 사용량입니다.
    - **Allocated CPU**: 이 메트릭은 노드에 있는 Pod의 총 CPU 요청을 기반으로 계산됩니다. 워크로드가 더 적은 CPU 리소스를 사용하는 경우에도 이 노드의 워크로드용으로 예약된 CPU 양을 나타냅니다. 이 수치는 대부분의 경우 Pod를 예약할 때 할당된 CPU 리소스가 낮은 노드를 선호하는 쿠버네티스 스케줄러(kube-scheduler)에 중요합니다. 자세한 내용은 [컨테이너 리소스 관리](https://kubernetes.io/docs/concepts/configuration/manage-resources-containers/)를 참조하십시오.
    - **Allocated Memory**: 이 메트릭은 노드에 있는 Pod의 총 메모리 요청을 기반으로 계산됩니다. 워크로드가 더 적은 메모리 리소스를 사용하는 경우에도 이 노드의 워크로드를 위해 예약된 메모리 양을 나타냅니다.

    {{< notice note >}}
    
**CPU**와 **Allocated CPU**는 대부분 다른데, **Memory**와 **Allocated Memory**도 다르며 정상입니다. 클러스터 관리자는 하나가 아닌 두 메트릭에 모두 집중해야 합니다. 실제 사용량과 일치하도록 각 노드에 대한 리소스 요청 및 제한을 설정하는 것은 항상 좋은 방법입니다. 리소스를 과도하게 할당하면 클러스터 사용률이 낮아질 수 있고 과소 할당하면 클러스터에 높은 부담이 가해져 클러스터가 비정상 상태가 될 수 있습니다.

    {{</ notice >}}

## 노드 관리

**Cluster Nodes** 페이지에서 다음 작업을 수행할 수 있습니다.

- **Cordon/Uncordon**: 오른쪽의 <img src="/images/docs/v3.3/common-icons/three-dots.png" width="15" alt="icon" /> 클릭 클러스터 노드를 클릭한 다음 **Cordon** 또는 **Uncordon**을 클릭합니다. 노드를 예약 불가능으로 표시하는 것은 노드 재부팅 또는 기타 유지 관리 중에 매우 유용합니다. Kubernetes 스케줄러는 예약 불가능으로 표시된 경우 이 노드에 새 Pod를 예약하지 않습니다. 또한 노드에 이미 있는 기존 워크로드에는 영향을 미치지 않습니다.

- **Open Terminal**：오른쪽의 <img src="/images/docs/v3.3/common-icons/three-dots.png" width="15" alt="icon" /> 클릭 클러스터 노드를 클릭한 다음 **Open Terminal**를 클릭합니다. 이렇게 하면 노드 구성 수정 및 이미지 다운로드와 같은 노드를 편리하게 관리할 수 있습니다.

- **Edit Taints**: Taints를 사용하면 노드가 포드 세트를 격퇴할 수 있습니다. taint를 편집하려면 대상 노드 앞의 확인란을 선택합니다. 표시된 **Edit Taints**에서 taint를 추가, 삭제 또는 수정할 수 있습니다.

노드 세부 정보를 보려면 노드를 클릭합니다. 세부 정보 페이지에서 다음 작업을 수행할 수 있습니다.

- **Edit Labels**: 노드 레이블은 특정 노드에 포드를 할당하려는 경우 매우 유용할 수 있습니다. 먼저 노드에 레이블을 지정(예: GPU 노드에 `node-role.kubernetes.io/gpu-node` 레이블 지정)한 다음 **Advanced Settings** [워크로드 생성 시](../ ../project-user-guide/application-workloads/deployments/#step-5-configure-advanced-settings) Pod가 GPU 노드에서 명시적으로 실행되도록 허용할 수 있습니다. 노드 레이블을 추가하려면 **More** > **Edit Labels**을 선택합니다.

- 노드, 포드, 메타데이터, 모니터링 데이터 및 이벤트의 실행 상태를 봅니다.

    {{< notice note >}}
    
taint를 추가할 때 예기치 않은 동작이 발생하여 서비스를 사용할 수 없게 될 수 있으므로 주의하십시오. 자세한 내용은 [Taints and Tolerations](https://kubernetes.io/docs/concepts/scheduling-eviction/taint-and-toleration/)를 참조하십시오.

    {{</ notice >}}

## 노드 추가 및 제거

현재 Kuberix Enterprise 콘솔에서 직접 노드를 추가하거나 제거할 수 없지만 KubePOP을 사용하여 노드를 추가하거나 제거할 수 있습니다. 자세한 내용은 [새 노드 추가](../../installing-on-linux/cluster-operation/add-new-nodes/) 및 [노드 제거](../../installing-on- linux/cluster-operation/remove-nodes/).
