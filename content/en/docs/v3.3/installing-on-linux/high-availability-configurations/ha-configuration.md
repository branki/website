---
title: "Set up an HA Cluster Using a Load Balancer"
keywords: 'Kuberix, Kubernetes, HA, high availability, installation, configuration'
description: 'Learn how to create a highly available cluster using a load balancer.'
linkTitle: "Set up an HA Cluster Using a Load Balancer"
weight: 3220
---

[멀티 노드 설치](../../../installing-on-linux/introduction/multioverview/)의 튜토리얼을 기반으로 Kuberix Enterprise가 설치된 쿠버네티스 클러스터(제어 평면 노드)를 설정할 수 있습니다. 제어 평면 노드가 있는 클러스터는 대부분의 경우 개발 및 테스트에 충분할 수 있습니다. 그러나 프로덕션 환경의 경우 클러스터의 고가용성을 고려해야 합니다. 주요 구성 요소(예: kube-apiserver, kube-scheduler 및 kube-controller-manager)가 모두 동일한 제어 평면 노드에서 실행 중인 경우 제어 평면 노드가 다운되면 쿠버네티스 및 Kuberix Enterprise를 사용할 수 없습니다. 따라서 여러 컨트롤 플레인 노드가 있는 로드 밸런서를 프로비저닝하여 고가용성 클러스터를 설정해야 합니다. 모든 클라우드 로드 밸런서 또는 하드웨어 로드 밸런서(예: F5)를 사용할 수 있습니다. 또한 Keepalived 및 [HAproxy](https://www.haproxy.com/) 또는 Nginx도 고가용성 클러스터를 생성하기 위한 대안입니다.

이 튜토리얼은 Linux에 Kuberix Enterprise를 설치할 때 고가용성 클러스터의 일반 구성을 보여줍니다.

## 건축물

시작하기 전에 6개의 Linux 시스템을 준비했는지 확인하십시오. 이 중 3개는 제어 평면 노드로, 나머지 3개는 작업자 노드로 사용합니다. 다음 이미지는 개인 IP 주소 및 역할을 포함하여 이러한 머신의 세부 정보를 보여줍니다. 시스템 및 네트워크 요구 사항에 대한 자세한 내용은 [다중 노드 설치](../../../installing-on-linux/introduction/multioverview/#step-1-prepare-linux-hosts)를 참조하십시오.

![ha-architecture](/images/docs/v3.3/installing-on-linux/high-availability-configurations/set-up-ha-cluster-using-lb/ha-architecture.png)

## 로드 밸런서 구성

주요 포트에서 수신 대기(일부 클라우드 플랫폼에서는 리스너라고도 함)하려면 환경에 로드 밸런서를 생성해야 합니다. 다음은 청취해야 하는 권장 포트 표입니다.

| Service    | Protocol | Port  |
| ---------- | -------- | ----- |
| apiserver  | TCP      | 6443  |
| ke-console | TCP      | 30880 |
| http       | TCP      | 80    |
| https      | TCP      | 443   |

{{< notice note >}}

- 로드 밸런서가 최소한 apiserver 포트에서 수신 대기하는지 확인하십시오.

- 클러스터가 배포된 위치에 따라 외부 트래픽이 차단되지 않도록 보안 그룹에서 포트를 열어야 할 수도 있습니다. 자세한 내용은 [포트 요구 사항](../../../installing-on-linux/introduction/port-firewall/)을 참조하십시오.
- 일부 클라우드 플랫폼에서는 내부 및 외부 부하 분산 장치를 모두 구성할 수 있습니다. 외부 로드 밸런서에 공용 IP 주소를 할당한 후 해당 IP 주소를 사용하여 클러스터에 액세스할 수 있습니다.
- 로드 밸런서를 구성하는 방법에 대한 자세한 내용은 [공용 클라우드에 설치](../../../installing-on-linux/public-cloud/install-kubesphere-on-azure-vms/)를 참조하십시오. 주요 퍼블릭 클라우드 플랫폼에 대한 특정 단계를 참조하십시오.

{{</ notice >}} 

## KubePOP 다운로드

[KubePOP](https://github.com/ke/kubepop)는 쿠버네티스와 Kuberix Enterprise를 쉽고 빠르고 유연하게 설치할 수 있는 차세대 설치 프로그램입니다. 아래 단계에 따라 KubePOP를 다운로드하십시오.

{{< tabs >}}

{{< tab "Good network connections to GitHub/Googleapis" >}}

[GitHub 릴리스 페이지](https://github.com/ke/kubepop/releases)에서 KubePOP를 다운로드하거나 다음 명령을 직접 사용합니다.

```bash
curl -sfL https://get-kp.kuberix.io | VERSION=v2.2.2 sh -
```

{{</ tab >}}

{{< tab "Poor network connections to GitHub/Googleapis" >}}

다음 명령을 먼저 실행하여 올바른 영역에서 KubePOP를 다운로드했는지 확인하십시오.

```bash
export KPZONE=cn
```

다음 명령을 실행하여 KubePOP를 다운로드하십시오.:

```bash
curl -sfL https://get-kp.kuberix.io | VERSION=v2.2.2 sh -
```

{{< notice note >}}

KubePOP를 다운로드한 후 Googleapis에 대한 네트워크 연결이 좋지 않은 새 컴퓨터로 전송하는 경우 아래 단계를 진행하기 전에 `export KPZONE=cn`을 다시 실행해야 합니다.

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

기본 구성으로 예제 구성 파일을 만듭니다. 여기에서 쿠버네티스 v1.22.10이 예로 사용됩니다.

```bash
./kp create config --with-kuberixEnterprise v3.3.0 --with-kubernetes v1.22.10
```

{{< notice note >}}

- Kuberix Enterprise 3.3.0의 권장 쿠버네티스 버전: v1.19.x, v1.20.x, v1.21.x, v1.22.x 및 v1.23.x(실험 지원). 쿠버네티스 버전을 지정하지 않으면 Kubepop는 기본적으로 쿠버네티스 v1.23.7을 설치합니다. 지원되는 쿠버네티스 버전에 대한 자세한 내용은 [지원 매트릭스](../../../installing-on-linux/introduction/kubepop/#support-matrix)를 참조하십시오.

- 이 단계에서 명령에 `--with-KuberixEnterprise` 플래그를 추가하지 않으면 구성 파일의 `addons` 필드를 사용하여 설치하거나 `를 사용할 때 이 플래그를 다시 추가하지 않는 한 Kuberix Enterprise가 배포되지 않습니다. ./kp는 나중에 클러스터를 생성합니다.
- Kuberix Enterprise 버전을 지정하지 않고 `--with-KuberixEnterprise` 플래그를 추가하면 최신 버전의 Kuberix Enterprise가 설치됩니다.

{{</ notice >}}

## Kuberix Enterprise 및 쿠버네티스 배포

위의 명령어를 실행하면 'config-sample.yaml' 구성 파일이 생성됩니다. 파일을 편집하여 시스템 정보를 추가하고 로드 밸런서를 구성하는 등의 작업을 수행합니다.

{{< notice note >}}

사용자 정의하면 파일 이름이 다를 수 있습니다.

{{</ notice >}} 

### config-sample.yaml 예제

```yaml
spec:
  hosts:
  - {name: master1, address: 192.168.0.2, internalAddress: 192.168.0.2, user: ubuntu, password: Testing123}
  - {name: master2, address: 192.168.0.3, internalAddress: 192.168.0.3, user: ubuntu, password: Testing123}
  - {name: master3, address: 192.168.0.4, internalAddress: 192.168.0.4, user: ubuntu, password: Testing123}
  - {name: node1, address: 192.168.0.5, internalAddress: 192.168.0.5, user: ubuntu, password: Testing123}
  - {name: node2, address: 192.168.0.6, internalAddress: 192.168.0.6, user: ubuntu, password: Testing123}
  - {name: node3, address: 192.168.0.7, internalAddress: 192.168.0.7, user: ubuntu, password: Testing123}
  roleGroups:
    etcd:
    - master1
    - master2
    - master3
    control-plane:
    - master1
    - master2
    - master3
    worker:
    - node1
    - node2
    - node3
```

이 구성 파일의 다른 필드에 대한 자세한 내용은 [쿠버네티스 클러스터 구성](../../../installing-on-linux/introduction/vars/) 및 [멀티 노드 설치](../. ./../installing-on-linux/introduction/multioverview/#2-edit-the-configuration-file)을 참조하십시오.

### 로드 밸런서 구성

```yaml
spec:
  controlPlaneEndpoint:
    ##Internal loadbalancer for apiservers
    #internalLoadbalancer: haproxy
    
    domain: lb.kuberix.local
    address: "192.168.0.xx"
    port: 6443
```

{{< notice note >}}

- `config-sample.yaml`에서 주소와 포트는 두 칸 들여쓰기를 해야 합니다.
- 대부분의 경우 'address' 필드에 로드 밸런서의 **private IP address**를 제공해야 합니다. 그러나 클라우드 공급자마다 로드 밸런서에 대한 구성이 다를 수 있습니다. 
- 내부 접근을 위한 로드밸런서의 도메인 이름은 기본적으로 `lb.kuberix.local`이다.
- 내부 로드 밸런서를 사용하려면 'internalLoadbalancer' 필드의 주석 처리를 제거합니다.

{{</ notice >}}

### 영구 저장소 플러그인 구성

프로덕션 환경의 경우 영구 저장소를 준비하고 `config-sample.yaml`에서 저장소 플러그인(예: CSI)을 구성하여 사용할 저장소 서비스를 정의해야 합니다. 자세한 내용은 [영구 저장소 구성](../../../installing-on-linux/persistent-storage-configurations/understand-persistent-storage/)을 참조하십시오.

### 연결 가능한 구성 요소 활성화(선택 사항)

Kuberix Enterprise는 일부 핵심 기능 구성요소를 분리했습니다. 이러한 구성 요소는 플러그 가능하도록 설계되었으므로 설치 전이나 후에 활성화할 수 있습니다. 기본적으로 Kuberix Enterprise는 활성화하지 않으면 최소 패키지로 설치됩니다.

요구 사항에 따라 이들 중 하나를 활성화할 수 있습니다. Kuberix Enterprise에서 제공하는 전체 스택 기능을 발견하려면 이러한 플러그형 구성 요소를 설치하는 것이 좋습니다. 컴퓨터를 활성화하기 전에 컴퓨터에 충분한 CPU와 메모리가 있는지 확인하십시오. 자세한 내용은 [플러그 가능 구성 요소 활성화](../../../pluggable-components/)를 참조하십시오.

### 설치 시작

구성을 완료한 후 다음 명령을 실행하여 설치를 시작할 수 있습니다.:

```bash
./kp create cluster -f config-sample.yaml
```

### 설치 확인

1. 다음 명령어를 실행하여 설치 로그를 확인합니다.

   ```bash
   kubectl logs -n ke-system $(kubectl get pod -n ke-system -l 'app in (ke-install, ke-installer)' -o jsonpath='{.items[0].metadata.name}') -f
   ```

2. When you see the following message, it means your HA cluster is successfully created.

   ```bash
   #####################################################
   ###         Welcome to Kuberix Enterprise!        ###
   #####################################################
   
   Console: http://192.168.0.3:30880
   Account: admin
   Password: P@88w0rd
   
   NOTES：
     1. After you log into the console, please check the
        monitoring status of service components in
        the "Cluster Management". If any service is not
        ready, please wait patiently until all components
        are up and running.
     2. Please change the default password after login.
   
   #####################################################
   https://kuberix.io                2020-xx-xx xx:xx:xx
   #####################################################
   ```

