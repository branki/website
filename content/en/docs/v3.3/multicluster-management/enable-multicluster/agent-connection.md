---
title: "Agent Connection"
keywords: 'Kubernetes, Kuberix, multicluster, agent-connection'
description: 'Understand the general steps of importing clusters through agent connection.'
titleLink: "Agent Connection"
weight: 5220
---

Kuberix Enterprise의 컴포넌트 Tower는 에이전트 연결에 사용됩니다. Tower는 에이전트를 통해 클러스터 간의 네트워크 연결을 위한 도구입니다. 호스트 클러스터가 멤버 클러스터에 직접 액세스할 수 없는 경우 호스트 클러스터의 프록시 서비스 주소를 노출할 수 있습니다. 이렇게 하면 구성원 클러스터가 에이전트를 통해 호스트 클러스터에 연결할 수 있습니다. 이 방법은 구성원 클러스터가 개인 환경(예: IDC)에 있고 호스트 클러스터가 프록시 서비스를 노출할 수 있는 경우에 적용할 수 있습니다. 에이전트 연결은 클러스터가 여러 클라우드 공급자에 분산되어 있는 경우에도 적용할 수 있습니다.

에이전트를 사용하여 멀티 클러스터 기능을 사용하려면 각각 호스트 클러스터와 멤버 클러스터 역할을 하는 클러스터가 두 개 이상 있어야 합니다. 클러스터는 Kuberix Enterprise를 설치하기 전이나 설치한 후에 호스트 클러스터 또는 구성원 클러스터로 정의할 수 있습니다. KubeSphere 설치에 대한 자세한 내용은 [Linux에 설치](../../../installing-on-linux/) 및 [쿠버네티스에 설치](../../../installing-on)를 참조하십시오. -쿠버네티스/)를 참조하십시오.

## 호스트 클러스터 준비

호스트 클러스터는 중앙 제어 평면을 제공하며 호스트 클러스터는 하나만 정의할 수 있습니다.

{{< tabs >}}

{{< tab "Kuberix Enterprise has been installed" >}}

독립 실행형 Kuberix Enterprise 클러스터가 이미 설치된 경우 클러스터 구성을 편집하여 `clusterRole` 값을 `host`로 설정할 수 있습니다.

- Option A - Use the web console:

  `admin` 계정을 사용하여 콘솔에 로그인하고 **클러스터 관리** 페이지에서 **CRD**로 이동합니다. 키워드 `ClusterConfiguration`을 입력하고 세부 정보 페이지로 이동합니다. [Enable Pluggable Components](../../../pluggable-components/)와 유사한 `ke-installer`의 YAML을 편집합니다.

- Option B - Use Kubectl:

  ```shell
  kubectl edit cc ke-installer -n ke-system
  ```

`ke-installer`의 YAML 파일에서 `multicluster`로 이동하고 `clusterRole` 값을 `host`로 설정한 다음 **OK**(웹 콘솔을 사용하는 경우)를 클릭하여 적용합니다.

```yaml
multicluster:
  clusterRole: host
```

호스트 클러스터 이름을 설정하려면 `ke-installer`의 YAML 파일에서 `multicluster.clusterRole` 아래에 `hostClusterName` 필드를 추가합니다. :

```yaml
multicluster:
  clusterRole: host
  hostClusterName: <Host cluster name>
```

{{< notice note >}}

- 호스트 클러스터를 준비하는 동안 호스트 클러스터 이름을 설정하는 것이 좋습니다. 호스트 클러스터가 설정되고 리소스가 배포된 상태에서 실행 중인 경우 호스트 클러스터 이름을 설정하지 않는 것이 좋습니다.
- 호스트 클러스터 이름은 소문자, 숫자, 하이픈(-) 또는 마침표(.)만 포함할 수 있으며 소문자 또는 숫자로 시작하고 끝나야 합니다.

{{</ notice >}}

변경 사항이 적용되려면 잠시 기다려야 합니다.

{{</ tab >}}

{{< tab "Kuberix Enteprise has not been installed" >}}

