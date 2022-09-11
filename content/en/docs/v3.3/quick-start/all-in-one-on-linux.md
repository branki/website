---
title: "All-in-One Installation of Kubernetes and Kuberix Enterprise on Linux"
keywords: 'Kuberix, Kubernetes, All-in-One, Installation'
description: 'Install Kuberix Enterprise on Linux with a minimal installation package. The tutorial serves as a basic kick-starter for you to understand the container platform, paving the way for learning the following guides.'
linkTitle: "All-in-One Installation on Linux"
weight: 2100,
showSubscribe: true
---

Kuberix Enterprise를 처음 사용하고 컨테이너 플랫폼을 빠르게 발견할 수 있는 방법을 찾는 사람들에게 올인원 모드는 시작하기에 가장 좋은 선택입니다. Kuberix Enterprise 및 쿠버네티스가 모두 머신에 프로비저닝되어 신속한 배포와 번거로움 없는 구성이 특징입니다.

## 1단계: Linux 머신 준비

일체형 설치를 시작하려면 하드웨어 및 운영 체제에 대한 다음 요구 사항에 따라 하나의 호스트만 준비하면 됩니다.

### 하드웨어 권장 사항

<table>
  <tbody>
    <tr>
    <th width='320'>OS</th>
    <th>Minimum Requirements</th>
    </tr>
    <tr>
      <td><b>Ubuntu</b> <i>16.04</i>, <i>18.04</i></td>
      <td>2 CPU cores, 4 GB memory, and 40 GB disk space</td>
    </tr>
    <tr>
      <td><b>Debian</b> <i>Buster</i>, <i>Stretch</i></td>
      <td>2 CPU cores, 4 GB memory, and 40 GB disk space</td>
    </tr><tr>
    <td><b>CentOS</b> <i>7.x</i></td>
      <td>2 CPU cores, 4 GB memory, and 40 GB disk space</td>
    </tr><tr>
    <td><b>Red Hat Enterprise Linux 7</b></td>
      <td>2 CPU cores, 4 GB memory, and 40 GB disk space</td>
    </tr><tr>
    <td><b>SUSE Linux Enterprise Server 15/openSUSE Leap 15.2</b></td>
      <td>2 CPU cores, 4 GB memory, and 40 GB disk space</td>
    </tr>
  </tbody>
</table>

{{< notice note >}}

앞의 시스템 요구 사항 및 다음 지침은 플러그형 구성 요소가 활성화되지 않은 기본 최소 설치에 대한 것입니다. 컴퓨터에 8개 이상의 CPU 코어와 16GB 메모리가 있는 경우 모든 구성 요소를 활성화하는 것이 좋습니다. 자세한 내용은 [플러그 가능한 구성 요소 활성화](../../pluggable-components/)를 참조하십시오.

{{</ notice >}}

### Node requirements

- 'SSH'를 통해 노드에 접근할 수 있습니다.
- `sudo`/`curl`/`openssl`/`tar`를 사용해야 합니다.

### 컨테이너 런타임

클러스터에는 사용 가능한 컨테이너 런타임이 있어야 합니다. KubePOP를 사용하여 클러스터를 설정하는 경우 KubePOP는 기본적으로 최신 버전의 Docker를 설치합니다. 또는 클러스터를 생성하기 전에 Docker 또는 기타 컨테이너 런타임을 수동으로 설치할 수 있습니다.

<table>
  <tbody>
    <tr>
      <th width='500'>Supported Container Runtime</th>
      <th>Version</th>
    </tr>
    <tr>
      <td>Docker</td>
      <td>19.3.8 +</td>
    </tr>
    <tr>
      <td>containerd</td>
      <td>Latest</td>
    </tr><tr>
      <td>CRI-O (experimental, not fully tested)</td>
      <td>Latest</td>
    </tr><tr>
      <td>iSula (experimental, not fully tested)</td>
      <td>Latest</td>
    </tr>
  </tbody>
</table>

### 종속성 요구 사항

KubePOP는 쿠버네티스와 Kuberix Enterprise를 함께 설치할 수 있습니다. 설치해야 하는 종속성은 설치할 쿠버네티스 버전에 따라 다를 수 있습니다. 다음 목록을 참조하여 사전에 노드에 관련 종속성을 설치해야 하는지 확인할 수 있습니다.

