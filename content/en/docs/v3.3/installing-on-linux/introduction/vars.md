---
title: "Kubernetes Cluster Configurations"
keywords: 'Kubernetes, Kuberix, cluster, configuration, KubePOP'
description: 'Customize your Kubernetes settings in the configuration file for your cluster.'
linkTitle: "Kubernetes Cluster Configurations"
weight: 3160
---

쿠버네티스 클러스터를 생성할 때 [KubePOP](../kubepop/)를 사용하여 클러스터의 기본 정보가 포함된 구성 파일(`config-sample.yaml`)을 정의할 수 있습니다. 구성 파일의 쿠버네티스 관련 매개변수는 다음 예를 참조하십시오.

```yaml
  kubernetes:
    version: v1.22.10
    imageRepo: kuberixEnterprise
    clusterName: cluster.local
    masqueradeAll: false
    maxPods: 110
    nodeCidrMaskSize: 24
    proxyMode: ipvs
  network:
    plugin: calico
    calico:
      ipipMode: Always
      vxlanMode: Never
      vethMTU: 1440
    kubePodsCIDR: 10.233.64.0/18
    kubeServiceCIDR: 10.233.0.0/18
  registry:
    registryMirrors: []
    insecureRegistries: []
    privateRegistry: ""
  addons: []
```

아래 표는 위의 매개변수에 대해 자세히 설명합니다.

  <table border="1">
   <tbody>
   <tr>
     <th width='140'>Parameter</th>
     <th>Description</th>
   </tr>
   <tr>
     <th colSpan='2'><code>kubernetes</code></th>
   </tr>
   <tr>
     <td><code>version</code></td>
     <td>설치할 쿠버네티스 버전입니다. 쿠버네티스 버전을 지정하지 않으면 {{< contentLink "docs/installing-on-linux/introduction/kubepop" "KubePOP" >}} v2.2.2가 기본적으로 쿠버네티스 v1.23.7을 설치합니다. 자세한 내용은 {{< contentLink "docs/installing-on-linux/introduction/kubepop/#support-matrix" "Support Matrix" >}}를 참조하세요.</td>
   </tr>
   <tr>
     <td><code>imageRepo</code></td>
     <td>이미지가 다운로드될 Docker Hub 리포지토리입니다.</td>
   </tr>
   <tr>
     <td><code>clusterName</code></td>
     <td>쿠버네티스 클러스터 이름입니다.</td>
   </tr>
   <tr>
     <td><code>masqueradeAll</code>*</td>
     <td><code>masqueradeAll은 순수 iptables 프록시 모드를 사용하는 경우 kube-proxy에 SNAT에 모든 것을 알려줍니다. 기본값은 false입니다.</td>
   </tr>
   <tr>
     <td><code>maxPods</code>*</td>
     <td>이 Kubelet에서 실행할 수 있는 최대 Pod 수입니다. 기본값은 <code>110</code> 입니다.</td>
   </tr>
   <tr>
     <td><code>nodeCidrMaskSize</code>*</td>
     <td>클러스터의 노드 CIDR에 대한 마스크 크기입니다. 기본값은 <code>24</code> 입니다.</td>
   </tr>
   <tr>
     <td><code>proxyMode</code>*</td>
     <td>사용할 프록시 모드입니다. 기본값은 <code>ipvs</code> 입니다.</td>
   </tr>
   <tr>
     <th colSpan='2'><code>network</code></th>
   </tr>
   <tr>
     <td><code>plugin</code></td>
     <td>사용할 CNI 플러그인입니다. KubePOP은 기본적으로 Calico를 설치하지만 Flannel을 지정할 수도 있습니다. Pod IP Pools와 같은 일부 기능은 Calico가 CNI 플러그인으로 채택된 경우에만 사용할 수 있습니다.</td>
   </tr>
   <tr>
     <td><code>calico.ipipMode</code>*</td>
     <td>시작 시 생성된 IPv4 POOL에 사용할 IPIP 모드입니다. <code>Never</code> 이외의 값으로 설정하면 <code>vxlanMode</code>를 <code>Never</code>로 설정해야 합니다. 허용되는 값은 <code>Always</code>, <code>CrossSubnet</code> 및<code>Never</code>입니다. 기본값은 <code>Always</code>입니다.</td>
   </tr>
   <tr>
     <td><code>calico.vxlanMode</code>*</td>
     <td>시작 시 생성된 IPv4 풀에 사용할 VXLAN 모드입니다. <code>Never</code> 이외의 값으로 설정하면 <code>ipipMode</code>를<code>Never</code>로 설정해야 합니다. 허용되는 값은 <code>Always</code>, <code>CrossSubnet</code> 및 <code>Never</code>입니다. 기본값은 <code>Never</code>입니다.</td>
   </tr>
   <tr>
     <td><code>calico.vethMTU</code>*</td>
     <td>MTU(최대 전송 단위) 설정은 네트워크를 통해 전송할 수 있는 최대 패킷 크기를 결정합니다. 기본값은 <code>1440</code>입니다.</td>
   </tr>
   <tr>
     <td><code>kubePodsCIDR</code></td>
     <td>쿠버네티스 Pod 서브넷에 대한 유효한 CIDR 블록입니다. 노드 서브넷 및 쿠버네티스 서비스 서브넷과 겹치지 않아야 합니다.</td>
   </tr>
   <tr>
     <td><code>kubeServiceCIDR</code></td>
     <td>쿠버네티스 서비스에 대한 유효한 CIDR 블록입니다. 노드 서브넷 및 쿠버네티스 Pod 서브넷과 겹치지 않아야 합니다.</td>
   </tr>
   <tr>
     <th colSpan='2'><code>registry</code></th>
   </tr>
   <tr>
     <td><code>registryMirrors</code></td>
     <td>다운로드 속도를 높이기 위해 Docker 레지스트리 미러를 구성합니다. 자세한 내용은 {{< contentLink "https://docs.docker.com/registry/recipes/mirror/#configure-the-docker-daemon" "도커 데몬 구성" >}}을 참조하세요.</td>
   </tr>
   <tr>
     <td><code>insecureRegistries</code></td>
     <td>안전하지 않은 이미지 레지스트리의 주소를 설정합니다. 자세한 내용은 {{< contentLink "https://docs.docker.com/registry/insecure/" "안전하지 않은 레지스트리 테스트" >}}를 참조하세요.</td>
   </tr>
   <tr>
     <td><code>privateRegistry</code>*</td>
     <td>에어 갭 설치를 위해 개인 이미지 레지스트리를 구성합니다(예: Docker 로컬 레지스트리 또는 Harbour). 자세한 내용은 {{< contentLink "docs/installing-on-linux/introduction/air-gapped-installation/" "Air-gapped Installation on Linux" >}}를 참조하세요.</td>
   </tr> 
   </tbody>
   </table>


{{< notice note >}}

- \* 기본적으로 KubePOP는 구성 파일에서 이러한 매개변수를 정의하지 않지만 수동으로 추가하고 값을 사용자 지정할 수 있습니다.
- 'addons'는 클라우드 네이티브 추가 기능(YAML 또는 Chart)을 설치하는 데 사용됩니다. 자세한 내용은 [이 파일](https://github.com/ke/kubepop/blob/release-2.2/docs/addons.md)을 참조하세요.
- 이 페이지는 KubePOP에서 생성한 구성 파일의 매개변수 중 일부만 나열합니다. 다른 매개변수에 대한 자세한 내용은 [이 예제 파일](https://github.com/ke/kubepop/blob/release-2.2/docs/config-example.md)을 참조하십시오.

{{</ notice >}} 
