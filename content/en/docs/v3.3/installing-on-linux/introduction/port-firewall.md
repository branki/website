---
title: "Port Requirements"
keywords: 'Kubernetes, Kuberix, port-requirements, firewall-rules'
description: 'Understand the specific port requirements for different services in Kuberix Enterprise.'

linkTitle: "Port Requirements"
weight: 3150
---


Kuberix Enterprise는 서비스 간 통신을 위해 특정 포트가 필요합니다. 네트워크가 방화벽 규칙으로 구성된 경우 인프라 구성 요소가 특정 프로세스 또는 서비스의 통신 끝점 역할을 하는 특정 포트를 통해 서로 통신할 수 있도록 해야 합니다.

|Service|Protocol|Action|Start Port|End Port|Notes
|---|---|---|---|---|---|
|ssh|TCP|allow|22|
|etcd|TCP|allow|2379|2380|
|apiserver|TCP|allow|6443|
|calico|TCP|allow|9099|9100|
|bgp|TCP|allow|179||
|nodeport|TCP|allow|30000|32767|
|master|TCP|allow|10250|10258|
|dns|TCP|allow|53|
|dns|UDP|allow|53|
|local-registry|TCP|allow|5000||For the offline environment|
|local-apt|TCP|allow|5080||For the offline environment|
|rpcbind|TCP|allow|111|| Required if NFS is used|
|ipip| IPENCAP / IPIP|allow| | |Calico needs to allow the ipip protocol |
|metrics-server| TCP|allow| 8443 |


{{< notice note >}}

Calico 네트워크 플러그인을 사용하고 클라우드의 클래식 네트워크에서 클러스터를 실행할 때 소스 IP에 대해 IPENCAP 및 IPIP 프로토콜을 모두 활성화해야 합니다.

{{</ notice >}}