<table>
  <tbody>
    <tr>
      <th>Dependency</th>
     <th>Kubernetes Version ≥ 1.18</th>
      <th>Kubernetes Version < 1.18</th>
    </tr>
    <tr>
      <td><code>socat</code></td>
     <td>Required</td> 
      <td>Optional but recommended</td> 
     </tr>
    <tr>
      <td><code>conntrack</code></td>
     <td>Required</td> 
      <td>Optional but recommended</td> 
    </tr><tr>
    <td><code>ebtables</code></td>
     <td>Optional but recommended</td> 
    <td>Optional but recommended</td> 
    </tr><tr>
    <td><code>ipset</code></td>
    <td>Optional but recommended</td> 
     <td>Optional but recommended</td> 
    </tr>
  </tbody>
</table>

{{< notice info >}}

Go로 개발된 KubePOP는 이전에 사용된 가능 기반 설치 프로그램을 대체하는 새로운 설치 도구입니다. KubePOP는 Kuberix Enterprise와 쿠버네티스를 별도로 설치하거나 한 번에 설치할 수 있어 사용자에게 유연한 설치 선택을 제공하여 편리하고 효율적입니다.

{{</ notice >}}

### 네트워크 및 DNS 요구 사항

- `/etc/resolv.conf`의 DNS 주소가 사용 가능한지 확인하십시오. 그렇지 않으면 클러스터에서 일부 DNS 문제가 발생할 수 있습니다.
- 네트워크 구성에서 방화벽 규칙이나 보안 그룹을 사용하는 경우 인프라 구성 요소가 특정 포트를 통해 서로 통신할 수 있는지 확인해야 합니다. 방화벽을 끄는 것이 좋습니다. 자세한 내용은 [포트 요구 사항](../../installing-on-linux/introduction/port-firewall/)을 참조하십시오.
- 지원되는 CNI 플러그인: Calico 및 Flannel. Cilium 및 Kube-OVN과 같은 다른 프로그램도 작동할 수 있지만 완전히 테스트되지는 않았습니다.

{{< notice tip >}}

- OS가 깨끗한 상태(다른 소프트웨어가 설치되지 않은 상태)를 권장합니다. 그렇지 않으면 충돌이 발생할 수 있습니다.
- 'dockerhub.io'에서 이미지 다운로드가 어려울 경우 레지스트리 미러(부스터)를 준비하는 것을 권장합니다. 자세한 내용은 [설치를 위한 부스터 구성](../../faq/installation/configure-booster/)을 참조하십시오.

{{</ notice >}}

## 2단계: KubePOP 다운로드

KubePOP를 다운로드하려면 다음 단계를 수행하십시오.

{{< tabs >}}

{{< tab "Good network connections to GitHub/Googleapis" >}}

