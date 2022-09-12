---
title: "Air-gapped Installation"
keywords: 'Air-gapped, installation, Kuberix, KubePOP'
description: 'Understand how to install Kuberix Enterprise and Kubernetes in the air-gapped environment.'

linkTitle: "Air-gapped Installation"
weight: 3140
---

KubePOP을 사용하면 쿠버네티스/K3s만(쿠버네티스/K3s 및 Kuberix Enterprise 둘 다) 및 기타 클라우드 네이티브 플러그인을 유연하고 빠르고 편리한 방식으로 설치할 수 있습니다. 또한 클러스터를 확장하고 업그레이드하는 데 효과적인 도구입니다.

KubePOP v2.1.0에서는 쿠버네티스 클러스터의 에어갭 설치를 위한 솔루션을 제공하는 매니페스트 및 아티팩트의 개념을 도입했습니다. 매니페스트 파일은 현재 쿠버네티스 클러스터의 정보를 설명하고 아티팩트의 콘텐츠를 정의합니다. 이전에는 배포할 쿠버네티스 버전과 이미지가 다르기 때문에 배포 도구, 이미지(.tar) 파일 및 기타 바이너리를 사용자가 준비해야 했습니다. 이제 KubePOP를 사용하면 에어 갭 설치가 그 어느 때보다 쉬워집니다. 매니페스트 파일을 사용하여 에어 갭 환경에서 클러스터에 필요한 것을 정의한 다음, 아티팩트 파일을 내보내 이미지 레지스트리와 쿠버네티스 클러스터를 빠르고 쉽게 배포할 수 있습니다.

## 전제 조건

|Host IP| Host Name | Usage      |
| ---------------- | ---- | ---------------- |
|192.168.0.2 | node1    | 쿠버네티스 v1.22.10 및 Kuberix Enterprise v3.3.0이 설치된 소스 클러스터 패키징을 위한 온라인 호스트 |
|192.168.0.3 | node2    | 에어 갭 환경의 컨트롤 패널 노드 |
|192.168.0.4 | node3    | 에어갭 환경의 이미지 레지스트리 노드 |

## 설치 준비

1. 다음 명령을 실행하여 KubePOP v2.2.2를 다운로드합니다.

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
   {{</ tab >}}

   {{</ tabs >}}

