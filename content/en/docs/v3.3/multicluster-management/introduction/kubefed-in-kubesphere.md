---
title: "Kuberix Enterprise Federation"
keywords: "Kubernetes, Kuberix, federation, multicluster, hybrid-cloud"
description: "Understand the fundamental concept of Kubernetes federation in Kuberix Enterprise, including member clusters and host clusters."
linkTitle: "Kuberix Enterprise Federation"
weight: 5120
---

다중 클러스터 기능은 다중 클러스터 간의 네트워크 연결과 관련됩니다. 따라서 클러스터의 토폴로지 관계를 이해하는 것이 중요합니다.

## 멀티 클러스터 아키텍처 작동 방식

Kuberix Enterprise의 중앙 제어 평면을 사용하여 여러 클러스터를 관리하기 전에 **호스트** 클러스터라고도 하는 호스트 클러스터를 생성해야 합니다. 호스트 클러스터는 기본적으로 멀티 클러스터 기능이 활성화된 Kuberix Enterprise 클러스터입니다. **구성원** 클러스터라고도 하는 구성원 클러스터의 통합 관리를 위한 제어 평면을 제공합니다. 구성원 클러스터는 중앙 제어 평면이 없는 일반적인 Kuberix Enterprise 클러스터입니다. 즉, 필요한 권한이 있는 테넌트(일반적으로 클러스터 관리자)는 호스트 클러스터에서 컨트롤 플레인에 액세스하여 구성원 클러스터의 리소스 보기 및 편집과 같은 모든 구성원 클러스터를 관리할 수 있습니다. 반대로 멤버 클러스터의 웹 콘솔에 별도로 액세스하면 다른 클러스터의 리소스를 볼 수 없습니다.

여러 구성원 클러스터가 동시에 존재할 수 있지만 호스트 클러스터는 하나만 있을 수 있습니다. 다중 클러스터 아키텍처에서 호스트 클러스터와 구성원 클러스터 간의 네트워크는 [Direct-Connection](../../enable-multicluster/direct-connection/) 또는 [Agent-Connection](../.. /enable-multicluster/agent-connection/). 구성원 클러스터 간의 네트워크는 완전히 격리된 환경에서 설정할 수 있습니다.

kubeadm을 통해 구축된 온프레미스 쿠버네티스 클러스터를 사용하는 경우 [Air-gapped Installation on Kubernetes](../../../installing-on-kubernetes/on-prem- kubernetes/install-ks-on-linux-airgapped/)를 선택한 다음 직접 연결 또는 에이전트 연결을 통해 Kuberix Enterprise 멀티 클러스터 관리를 활성화합니다.

![KE-federation](/images/docs/v3.3/multicluster-management/introduction/kubesphere-federation/kubesphere-federation.png)

## 벤더 종속제거

Kuberix Enterprise는 강력하고 포괄적인 중앙 제어 평면을 제공하므로 배포 환경이나 클라우드 제공업체에 관계없이 모든 Kuberix Enterprise 클러스터를 통합된 방식으로 관리할 수 있습니다.

## 리소스 요구 사항

다중 클러스터 관리를 활성화하기 전에 환경에 충분한 리소스가 있는지 확인하십시오.

| Namespace      | kube-federation-system | KE-system |
| -------------- | ---------------------- | ----------------- |
| Sub-component  | 2 x controller-manager | tower             |
| CPU Request    | 100 m                  | 100 m             |
| CPU Limit      | 500 m                  | 500 m             |
| Memory Request | 64 MiB                 | 128 MiB           |
| Memory Limit   | 512 MiB                | 256 MiB           |
| Installation   | Optional               | Optional          |

{{< notice note >}}

- The request and limit of CPU and memory resources all refer to single replica.
- After the multi-cluster feature is enabled, tower and controller-manager will be installed on the host cluster. If you use [agent connection](../../../multicluster-management/enable-multicluster/agent-connection/), only tower is needed for member clusters. If you use [direct connection](../../../multicluster-management/enable-multicluster/direct-connection/), no additional component is needed for member clusters.

{{</ notice >}}

## 멀티 클러스터 아키텍처에서 App Store 사용

Kuberix Enterprise의 다른 구성 요소와 달리 [KE 앱 스토어](../../../pluggable-components/app-store/)는 호스트 클러스터 및 멤버 클러스터를 포함한 모든 클러스터에 대한 글로벌 애플리케이션 풀 역할을 합니다. 호스트 클러스터에서 App Store를 활성화하기만 하면 [앱 템플릿](. ./../../project-user-guide/application/app-template/) 및 [앱 저장소](../../../workspace-administration/app-repository/import-helm-repository/ )를 사용할수 있습니다.

그러나 호스트 클러스터에서 활성화하지 않고 멤버 클러스터에서만 App Store를 활성화하면 멀티 클러스터 아키텍처의 클러스터에서 App Store를 사용할 수 없습니다.