Download KubePOP from its [GitHub Release Page](https://github.com/kubePOP/releases) or run the following command:

```bash
curl -sfL https://get-kp.kuberix.io | VERSION=v2.2.2 sh -
```

{{</ tab >}}

{{< tab "Poor network connections to GitHub/Googleapis" >}}

다음 명령을 먼저 실행하여 올바른 영역에서 KubePOP를 다운로드했는지 확인하십시오.

```bash
export KKZONE=cn
```

다음 명령을 실행하여 KubePOP를 다운로드하십시오.:

```bash
curl -sfL https://get-kp.kuberix.io| VERSION=v2.2.2 sh -
```

{{< notice note >}}

KubePOP를 다운로드한 후 Googleapis에 대한 네트워크 연결이 좋지 않은 새 컴퓨터로 전송하는 경우 다음 단계를 진행하기 전에 `export KKZONE=cn`을 다시 실행해야 합니다.

{{</ notice >}} 

{{</ tab >}}

{{</ tabs >}}

{{< notice note >}}

위의 명령은 KubePOP의 최신 릴리스(v2.2.2)를 다운로드합니다. 명령에서 버전 번호를 변경하여 특정 버전을 다운로드할 수 있습니다.

{{</ notice >}} 

Make `kp` executable:

```bash
chmod +x kp
```

## 3단계: 설치 시작하기

올인원 설치의 경우 하나의 명령만 실행하면 됩니다. 템플릿은 다음과 같습니다.:

```bash
./kp create cluster [--with-kubernetes version] [--with-KuberixEnterprise version]
```

Kuberix Enterprise가 설치된 쿠버네티스 클러스터를 생성하려면 다음 명령어를 예시로 참조하세요.:

```bash
./kp create cluster --with-kubernetes v1.22.10 --with-KuberixEnterprise v3.3.0
```

{{< notice note >}}

- Kuberix Enterprise 3.3.0의 권장 쿠버네티스 버전: v1.19.x, v1.20.x, v1.21.x, v1.22.x 및 v1.23.x(실험 지원). 쿠버네티스 버전을 지정하지 않으면 KubePOP는 기본적으로 쿠버네티스 v1.23.7을 설치합니다. 지원되는 쿠버네티스 버전에 대한 자세한 내용은 [지원 매트릭스](../../installing-on-linux/introduction/kubekey/#support-matrix)를 참조하십시오.
- 일체형 설치의 경우 구성을 변경할 필요가 없습니다.
- 이 단계에서 명령어에 `--with-KuberixEnterprise` 플래그를 추가하지 않으면 Kuberix Enterprise가 배포되지 않습니다. KubePOP는 쿠버네티스만 설치합니다. Kuberix Enterprise 버전을 지정하지 않고 `--with-KuberixEnterprise` 플래그를 추가하면 최신 버전의 Kuberix Enterprise가 설치됩니다.
- KubePOP는 [OpenEBS](https://openebs.io/)를 기본적으로 설치하여 개발 및 테스트 환경에 LocalPV를 프로비저닝하므로 신규 사용자에게 편리합니다. 다른 스토리지 클래스에 대해서는 [영구 스토리지 구성](../../installing-on-linux/persistent-storage-configurations/understand-persistent-storage/)을 참조하십시오.

{{</ notice >}}

명령을 실행하면 환경 검사를 위한 테이블이 표시됩니다. 자세한 내용은 [노드 요구 사항](#node-requirements) 및 [종속성 요구 사항](#dependency-requirements)을 참조하세요. 계속하려면 '예'를 입력하세요.

## 4단계: 설치 확인

다음 명령을 실행하여 결과를 확인하십시오.

```bash
kubectl logs -n ke-system $(kubectl get pod -n ke-system -l 'app in (ke-install, ke-installer)' -o jsonpath='{.items[0].metadata.name}') -f
```

출력은 기본적으로 'NodePort 30880'을 통해 노출되는 웹 콘솔의 IP 주소와 포트 번호를 표시합니다. 이제 기본 계정과 비밀번호(`admin/P@88w0rd`)로 `<NodeIP>:30880`에서 콘솔에 액세스할 수 있습니다.

```bash
#####################################################
###         Welcome to Kuberix Enterprise!        ###
#####################################################

Console: http://192.168.0.2:30880
Account: admin
Password: P@88w0rd

NOTES：
  1. After you log into the console, please check the
     monitoring status of service components in
     "Cluster Management". If any service is not
     ready, please wait patiently until all components 
     are up and running.
  2. Please change the default password after login.

#####################################################
https://kuberix.com             20xx-xx-xx xx:xx:xx
#####################################################
```

{{< notice note >}}

외부 사용자가 콘솔에 액세스할 수 있도록 포트 전달 규칙을 구성하고 보안 그룹에서 포트를 열어야 할 수 있습니다.

{{</ notice >}}

콘솔에 로그인한 후 **시스템 구성 요소**에서 다양한 구성 요소의 상태를 확인할 수 있습니다. 관련 서비스를 사용하려면 일부 구성 요소가 실행될 때까지 기다려야 할 수 있습니다. 또한 `kubectl get pod --all-namespaces`를 사용하여 Kuberix Enterprise 워크로드의 실행 상태를 검사할 수 있습니다.

## 플러그인 가능 구성 요소 활성화(선택 사항)

이 가이드는 기본적으로 최소 설치에만 사용됩니다. Kuberix Enterprise에서 다른 구성 요소를 활성화하는 방법에 대한 자세한 내용은 [플러그 가능한 구성 요소 활성화](../../pluggable-components/)를 참조하십시오.

## 코드 데모
<script src="https://asciinema.org/a/379741.js" id="asciicast-379741" async></script>
