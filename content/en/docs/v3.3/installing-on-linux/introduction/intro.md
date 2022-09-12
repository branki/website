---
title: "Installing on Linux — Overview"
keywords: 'Kubernetes, Kuberix, Linux, Installation'
description: 'Explore the general content in this chapter, including installation preparation, installation tool and method, and storage configurations.'
linkTitle: "Overview"
weight: 3110
---

Linux에 설치하는 경우 Kuberix Enterprise는 클라우드와 AWS EC2, Azure VM 및 베어메탈과 같은 온프레미스 환경 모두에 배포할 수 있습니다.

Kuberix Enterprise는 쿠버네티스 및 관련 애드온 설치를 지원하는 경량 설치 프로그램인 [KubePOP](https://github.com/ke/kubepop)를 사용자에게 제공하므로 설치 프로세스가 쉽고 친숙합니다. KubePOP는 사용자가 온라인으로 클러스터를 생성하는 데 도움이 될 뿐만 아니라 에어갭 설치 솔루션 역할도 합니다.

다음은 사용 가능한 설치 옵션 목록입니다.

- [올인원 설치](../../../quick-start/all-in-one-on-linux/): 단일 노드에 Kuberix Enterprise를 설치합니다. 사용자가 Kuberix Enterprise에 빠르게 익숙해지기만 하면 됩니다.
- [멀티 노드 설치](../multioverview/): Kuberix Enterprise를 멀티 노드에 설치합니다. 테스트 또는 개발용입니다.
- [Linux에서 Air-gapped 설치](../air-gapped-installation/): Kuberix Enterprise의 모든 이미지가 패키지로 캡슐화되었습니다. Linux 시스템에서 에어 갭 설치에 편리합니다.
- [고가용성 설치](../../../installing-on-linux/high-availability-configurations/ha-configuration/): 프로덕션에 사용되는 다중 노드가 있는 고가용성 Kuberix Enterprise 클러스터를 설치합니다.
- 최소 패키지: Kuberix Enterprise의 최소 필수 시스템 구성 요소만 설치합니다. 최소 리소스 요구 사항은 다음과 같습니다.
  - 2개의 CPU
  - 4GB RAM
  - 40GB 스토리지
- [전체 패키지](../../../pluggable-components/): DevOps, 서비스 메시 및 경고와 같은 Kuberix Enterprise의 사용 가능한 모든 시스템 구성 요소를 설치합니다.

{{< notice note >}}

모든 옵션이 상호 배타적인 것은 아닙니다. 예를 들어 에어 갭 환경의 여러 노드에 최소 패키지로 Kuberix Enterprise를 배포할 수 있습니다.

{{</ notice >}}

기존 쿠버네티스 클러스터가 있는 경우 [쿠버네티스 설치 개요](../../../installing-on-kubernetes/introduction/overview/)를 참조하세요.

## 설치 전

- 인터넷에서 이미지를 가져오므로 환경에서 인터넷에 액세스할 수 있어야 합니다. 그렇지 않으면 [에어 갭 환경에 쿠버네티스 설치](../air-gapped-installation/)가 필요합니다.
- 일체형 설치의 경우 제어 평면과 작업자가 모두 하나의 노드입니다.
- 다중 노드 설치의 경우 구성 파일에 호스트 정보를 제공해야 합니다.
- 설치하기 전에 [포트 요구 사항](../port-firewall/)을 참조하십시오.

## KubePOP

[KubePOP](https://github.com/ke/kubepop)는 클러스터 설치 및 구성에 대한 효율적인 접근 방식을 제공합니다. 이를 사용하여 쿠버네티스 클러스터를 생성, 확장 및 업그레이드할 수 있습니다. 또한 클러스터를 설정할 때 클라우드 네이티브 추가 기능(YAML 또는 차트)을 설치할 수 있습니다. 자세한 내용은 [KubePOP](../kubepop)를 참조하십시오.

## 개발 및 테스트를 위한 빠른 설치

Kuberix Enterprise는 v2.1.0부터 일부 구성 요소를 분리했습니다. KubePOP는 빠른 설치와 최소한의 리소스 소비를 특징으로 하기 때문에 기본적으로 필요한 구성 요소만 설치합니다. 향상된 플러그 가능 기능을 활성화하려면 [플러그 가능 구성 요소 활성화](../../../pluggable-components/)에서 자세한 내용을 참조하십시오.

Kuberix Enterprise의 빠른 설치는 [openEBS](https://openebs.xml) 기반의 [Local Volume](https://kubernetes.io/docs/concepts/storage/volumes/#local)을 사용하기 때문에 개발 또는 테스트 전용입니다. io/) 기본적으로 스토리지 서비스를 제공합니다. 프로덕션 설치를 원하면 [고가용성 구성](../../../installing-on-linux/high-availability-configurations/ha-configuration/)을 참조하십시오.

## 스토리지 구성

Kuberix Enterprise를 사용하면 설치 전후에 영구 스토리지 서비스를 구성할 수 있습니다. 한편, Kuberix Enterprise는 상용 스토리지 제품뿐만 아니라 다양한 오픈 소스 스토리지 솔루션(예: Ceph 및 GlusterFS)을 지원합니다. Kuberix Enterprise를 설치하기 전에 스토리지 클래스를 구성하는 방법에 대한 자세한 지침은 [영구 스토리지 구성](../../../installing-on-linux/persistent-storage-configurations/understand-persistent-storage/)을 참조하십시오. .

Kuberix Enterprise를 설치한 후 워크로드에 대해 다른 스토리지 클래스를 설정하는 방법에 대한 자세한 내용은 [스토리지 클래스](../../../cluster-administration/storageclass/)를 참조하십시오.

## 클러스터 운영 및 유지 관리

### 새 노드 추가

KubePOP를 사용하면 특히 프로덕션 환경에서 설치 후 더 많은 리소스 요구 사항을 충족하기 위해 노드 수를 늘릴 수 있습니다. 자세한 내용은 [새 노드 추가](../../../installing-on-linux/cluster-operation/add-new-nodes/)를 참조하십시오.

### 노드 제거

노드를 제거하기 전에 노드를 비워야 합니다. 자세한 내용은 [노드 제거](../../../installing-on-linux/cluster-operation/remove-nodes/)를 참조하십시오.

## 설치 제거

Kuberix Enterprise를 제거하면 시스템에서 제거되며 되돌릴 수 없습니다. 조작에 주의해 주십시오.

자세한 내용은 [Kuberix Enterprise 및 쿠버네티스 제거](../../../installing-on-linux/uninstall-kuberixEnterprise-and-kubernetes/)를 참조하십시오.