Linux 또는 기존 쿠버네티스 클러스터에 Kuberix Enterprise를 설치하기 전에 호스트 클러스터를 정의할 수 있습니다. [Linux에 Kuberix Enterprise 설치](../../../installing-on-linux/introduction/multioverview/#1-create-an-example-configuration-file)하려면 `config- sample.yaml` 파일. [기존 쿠버네티스 클러스터에 Kuberix Enterprise 설치](../../../installing-on-kubernetes/introduction/overview/#deploy-kubesphere)하려면 두 개의 YAML 파일을 사용하며 그 중 하나는 ` 클러스터 구성.yaml` 입니다.

호스트 클러스터를 설정하려면 Kuberix Enterprise를 설치하기 전에 `config-sample.yaml` 또는 `cluster-configuration.yaml`에서 `clusterRole`의 값을 `host`로 변경하십시오.

```yaml
multicluster:
  clusterRole: host
```

호스트 클러스터 이름을 설정하려면 'config-sample.yaml' 또는 'cluster-configuration.yaml'의 'multicluster.clusterRole' 아래에 'hostClusterName' 필드를 추가하세요.:

```yaml
multicluster:
  clusterRole: host
  hostClusterName: <Host cluster name>
```

{{< notice note >}}

- 호스트 클러스터 이름은 소문자, 숫자, 하이픈(-) 또는 마침표(.)만 포함할 수 있으며 소문자 또는 숫자로 시작하고 끝나야 합니다.

{{</ notice >}}

{{< notice info >}}

단일 노드 클러스터([All-in-One](../../../quick-start/all-in-one-on-linux/))에 Kuberix Enterprise를 설치하는 경우 다음을 수행할 필요가 없습니다. `config-sample.yaml` 파일을 만듭니다. 이 경우 Kuberix Enterprise 설치 후 호스트 클러스터를 설정할 수 있다.

{{</ notice >}} 

{{</ tab >}}

{{</ tabs >}}

**kubectl**을 사용하여 다음 명령을 실행하여 설치 로그를 검색하여 상태를 확인할 수 있습니다. 잠시 기다리면 호스트 클러스터가 준비되면 성공적인 로그 반환을 볼 수 있습니다.

```bash
kubectl logs -n ke-system $(kubectl get pod -n ke-system -l 'app in (ke-install, ke-installer)' -o jsonpath='{.items[0].metadata.name}') -f
```

## 프록시 서비스 주소 설정

호스트 클러스터를 설치한 후 'tower'라는 프록시 서비스가 'LoadBalancer' 유형인 'ke-system'에 생성됩니다.

{{< tabs >}}

{{< tab "A LoadBalancer available in your cluster" >}}

클러스터에 대해 LoadBalancer 플러그인을 사용할 수 있는 경우 Kuberix Enterprise에 등록할 타워의 'EXTERNAL-IP'에 해당하는 주소를 볼 수 있습니다. 이 경우 프록시 서비스가 자동으로 설정됩니다. 즉, 프록시를 설정하는 단계를 건너뛸 수 있습니다. 다음 명령을 실행하여 LoadBalancer가 있는지 확인하십시오.

```bash
kubectl -n ke-system get svc
```

출력은 아래와 같습니다.:

```shell
NAME       TYPE            CLUSTER-IP      EXTERNAL-IP     PORT(S)              AGE
tower      LoadBalancer    10.233.63.191   139.198.110.23  8080:30721/TCP       16h
```

{{< notice note >}}

일반적으로 퍼블릭 클라우드에는 항상 LoadBalancer 솔루션이 존재하며, 외부 IP는 로드밸런서에 의해 자동으로 할당될 수 있습니다. 클러스터가 온프레미스 환경, 특히 **베어메탈 환경**에서 실행되는 경우 OpenELB를 LB 솔루션으로 사용할 수 있습니다.

{{</ notice >}}

{{</ tab >}}

{{< tab "No LoadBalancer available in your cluster" >}}

    ```
1. 다음 명령을 실행하여 서비스를 확인하십시오.:

    ```shell
    kubectl -n ke-system get svc
    ```

    In this sample, `NodePort` is `30721`.
    ```
    NAME       TYPE            CLUSTER-IP      EXTERNAL-IP     PORT(S)              AGE
    tower      LoadBalancer    10.233.63.191   <pending>  8080:30721/TCP            16h
    ```

2. 'EXTERNAL-IP'가 'pending'인 경우 프록시 주소를 수동으로 설정해야 합니다. 예를 들어 공인 IP 주소가 '139.198.120.120'인 경우 이 공인 IP 주소의 포트(예: '8080')를 <NodeIP>:<NodePort>에 노출해야 합니다.

3. `ke-installer`의 구성 파일에 `proxyPublishAddress` 값을 추가하고 다음과 같이 공용 IP 주소(이 튜토리얼에서는 `139.198.120.120`)와 포트 번호를 제공합니다.

    - Option A - Use the web console:

      `admin` 계정을 사용하여 콘솔에 로그인하고 **클러스터 관리** 페이지에서 **CRD**로 이동합니다. 키워드 `ClusterConfiguration`을 입력하고 세부 정보 페이지로 이동합니다. [Enable Pluggable Components](../../../pluggable-components/)와 유사한 `ke-installer`의 YAML을 편집합니다.

    - Option B - Use Kubectl:

      ```bash
      kubectl -n ke-system edit clusterconfiguration ke-installer
      ```

    Navigate to `multicluster` and add a new line for `proxyPublishAddress` to define the IP address to access tower.

    ```yaml
    multicluster:
        clusterRole: host
        proxyPublishAddress: http://139.198.120.120:8080 # Add this line to set the address to access tower
    ```

4. 구성을 저장하고 잠시 기다리거나 다음 명령을 사용하여 수동으로 `ke-apiserver`를 다시 시작하여 변경 사항을 즉시 적용할 수 있습니다.

    ```shell
    kubectl -n ke-system rollout restart deployment ke-apiserver
    ```

{{</ tab >}}

{{</ tabs >}}

## 멤버 클러스터 준비

**호스트 클러스터**에서 멤버 클러스터를 관리하기 위해서는 `jwtSecret`을 동일하게 설정해야 합니다. 따라서 **호스트 클러스터**에서 다음 명령을 실행하여 먼저 가져옵니다.

```bash
kubectl -n ke-system get cm ke-config -o yaml | grep -v "apiVersion" | grep jwtSecret
```

출력은 다음과 같습니다.:

```yaml
jwtSecret: "gfIwilcc0WjNGKJ5DLeksf2JKfcLgTZU"
```

{{< tabs >}}

{{< tab "Kuberix Enterprise has been installed" >}}

독립형 Kuberix Enterprise 클러스터가 이미 설치된 경우 클러스터 구성을 편집하여 `clusterRole` 값을 `member`로 설정할 수 있습니다.

- Option A - Use the web console:

  `admin` 계정을 사용하여 콘솔에 로그인하고 **클러스터 관리** 페이지에서 **CRD**로 이동합니다. 키워드 `ClusterConfiguration`을 입력하고 세부 정보 페이지로 이동합니다. [Enable Pluggable Components](../../../pluggable-components/)와 유사한 `ke-installer`의 YAML을 편집합니다.

- Option B - Use Kubectl:

  ```shell
  kubectl edit cc ke-installer -n ke-system
  ```

`ke-installer`의 YAML 파일에서 위에 표시된 해당 `jwtSecret`을 입력합니다.:

```yaml
authentication:
  jwtSecret: gfIwilcc0WjNGKJ5DLeksf2JKfcLgTZU
```

아래로 스크롤하여 `clusterRole` 값을 `member`로 설정한 다음 **확인**(웹 콘솔을 사용하는 경우)을 클릭하여 적용합니다.:

```yaml
multicluster:
  clusterRole: member
```

변경 사항이 적용되려면 잠시 기다려야 합니다.

{{</ tab >}}

{{< tab "Kuberix Enterprise has not been installed" >}}

Linux 또는 기존 쿠버네티스 클러스터에 Kuberix Enterprise를 설치하기 전에 구성원 클러스터를 정의할 수 있습니다. [Linux에 Kuberix Enterprise 설치](../../../installing-on-linux/introduction/multioverview/#1-create-an-example-configuration-file)하려면 `config- sample.yaml` 파일. [기존 쿠버네티스 클러스터에 Kuberix Enterprise 설치](../../../installing-on-kubernetes/introduction/overview/#deploy-kubesphere)하려면 두 개의 YAML 파일을 사용하며 그 중 하나는 ` 클러스터 구성.yaml`. 멤버 클러스터를 설정하려면 위의 `jwtSecret` 값을 입력하고 Kuberix Enterprise를 설치하기 전에 `config-sample.yaml` 또는 `cluster-configuration.yaml`에서 `clusterRole` 값을 `member`로 적절히 변경합니다.

```yaml
authentication:
  jwtSecret: gfIwilcc0WjNGKJ5DLeksf2JKfcLgTZU
```

```yaml
multicluster:
  clusterRole: member
```

{{< notice note >}}

단일 노드 클러스터([All-in-One](../../../quick-start/all-in-one-on-linux/))에 Kuberix Enterprise를 설치하는 경우 다음을 수행할 필요가 없습니다. `config-sample.yaml` 파일을 만듭니다. 이 경우 Kuberix Enterprise 설치 후 멤버 클러스터를 설정할 수 있다.

{{</ notice >}} 

{{</ tab >}}

{{</ tabs >}}

**kubectl**을 사용하여 다음 명령을 실행하여 설치 로그를 검색하여 상태를 확인할 수 있습니다. 잠시 기다리면 구성원 클러스터가 준비되면 성공적인 로그 반환을 볼 수 있습니다.

```bash
kubectl logs -n ke-system $(kubectl get pod -n ke-system -l 'app in (ke-install, ke-installer)' -o jsonpath='{.items[0].metadata.name}') -f
```

## 멤버 클러스터 가져오기

1. Kuberix Enterprise 콘솔에 `admin`으로 로그인하고 **클러스터 관리** 페이지에서 **클러스터 추가**를 클릭합니다.

2. **Import Cluster** 페이지에서 가져올 클러스터의 기본 정보를 입력합니다. 또한 오른쪽 상단의 **편집 모드**를 클릭하여 YAML 형식의 기본 정보를 보고 편집할 수도 있습니다. 편집을 마친 후 **다음**을 클릭합니다.

3. **연결 방법**에서 **에이전트 연결**을 선택하고 **만들기**를 클릭합니다. 호스트 클러스터에서 생성한 에이전트 배포에 대한 YAML 구성 파일이 콘솔에 표시됩니다.

4. 지침에 따라 구성원 클러스터에 `agent.yaml` 파일을 만든 다음 에이전트 배포를 파일에 복사하여 붙여넣습니다. 노드에서 `kubectl create -f agent.yaml`을 실행하고 에이전트가 실행될 때까지 기다립니다. 구성원 클러스터에서 프록시 주소에 액세스할 수 있는지 확인하십시오.

5. 클러스터 에이전트가 실행 중일 때 호스트 클러스터에서 가져온 클러스터를 볼 수 있습니다.
