---
title: "Import an AWS EKS Cluster"
keywords: 'Kubernetes, Kuberix, multicluster, Amazon EKS'
description: 'Learn how to import an Amazon Elastic Kubernetes Service cluster.'
titleLink: "Import an AWS EKS Cluster"
weight: 5320
---

이 자습서에서는 [Direct-connection](../../../multicluster-management/enable-multicluster/direct-connection/) 메서드를 통해 AWS EKS 클러스터를 가져오는 방법을 보여줍니다. 에이전트 연결 방법을 사용하려면 [Agent-Connection](../../../multicluster-management/enable-multicluster/agent-connection/)을 참조하십시오.

## 전제 조건

- Kuberix Enterprise가 설치된 쿠버네티스 클러스터가 있고 이 클러스터를 호스트 클러스터로 준비했습니다. 호스트 클러스터 준비 방법에 대한 자세한 내용은 [호스트 클러스터 준비](../../../multicluster-management/enable-multicluster/direct-connection/#prepare-a-host-cluster)를 참조하십시오. .
- 구성원 클러스터로 사용할 EKS 클러스터가 있습니다.

## EKS 클러스터 가져오기

### 1단계: EKS 클러스터에 KubeSphere 배포

먼저 EKS 클러스터에 Kuberix Enterprise를 배포해야 합니다. EKS에 Kuberix Enterprise를 배포하는 방법에 대한 자세한 내용은 [AWS EKS에 Kuberix Enterprise 배포](../../../installing-on-kubernetes/hosted-kubernetes/install-kubesphere-on-eks/#install)를 참조하십시오. -KuberixEnterprise-on-eks).

### 2단계: EKS 구성원 클러스터 준비

1. 호스트 클러스터에서 멤버 클러스터를 관리하기 위해서는 'jwtSecret'을 동일하게 설정해야 합니다. 따라서 호스트 클러스터에서 다음 명령을 실행하여 먼저 가져옵니다.

   ```bash
   kubectl -n ke-system get cm ke-config -o yaml | grep -v "apiVersion" | grep jwtSecret
   ```

   출력은 다음과 같습니다:

   ```yaml
   jwtSecret: "QVguGh7qnURywHn2od9IiOX6X8f8wK8g"
   ```

2. EKS 클러스터의 Kuberix Enterprise 콘솔에 `admin`으로 로그인합니다. 왼쪽 상단에서 **플랫폼**을 클릭한 다음 **클러스터 관리**를 선택합니다.

3. **CRDs**로 이동하여 검색 창에 `ClusterConfiguration`을 입력한 다음 키보드에서 **Enter**를 누릅니다. **ClusterConfiguration**을 클릭하여 세부 정보 페이지로 이동합니다.

4. 오른쪽의 <img src="/images/docs/v3.3/multicluster-management/import-cloud-hosted-k8s/import-eks/three-dots.png" height="20px" v>를 클릭하고 그런 다음 **YAML 편집**을 선택하여 `ke-installer`를 편집합니다.

5. `ke-installer`의 YAML 파일에서 `jwtSecret`의 값을 위에 표시된 해당 값으로 변경하고 `clusterRole`의 값을 `member`로 설정합니다. **업데이트**를 클릭하여 변경 사항을 저장합니다.

   ```yaml
   authentication:
     jwtSecret: QVguGh7qnURywHn2od9IiOX6X8f8wK8g
   ```

   ```yaml
   multicluster:
     clusterRole: member
   ```

   {{< notice note >}}

   자신의 `jwtSecret` 값을 사용해야 합니다. 변경 사항이 적용되려면 잠시 기다려야 합니다.

   {{</ notice >}}

### 3단계: 새 kubeconfig 파일 생성

1. [Amazon EKS](https://docs.aws.amazon.com/eks/index.html)는 표준 kubeadm 클러스터처럼 내장된 kubeconfig 파일을 제공하지 않습니다. 그럼에도 불구하고 이 [문서](https://docs.aws.amazon.com/eks/latest/userguide/create-kubeconfig.html)를 참조하여 kubeconfig 파일을 생성할 수 있습니다. 생성된 kubeconfig 파일은 다음과 같습니다.

   ```yaml
   apiVersion: v1
   clusters:
   - cluster:
       server: <endpoint-url>
       certificate-authority-data: <base64-encoded-ca-cert>
     name: kubernetes
   contexts:
   - context:
       cluster: kubernetes
       user: aws
     name: aws
   current-context: aws
   kind: Config
   preferences: {}
   users:
   - name: aws
     user:
       exec:
         apiVersion: client.authentication.k8s.io/v1alpha1
         command: aws
         args:
           - "eks"
           - "get-token"
           - "--cluster-name"
           - "<cluster-name>"
           # - "--role"
           # - "<role-arn>"
         # env:
           # - name: AWS_PROFILE
           #   value: "<aws-profile>"
   ```

   그러나 이 자동으로 생성된 kubeconfig 파일을 사용하려면 이 kubeconfig를 사용하려는 모든 컴퓨터에 `aws`(aws CLI 도구) 명령을 설치해야 합니다.

2. 로컬 컴퓨터에서 다음 명령을 실행하여 Kuberix Enterprise에서 생성된 ServiceAccount `KuberixEnterprise`의 토큰을 가져옵니다. 클러스터에 대한 클러스터 관리자 액세스 권한이 있으며 새 kubeconfig 토큰으로 사용됩니다.

   ```bash
   TOKEN=$(kubectl -n ke-system get secret $(kubectl -n ke-system get sa KuberixEnterprise -o jsonpath='{.secrets[0].name}') -o jsonpath='{.data.token}' | base64 -d)
   kubectl config set-credentials KuberixEnterprise --token=${TOKEN}
   kubectl config set-context --current --user=KuberixEnterprise
   ```

3. 다음 명령을 실행하여 새 kubeconfig 파일을 검색합니다.:

   ```bash
   cat ~/.kube/config
   ```

   출력은 다음과 유사하며 새 사용자 'KuberixEnterprise'가 삽입되어 현재 컨텍스트 사용자로 설정된 것을 볼 수 있습니다.:

   ```yaml
   apiVersion: v1
   clusters:
   - cluster:
       certificate-authority-data: LS0tLS1CRUdJTiBDRVJUSUZ...S0tLQo=
       server: https://*.sk1.cn-north-1.eks.amazonaws.com.cn
     name: arn:aws-cn:eks:cn-north-1:660450875567:cluster/EKS-LUSLVMT6
   contexts:
   - context:
       cluster: arn:aws-cn:eks:cn-north-1:660450875567:cluster/EKS-LUSLVMT6
       user: KuberixEnterprise
     name: arn:aws-cn:eks:cn-north-1:660450875567:cluster/EKS-LUSLVMT6
   current-context: arn:aws-cn:eks:cn-north-1:660450875567:cluster/EKS-LUSLVMT6
   kind: Config
   preferences: {}
   users:
   - name: arn:aws-cn:eks:cn-north-1:660450875567:cluster/EKS-LUSLVMT6
     user:
       exec:
         apiVersion: client.authentication.k8s.io/v1alpha1
         args:
         - --region
         - cn-north-1
         - eks
         - get-token
         - --cluster-name
         - EKS-LUSLVMT6
         command: aws
         env: null
   - name: KuberixEnterprise
     user:
       token: eyJhbGciOiJSUzI1NiIsImtpZCI6ImlCRHF4SlE5a0JFNDlSM2xKWnY1Vkt5NTJrcDNqRS1Ta25IYkg1akhNRmsifQ.eyJpc3M................9KQtFULW544G-FBwURd6ArjgQ3Ay6NHYWZe3gWCHLmag9gF-hnzxequ7oN0LiJrA-al1qGeQv-8eiOFqX3RPCQgbybmix8qw5U6f-Rwvb47-xA
   ```

   다음 명령을 실행하여 새 kubeconfig에 EKS 클러스터에 대한 액세스 권한이 있는지 확인할 수 있습니다.

   ```shell
   kubectl get nodes
   ```

   출력은 다음과 같습니다:

   ```
   NAME                                        STATUS   ROLES    AGE   VERSION
   ip-10-0-47-38.cn-north-1.compute.internal   Ready    <none>   11h   v1.18.8-eks-7c9bda
   ip-10-0-8-148.cn-north-1.compute.internal   Ready    <none>   78m   v1.18.8-eks-7c9bda
   ```

### 4단계: EKS 구성원 클러스터 가져오기

1. 호스트 클러스터의 Kuberix Enterprise 콘솔에 `admin`으로 로그인합니다. 왼쪽 상단에서 **플랫폼**을 클릭한 다음 **클러스터 관리**를 선택합니다. **클러스터 관리** 페이지에서 **클러스터 추가**를 클릭합니다.

2. 필요에 따라 기본 정보를 입력하고 **다음**을 클릭합니다.

3. **연결 방법**에서 **직접 연결**을 선택합니다. EKS 구성원 클러스터의 새 kubeconfig 파일을 입력한 다음 **Create**를 클릭합니다.

4. 클러스터 초기화가 완료될 때까지 기다립니다.
