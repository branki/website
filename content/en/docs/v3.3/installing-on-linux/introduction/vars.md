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
     <td>The CNI plugin to use. KubePOP installs Calico by default while you can also specify Flannel. Note that some features can only be used when Calico is adopted as the CNI plugin, such as Pod IP Pools.</td>
   </tr>
   <tr>
     <td><code>calico.ipipMode</code>*</td>
     <td>The IPIP Mode to use for the IPv4 POOL created at startup. If it is set to a value other than <code>Never</code>, <code>vxlanMode</code> should be set to <code>Never</code>. Allowed values are <code>Always</code>, <code>CrossSubnet</code> and <code>Never</code>. It defaults to <code>Always</code>.</td>
   </tr>
   <tr>
     <td><code>calico.vxlanMode</code>*</td>
     <td>The VXLAN Mode to use for the IPv4 POOL created at startup. If it is set to a value other than <code>Never</code>, <code>ipipMode</code> should be set to <code>Never</code>. Allowed values are <code>Always</code>, <code>CrossSubnet</code> and <code>Never</code>. It defaults to <code>Never</code>.</td>
   </tr>
   <tr>
     <td><code>calico.vethMTU</code>*</td>
     <td>The maximum transmission unit (MTU) setting determines the largest packet size that can be transmitted through your network. It defaults to <code>1440</code>.</td>
   </tr>
   <tr>
     <td><code>kubePodsCIDR</code></td>
     <td>A valid CIDR block for your Kubernetes Pod subnet. It should not overlap with your node subnet and your Kubernetes Services subnet.</td>
   </tr>
   <tr>
     <td><code>kubeServiceCIDR</code></td>
     <td>A valid CIDR block for your Kubernetes Services. It should not overlap with your node subnet and your Kubernetes Pod subnet.</td>
   </tr>
   <tr>
     <th colSpan='2'><code>registry</code></th>
   </tr>
   <tr>
     <td><code>registryMirrors</code></td>
     <td>Configure a Docker registry mirror to speed up downloads. For more information, see {{< contentLink "https://docs.docker.com/registry/recipes/mirror/#configure-the-docker-daemon" "Configure the Docker daemon" >}}.</td>
   </tr>
   <tr>
     <td><code>insecureRegistries</code></td>
     <td>Set an address of insecure image registry. For more information, see {{< contentLink "https://docs.docker.com/registry/insecure/" "Test an insecure registry" >}}.</td>
   </tr>
   <tr>
     <td><code>privateRegistry</code>*</td>
     <td>Configure a private image registry for air-gapped installation (for example, a Docker local registry or Harbor). For more information, see {{< contentLink "docs/installing-on-linux/introduction/air-gapped-installation/" "Air-gapped Installation on Linux" >}}.</td>
   </tr> 
   </tbody>
   </table>


{{< notice note >}}

- \* By default, KubeKey does not define these parameters in the configuration file while you can manually add them and customize their values.
- `addons` is used to install cloud-native add-ons (YAML or Chart). For more information, see [this file](https://github.com/kubesphere/kubekey/blob/release-2.2/docs/addons.md).
- This page only lists part of the parameters in the configuration file created by KubeKey. For more information about other parameters, see [this example file](https://github.com/kubesphere/kubekey/blob/release-2.2/docs/config-example.md).

{{</ notice >}} 
