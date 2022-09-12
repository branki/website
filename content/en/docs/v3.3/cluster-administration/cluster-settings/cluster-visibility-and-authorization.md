---
title: "Cluster Visibility and Authorization"
keywords: "Cluster Visibility, Cluster Management"
description: "Learn how to set up cluster visibility and authorization."
linkTitle: "Cluster Visibility and Authorization"
weight: 8610
---

Kuberix Enterprise에서는 작업 공간 리소스가 클러스터에서 모두 실행될 수 있도록 권한 부여를 통해 클러스터를 여러 작업 공간에 할당할 수 있습니다. 동시에 작업 공간을 여러 클러스터와 연결할 수도 있습니다. 필요한 권한이 있는 작업 영역 사용자는 작업 영역에 할당된 클러스터를 사용하여 다중 클러스터 프로젝트를 생성할 수 있습니다.

이 가이드는 클러스터 가시성을 설정하는 방법을 보여줍니다.

## 전제 조건
* [멀티 클러스터 기능](../../../multicluster-management/)을 활성화해야 합니다.
* ws-manager와 같은 작업공간 생성 권한이 있는 사용자와 작업공간이 필요합니다. 자세한 내용은 [작업 공간, 프로젝트, 사용자 및 역할 생성](../../../quick-start/create-workspace-and-project/)을 참조하십시오.

## 클러스터 가시성 설정

### 작업 공간을 만들 때 사용 가능한 클러스터 선택

1. `ws-manager`와 같은 작업 공간 생성 권한이 있는 사용자로 Kuberix Enterprise에 로그인합니다.

2. 왼쪽 상단의 **Platform**을 클릭하고 **Access Control**를 선택합니다. **Workspaces**의 탐색 모음에서 **Create**를 클릭합니다.

3. 작업 공간에 대한 기본 정보를 제공하고 **Next**를 클릭합니다.

4. **Cluster Settings** 페이지에서 사용 가능한 클러스터 목록을 볼 수 있습니다. 작업 공간에 할당할 클러스터를 선택하고 **Create**를 클릭합니다.

5. 작업 영역이 생성된 후 필요한 권한이 있는 작업 영역 구성원은 연결된 클러스터에서 실행되는 리소스를 생성할 수 있습니다.

   {{< notice warning >}}

클러스터 전체의 안정성이 저하될 수 있는 과도한 로드를 피하기 위해 호스트 클러스터에 리소스를 생성하지 마십시오.

   {{</ notice >}}

### 작업 공간이 생성된 후 클러스터 가시성을 설정합니다.

작업 영역이 생성된 후 권한 부여를 통해 작업 영역에 추가 클러스터를 할당하거나 작업 영역에서 클러스터를 바인딩 해제할 수 있습니다. 아래 단계에 따라 클러스터의 가시성을 조정하십시오.

1. `admin`과 같이 클러스터 관리 권한이 있는 사용자로 Kuberix Enterprise에 로그인합니다.

2. 왼쪽 상단 모서리에서 **Platform**을 클릭하고 **Cluster Management**를 선택합니다. 클러스터 정보를 보려면 목록에서 클러스터를 선택하십시오.

3. 탐색 모음의 **Cluster Settings**에서 **Cluster Visibility**을 선택합니다.

4. 승인된 작업 영역 목록을 볼 수 있습니다. 즉, 현재 클러스터를 이러한 모든 작업 영역의 리소스에 사용할 수 있습니다.

5. **Edit Visibility**을 클릭하여 클러스터 가시성을 설정합니다. 클러스터를 사용할 수 있는 새 작업 영역을 선택하거나 작업 영역에서 바인딩을 해제할 수 있습니다.

### 클러스터를 공개로 설정

플랫폼 사용자가 리소스를 생성하고 예약할 수 있는 클러스터에 액세스할 수 있도록 **Set as Public Cluster**을 선택할 수 있습니다.
