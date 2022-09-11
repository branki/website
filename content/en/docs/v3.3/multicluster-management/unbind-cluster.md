---
title: "Unbind a Cluster"
keywords: 'Kubernetes, Kuberix, multicluster, hybrid-cloud'
description: 'Learn how to unbind a cluster from your cluster pool in Kuberix Enterprise.'
linkTitle: "Unbind a Cluster"
weight: 5500
---

이 튜토리얼은 Kuberix Enterprise의 중앙 제어 평면에서 클러스터를 바인딩 해제하는 방법을 보여줍니다.

## 전제 조건

- 다중 클러스터 관리를 활성화했습니다.
- **클러스터 관리** 권한을 포함한 역할을 부여받은 사용자가 필요합니다. 예를 들어 콘솔에 'admin'으로 직접 로그인하거나 권한이 있는 새 역할을 생성하여 사용자에게 할당할 수 있습니다.

## 클러스터 바인딩 해제
다음 방법 중 하나를 사용하여 클러스터를 바인딩 해제할 수 있습니다.

**방법 1**

1. 왼쪽 상단 모서리에서 **플랫폼**을 클릭하고 **클러스터 관리**를 선택합니다.

2. **구성원 클러스터** 영역에서 <img src="/images/docs/v3.3/common-icons/three-dots.png" width="15" alt="icon" />를 클릭합니다. 제어 영역에서 제거하려는 클러스터의 오른쪽을 클릭한 다음 **Unbind Cluster**를 클릭합니다.

3. 표시되는 **Unbind Cluster** 대화 상자에서 위험 경고를 주의 깊게 읽으십시오. 계속 진행하려면 구성원 클러스터의 이름을 입력하고 **확인**을 클릭합니다.

**방법 2**

1. 왼쪽 상단 모서리에서 **플랫폼**을 클릭하고 **클러스터 관리**를 선택합니다.

2. **Member Clusters** 영역에서 제어 평면에서 제거하려는 구성원 클러스터의 이름을 클릭합니다.

3. 왼쪽 탐색 트리에서 **클러스터 설정** > **기본 정보**를 선택합니다.

4. **클러스터 정보** 영역에서 **관리** > **클러스터 바인딩 해제**를 클릭합니다.

5. 표시되는 **Unbind Cluster** 대화 상자에서 위험 경고를 주의 깊게 읽으십시오. 계속 진행하려면 구성원 클러스터의 이름을 입력하고 **확인**을 클릭합니다.

   {{< notice note >}}

   After you unbind the cluster, you cannot manage it from the control plane while Kubernetes resources on the cluster will not be deleted.

   {{</ notice >}} 

## 비정상 클러스터 바인딩 해제

경우에 따라 위의 단계에 따라 클러스터를 바인딩 해제할 수 없습니다. 예를 들어 잘못된 자격 증명으로 클러스터를 가져오고 **클러스터 설정**에 액세스할 수 없습니다. 이 경우 다음 명령을 실행하여 비정상 클러스터를 바인딩 해제합니다.

```bash
kubectl delete cluster <cluster name>
```

