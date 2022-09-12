---
title: "Delete Kubernetes Nodes"
keywords: 'Kubernetes, Kuberix, scale-in, remove-nodes'
description: 'Cordon a node and even delete a node to scale in your cluster.'
linkTitle: "Delete Nodes"
weight: 3620
---

## 쿠버네티스 노드 차단

노드를 예약 불가능으로 표시하면 스케줄러가 노드의 기존 포드에 영향을 미치지 않으면서 새 포드를 해당 노드에 배치하지 못하게 됩니다. 이는 노드 재부팅 또는 기타 유지 관리 전에 준비 단계로 유용합니다.

콘솔에 `admin`으로 로그인하고 **Cluster Management** 페이지로 이동합니다. 노드를 예약할 수 없는 것으로 표시하려면 왼쪽 메뉴의 **Nodes** 아래에서 **Cluster Nodes**를 선택하고 클러스터에서 제거할 노드를 찾은 다음 **Cordon**을 클릭합니다. 또는 `kubectl cordon $NODENAME` 명령을 직접 실행할 수 있습니다. 자세한 내용은 [쿠버네티스 노드](https://kubernetes.io/docs/concepts/architecture/nodes/)를 참조하십시오.

{{< notice note >}}

DaemonSet의 일부인 포드는 예약할 수 없는 노드에서 실행되는 것을 허용합니다. DaemonSets는 일반적으로 워크로드 애플리케이션이 소진되는 경우에도 노드에서 실행되어야 하는 노드 로컬 서비스를 제공합니다.

{{</ notice >}}

## 쿠버네티스 노드 삭제

1. 노드를 삭제하려면 먼저 클러스터의 구성 파일을 준비해야 합니다. 이 파일은 [클러스터 설정](../../introduction/multioverview/#1-create-an- 예제 구성 파일). 없는 경우 [KubePOP](https://github.com/ke/kubepop)를 사용하여 클러스터 정보를 검색합니다(기본적으로 `sample.yaml` 파일이 생성됨).

   ```bash
   ./kp create config --from-cluster
   ```

2. 구성 파일에 호스트의 모든 정보를 제공했는지 확인하고 다음 명령을 실행하여 노드를 삭제합니다.

   ```bash
   ./kp delete node <nodeName> -f sample.yaml
   ```