2. 소스 클러스터에서 KubePOP를 사용하여 매니페스트를 생성합니다. 다음 두 가지 방법이 지원됩니다.

   - (권장) 생성된 클러스터에서 다음 명령어를 실행하여 매니페스트 파일을 생성합니다.:

   ```bash
   ./kp create manifest
   ```

   - 템플릿에 따라 수동으로 매니페스트 파일을 생성하고 컴파일합니다. 자세한 내용은 [ manifest-example ](https://github.com/ke/kubepop/blob/master/docs/manifest-example.md)을 참조하세요.

3. 다음 명령을 실행하여 소스 클러스터에서 매니페스트 구성을 수정합니다.
   
   ```bash
   vim manifest.yaml
   ```
   
   ```yaml
   ---
   apiVersion: kubepop.kuberix.io/v1alpha2
   kind: Manifest
   metadata:
     name: sample
   spec:
     arches:
     - amd64
     operatingSystems:
     - arch: amd64
       type: linux
       id: centos
       version: "7"
       repository:
         iso:
           localPath:
           url: https://github.com/ke/kubepop/releases/download/v2.2.2/centos7-rpms-amd64.iso
     - arch: amd64
       type: linux
       id: ubuntu
       version: "20.04"
       repository:
         iso:
           localPath:
           url: https://github.com/ke/kubepop/releases/download/v2.2.2/ubuntu-20.04-debs-amd64.iso
     kubernetesDistributions:
     - type: kubernetes
       version: v1.22.10
     components:
       helm:
         version: v3.6.3
       cni:
         version: v0.9.1
       etcd:
         version: v3.4.13
      ## For now, if your cluster container runtime is containerd, KubePOP will add a docker 20.10.8 container runtime in the below list.
      ## The reason is KubePOP creates a cluster with containerd by installing a docker first and making kubelet connect the socket file of containerd which docker contained.
       containerRuntimes:
       - type: docker
         version: 20.10.8
       crictl:
         version: v1.22.0
       docker-registry:
         version: "2"
       harbor:
         version: v2.4.1
       docker-compose:
         version: v2.2.2
     images:
     - docker.io/ke/kube-apiserver:v1.22.10
     - docker.io/ke/kube-controller-manager:v1.22.10
     - docker.io/ke/kube-proxy:v1.22.10
     - docker.io/ke/kube-scheduler:v1.22.10
     - docker.io/ke/pause:3.5
     - docker.io/coredns/coredns:1.8.0
     - docker.io/calico/cni:v3.23.2
     - docker.io/calico/kube-controllers:v3.23.2
     - docker.io/calico/node:v3.23.2
     - docker.io/calico/pod2daemon-flexvol:v3.23.2
     - docker.io/calico/typha:v3.23.2
     - docker.io/kubesphere/flannel:v0.12.0
     - docker.io/openebs/provisioner-localpv:3.3.0
     - docker.io/openebs/linux-utils:3.3.0
     - docker.io/library/haproxy:2.3
     - docker.io/ke/nfs-subdir-external-provisioner:v4.0.2
     - docker.io/ke/k8s-dns-node-cache:1.15.12
     - docker.io/ke/ke-installer:v3.3.0
     - docker.io/ke/ke-apiserver:v3.3.0
     - docker.io/ke/ke-console:v3.3.0
     - docker.io/ke/ke-controller-manager:v3.3.0
     - docker.io/ke/kubectl:v1.20.0
     - docker.io/ke/kubectl:v1.21.0
     - docker.io/ke/kubectl:v1.22.0
     - docker.io/ke/kubefed:v0.8.1
     - docker.io/ke/tower:v0.2.0
     - docker.io/minio/minio:RELEASE.2019-08-07T01-59-21Z
     - docker.io/minio/mc:RELEASE.2019-08-07T23-14-43Z
     - docker.io/csiplugin/snapshot-controller:v4.0.0
     - docker.io/ke/nginx-ingress-controller:v1.1.0
     - docker.io/mirrorgooglecontainers/defaultbackend-amd64:1.4
     - docker.io/ke/metrics-server:v0.4.2
     - docker.io/library/redis:5.0.14-alpine
     - docker.io/library/haproxy:2.0.25-alpine
     - docker.io/library/alpine:3.14
     - docker.io/osixia/openldap:1.3.0
     - docker.io/ke/netshoot:v1.0
     - docker.io/kubeedge/cloudcore:v1.9.2
     - docker.io/kubeedge/iptables-manager:v1.9.2
     - docker.io/ke/edgeservice:v0.2.0
     - docker.io/ke/openpitrix-jobs:v3.2.1
     - docker.io/ke/devops-apiserver:v3.3.0
     - docker.io/ke/devops-controller:v3.3.0
     - docker.io/ke/devops-tools:v3.3.0
     - docker.io/ke/ke-jenkins:v3.3.0-2.319.1
     - docker.io/jenkins/inbound-agent:4.10-2
     - docker.io/ke/builder-base:v3.2.2
     - docker.io/ke/builder-nodejs:v3.2.0
     - docker.io/ke/builder-maven:v3.2.0
     - docker.io/ke/builder-maven:v3.2.1-jdk11
     - docker.io/ke/builder-python:v3.2.0
     - docker.io/ke/builder-go:v3.2.0
     - docker.io/ke/builder-go:v3.2.2-1.16
     - docker.io/ke/builder-go:v3.2.2-1.17
     - docker.io/ke/builder-go:v3.2.2-1.18
     - docker.io/ke/builder-base:v3.2.2-podman
     - docker.io/ke/builder-nodejs:v3.2.0-podman
     - docker.io/ke/builder-maven:v3.2.0-podman
     - docker.io/ke/builder-maven:v3.2.1-jdk11-podman
     - docker.io/ke/builder-python:v3.2.0-podman
     - docker.io/ke/builder-go:v3.2.0-podman
     - docker.io/ke/builder-go:v3.2.2-1.16-podman
     - docker.io/ke/builder-go:v3.2.2-1.17-podman
     - docker.io/ke/builder-go:v3.2.2-1.18-podman
     - docker.io/ke/s2ioperator:v3.2.1
     - docker.io/ke/s2irun:v3.2.0
     - docker.io/ke/s2i-binary:v3.2.0
     - docker.io/ke/tomcat85-java11-centos7:v3.2.0
     - docker.io/ke/tomcat85-java11-runtime:v3.2.0
     - docker.io/ke/tomcat85-java8-centos7:v3.2.0
     - docker.io/ke/tomcat85-java8-runtime:v3.2.0
     - docker.io/ke/java-11-centos7:v3.2.0
     - docker.io/ke/java-8-centos7:v3.2.0
     - docker.io/ke/java-8-runtime:v3.2.0
     - docker.io/ke/java-11-runtime:v3.2.0
     - docker.io/ke/nodejs-8-centos7:v3.2.0
     - docker.io/ke/nodejs-6-centos7:v3.2.0
     - docker.io/ke/nodejs-4-centos7:v3.2.0
     - docker.io/ke/python-36-centos7:v3.2.0
     - docker.io/ke/python-35-centos7:v3.2.0
     - docker.io/ke/python-34-centos7:v3.2.0
     - docker.io/ke/python-27-centos7:v3.2.0
     - quay.io/argoproj/argocd:v2.3.3
     - quay.io/argoproj/argocd-applicationset:v0.4.1
     - ghcr.io/dexidp/dex:v2.30.2
     - docker.io/library/redis:6.2.6-alpine
     - docker.io/jimmidyson/configmap-reload:v0.5.0
     - docker.io/prom/prometheus:v2.34.0
     - docker.io/ke/prometheus-config-reloader:v0.55.1
     - docker.io/ke/prometheus-operator:v0.55.1
     - docker.io/ke/kube-rbac-proxy:v0.11.0
     - docker.io/ke/kube-state-metrics:v2.3.0
     - docker.io/prom/node-exporter:v1.3.1
     - docker.io/prom/alertmanager:v0.23.0
     - docker.io/thanosio/thanos:v0.25.2
     - docker.io/grafana/grafana:8.3.3
     - docker.io/ke/kube-rbac-proxy:v0.8.0
     - docker.io/ke/notification-manager-operator:v1.4.0
     - docker.io/ke/notification-manager:v1.4.0
     - docker.io/ke/notification-tenant-sidecar:v3.2.0
     - docker.io/ke/elasticsearch-curator:v5.7.6
     - docker.io/ke/elasticsearch-oss:6.8.22
     - docker.io/ke/fluentbit-operator:v0.13.0
     - docker.io/library/docker:19.03
     - docker.io/ke/fluent-bit:v1.8.11
     - docker.io/ke/log-sidecar-injector:1.1
     - docker.io/elastic/filebeat:6.7.0
     - docker.io/ke/kube-events-operator:v0.4.0
     - docker.io/ke/kube-events-exporter:v0.4.0
     - docker.io/ke/kube-events-ruler:v0.4.0
     - docker.io/ke/kube-auditing-operator:v0.2.0
     - docker.io/ke/kube-auditing-webhook:v0.2.0
     - docker.io/istio/pilot:1.11.1
     - docker.io/istio/proxyv2:1.11.1
     - docker.io/jaegertracing/jaeger-operator:1.27
     - docker.io/jaegertracing/jaeger-agent:1.27
     - docker.io/jaegertracing/jaeger-collector:1.27
     - docker.io/jaegertracing/jaeger-query:1.27
     - docker.io/jaegertracing/jaeger-es-index-cleaner:1.27
     - docker.io/ke/kiali-operator:v1.38.1
     - docker.io/ke/kiali:v1.38
     - docker.io/library/busybox:1.31.1
     - docker.io/library/nginx:1.14-alpine
     - docker.io/joosthofman/wget:1.0
     - docker.io/nginxdemos/hello:plain-text
     - docker.io/library/wordpress:4.8-apache
     - docker.io/mirrorgooglecontainers/hpa-example:latest
     - docker.io/library/java:openjdk-8-jre-alpine
     - docker.io/fluent/fluentd:v1.4.2-2.0
     - docker.io/library/perl:latest
     - docker.io/ke/examples-bookinfo-productpage-v1:1.16.2
     - docker.io/ke/examples-bookinfo-reviews-v1:1.16.2
     - docker.io/ke/examples-bookinfo-reviews-v2:1.16.2
     - docker.io/ke/examples-bookinfo-details-v1:1.16.2
     - docker.io/ke/examples-bookinfo-ratings-v1:1.16.3
     - docker.io/weaveworks/scope:1.13.0
   ```
   
   {{< notice note >}}

   - 내보낼 아티팩트 파일에 conntarck, chrony 등의 ISO 종속성이 포함된 경우 **operationSystem**의 **repostiory.iso.url**에서 ISO 종속성을 다운로드할 IP 주소를 설정합니다. 또는 미리 ISO 패키지를 다운로드하여 **localPath**에 로컬 경로를 입력하고 `url` 구성 항목을 삭제할 수 있습니다.
   
   - KubePOP를 사용하여 이미지 푸시를 위한 Harbor 레지스트리를 구축할 때 사용할 **harbor** 및 **docker-compose** 구성 항목을 활성화해야 합니다.
   
   - 기본적으로 생성된 매니페스트의 이미지 목록은 **docker.io**에서 가져옵니다.
   
   - **manifest-sample.yaml** 파일을 사용자 지정하여 원하는 아티팩트 파일을 내보낼 수 있습니다.

   - ISO 파일은 https://github.com/ke/kubepop/releases/tag/v2.2.2에서 다운로드할 수 있습니다.
   
   {{</ notice >}}
   
4. 소스 클러스터에서 아티팩트를 내보냅니다.

   {{< tabs >}}

   {{< tab "Good network connections to GitHub/Googleapis" >}}

   다음 명령을 직접 실행합니다.:

   ```bash
   ./kp artifact export -m manifest-sample.yaml -o kuberixEnterprise.tar.gz
   ```

   {{</ tab >}}

   {{< tab "Poor network connections to GitHub/Googleapis" >}}

   다음 명령을 실행합니다.:

   ```bash
   export KPZONE=cn
   
   ./kp artifact export -m manifest-sample.yaml -o kuberixEnterprise.tar.gz
   ```
   {{</ tab >}}

   {{</ tabs >}}

   {{< notice note >}}

   아티팩트는 지정된 매니페스트 파일에서 내보낸 이미지 패키지(.tar) 및 관련 바이너리를 포함하는 .tgz 패키지입니다. KubePOP 명령에서 이미지 레지스트리 초기화, 클러스터 생성, 노드 추가 및 클러스터 업그레이드를 위해 아티팩트를 지정할 수 있으며, 그러면 KubePOP는 명령을 실행할 때 자동으로 아티팩트의 압축을 풀고 압축이 풀린 파일을 사용합니다.

   - 네트워크 연결이 작동하는지 확인하십시오.

   - KubePOP는 이미지 목록에서 이미지 이름을 확인합니다. 이미지 레지스트리에 인증이 필요한 경우 매니페스트 파일의 **.registry.auths**에서 구성할 수 있습니다.
   
   {{</ notice >}}
   
## 에어 갭 환경에 클러스터 설치

1. USB 장치를 사용하여 에어 갭 환경의 노드에 다운로드한 KubePOP 아티팩트를 복사합니다.

2. 다음 명령을 실행하여 air-gapped 클러스터에 대한 구성 파일을 생성합니다.:

   ```bash
   ./kp create config --with-kuberixEnterprise v3.3.0 --with-kubernetes v1.22.10 -f config-sample.yaml
   ```

3. 다음 명령을 실행하여 구성 파일을 수정합니다.:

   ```bash
   vim config-sample.yaml
   ```

   {{< notice note >}}

   - 에어갭 환경의 실제 구성에 따라 노드 정보를 수정한다.
   - 배포할 '레지스트리'가 있는 노드를 지정해야 합니다(자체 구축 Harbour 레지스트리의 KubePOP 배포의 경우).
   - `registry`에서 `type`의 값은 `harbor`의 값으로 지정되어야 합니다. 그렇지 않으면 기본적으로 도커 레지스트리가 설치됩니다.
   
   {{</ notice >}}

   ```yaml
   apiVersion: kubepop.kuberix.io/v1alpha2
   kind: Cluster
   metadata:
     name: sample
   spec:
     hosts:
     - {name: master, address: 192.168.0.3, internalAddress: 192.168.0.3, user: root, password: "<REPLACE_WITH_YOUR_ACTUAL_PASSWORD>"}
     - {name: node1, address: 192.168.0.4, internalAddress: 192.168.0.4, user: root, password: "<REPLACE_WITH_YOUR_ACTUAL_PASSWORD>"}
   
     roleGroups:
       etcd:
       - master
       control-plane:
       - master
       worker:
       - node1
       # If you want to use KubePOP to automatically deploy the image registry, set this value. You are advised to separately deploy the registry and the cluster.
       registry:
       - node1
     controlPlaneEndpoint:
       ## Internal loadbalancer for apiservers
       # internalLoadbalancer: haproxy
   
       domain: lb.kuberix.local
       address: ""
       port: 6443
     kubernetes:
       version: v1.22.10
       clusterName: cluster.local
     network:
       plugin: calico
       kubePodsCIDR: 10.233.64.0/18
       kubeServiceCIDR: 10.233.0.0/18
       ## multus support. https://github.com/k8snetworkplumbingwg/multus-cni
       multusCNI:
         enabled: false
     registry:
       # To use KubePOP to deploy Harbor, set the value of this parameter to harbor. If you do not set this parameter and still use KubePOP to create an container image registry, the docker registry is used by default.
       type: harbor
       # If Harbor or other registries deployed by using KubePOP requires login, you can set the auths parameter of the registry. However, if you create a docker registry using KubePOP, you do not need to set the auths parameter.
       # Note: If you use KubePOP to deploy Harbor, do not set this parameter until Harbor is started.
       #auths:
       #  "dockerhub.kubepop.local":
       #    username: admin
       #    password: Harbor12345
       # Set the private registry to use during cluster deployment.
       privateRegistry: ""
       namespaceOverride: ""
       registryMirrors: []
       insecureRegistries: []
     addons: []
   ```


4. 다음 명령을 실행하여 이미지 레지스트리를 설치합니다.:

   ```bash
   ./kp init registry -f config-sample.yaml -a kuberixEnterprise.tar.gz
   ```
   {{< notice note >}}

   명령의 매개변수는 다음과 같이 설명됩니다.

    - **config-sample.yaml**: air-gapped 환경에서 클러스터의 구성 파일을 지정합니다.

    - **kuberixEnterprise.tar.gz**: 소스 클러스터의 이미지 패키지를 지정한다.

    {{</ notice >}}

5. 하버 프로젝트를 생성합니다.
   
    {{< notice note >}}

    Harbor는 RBAC(역할 기반 액세스 제어) 메커니즘을 채택하므로 지정된 사용자만 특정 작업을 수행할 수 있습니다. 따라서 Harbour에 이미지를 푸시하기 전에 프로젝트를 생성해야 합니다. Harbor는 두 가지 유형의 프로젝트를 지원합니다.

    - **공개**: 모든 사용자가 프로젝트에서 이미지를 가져올 수 있습니다.

    - **비공개**: 프로젝트 구성원만 프로젝트에서 이미지를 가져올 수 있습니다.

    Harbor 로그인을 위한 사용자 이름과 비밀번호는 기본적으로 **admin** 및 **Harbor12345**입니다. Harbor의 설치 파일은 **/opt/harbor**에 있으며, 여기서 Harbour의 O&M을 수행할 수 있습니다.
  
    {{</ notice >}}

   방법 1: 다음 명령을 실행하여 하버 프로젝트를 생성합니다.

   a. 다음 명령을 실행하여 지정된 스크립트를 다운로드하여 Harbour 레지스트리를 초기화합니다.:

      ```bash
      curl -O https://raw.githubusercontent.com/ke/ke-installer/master/scripts/create_project_harbor.sh
      ```

   b. 다음 명령을 실행하여 스크립트 구성 파일을 수정하십시오.:

      ```bash
      vim create_project_harbor.sh
      ```

      ```yaml
      #!/usr/bin/env bash
      
      # Copyright 2018 The Kuberix Enterprise Authors.
      #
      # Licensed under the Apache License, Version 2.0 (the "License");
      # you may not use this file except in compliance with the License.
      # You may obtain a copy of the License at
      #
      #     http://www.apache.org/licenses/LICENSE-2.0
      #
      # Unless required by applicable law or agreed to in writing, software
      # distributed under the License is distributed on an "AS IS" BASIS,
      # WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
      # See the License for the specific language governing permissions and
      # limitations under the License.
      
      url="https://dockerhub.kubepop.local"  #Change the value of url to https://dockerhub.kubepop.local.
      user="admin"
      passwd="Harbor12345"
      
      harbor_projects=(library
          kuberixEnterpriseio
          kuberixEnterprise
          calico
          coredns
          openebs
          csiplugin
          minio
          mirrorgooglecontainers
          osixia
          prom
          thanosio
          jimmidyson
          grafana
          elastic
          istio
          jaegertracing
          jenkins
          weaveworks
          openpitrix
          joosthofman
          nginxdemos
          fluent
          kubeedge
      )
      
      for project in "${harbor_projects[@]}"; do
          echo "creating $project"
          curl -u "${user}:${passwd}" -X POST -H "Content-Type: application/json" "${url}/api/v2.0/projects" -d "{ \"project_name\": \"${project}\", \"public\": true}" -k #Add -k at the end of the curl command.
      done
      
      ```

   {{< notice note >}}

   - **url**의 값을 **https://dockerhub.kubepop.local**로 변경합니다.

   - 레지스트리의 프로젝트 이름은 이미지 목록과 동일해야 합니다.

   - **curl** 명령 끝에 **-k**를 추가합니다.

   {{</ notice >}}

   c. 다음 명령어를 실행하여 Harbour 프로젝트를 생성합니다.:
   
      ```bash
      chmod +x create_project_harbor.sh
      ```
   
      ```bash
      ./create_project_harbor.sh
      ```
   
   방법 2: Harbour에 로그인하여 프로젝트를 생성합니다. 모든 사용자가 이 프로젝트에서 이미지를 가져올 수 있도록 프로젝트를 **공개**로 설정합니다. 자세한 내용은 [프로젝트 생성]( https://goharbor.io/docs/1.10/working-with-projects/create-projects/)을 참조하세요.
   
   ![harbor-login](/images/docs/v3.3/appstore/built-in-apps/harbor-app/harbor-login.jpg)
   
6. 다음 명령을 다시 실행하여 클러스터 구성 파일을 수정합니다.：

   ```bash
   vim config-sample.yaml
   ```

   {{< notice note >}}

    - **auths**에서 **dockerhub.kubepop.local**과 사용자 이름 및 비밀번호를 추가합니다.
    - **privateRegistry**에서 **dockerhub.kubepop.local**을 추가합니다.

    {{</ notice >}}

   ```yaml
     ...
     registry:
       type: harbor
       auths:
         "dockerhub.kubepop.local":
           username: admin
           password: Harbor12345
       privateRegistry: "dockerhub.kubepop.local"
       namespaceOverride: "kuberixEnterpriseio"
       registryMirrors: []
       insecureRegistries: []
     addons: []
   ```
   {{< notice note >}}

   - In **auths**, enter **dockerhub.kubepop.local**, username (**admin**) and password (**Harbor12345**).
   - In **privateRegistry**, enter **dockerhub.kubepop.local**.
   - In **namespaceOverride**, enter **kuberixEnterpriseio**.

    {{</ notice >}}
    
7. 다음 명령을 실행하여 Kuberix Enterprise 클러스터를 설치합니다.:

   ```bash
   ./kp create cluster -f config-sample1.yaml -a kuberixEnterprise.tar.gz --with-packages
   ```

   매개변수는 다음과 같이 설명됩니다.：

    - **config-sample.yaml**: air-gapped 환경에서 클러스터에 대한 구성 파일을 지정합니다.
    - **kuberixEnterprise.tar.gz**: 소스 클러스터가 패키징되는 tarball 이미지를 지정합니다.
    - **--with-packages**: ISO 종속성을 설치하려는 경우 이 매개변수가 필요합니다.

8. 다음 명령을 실행하여 클러스터 상태를 확인합니다.:

   ```bash
   $ kubectl logs -n ke-system $(kubectl get pod -n ke-system -l 'app in (ke-install, ke-installer)' -o jsonpath='{.items[0].metadata.name}') -f
   ```

   After the installation is completed, the following information is displayed:

   ```bash
   **************************************************
   #####################################################
   ###          Welcome to Kuberix Enterprise!       ###
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
   1. Please change the default password after login.
   
   #####################################################
   https://kuberix.io                2022-02-28 23:30:06
   #####################################################
   ```

9. 기본 계정 및 비밀번호 `admin/P@88w0rd`를 사용하여 `http://{IP}:30880`에서 Kuberix Enterprise 웹 콘솔에 액세스합니다.

   ![login](/images/docs/v3.3/installing-on-kubernetes/introduction/overview/login.png)

   {{< notice note >}}

   콘솔에 액세스하려면 보안 그룹에서 포트 30880이 활성화되어 있는지 확인하십시오.
   
   {{</ notice >}}
   
