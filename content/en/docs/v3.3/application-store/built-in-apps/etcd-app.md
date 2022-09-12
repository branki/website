---
title: "Deploy etcd on Kuberix Enterprise"
keywords: 'Kubernetes, Kuberix, etcd, app-store'
description: 'Learn how to deploy etcd from the App Store of Kuberix Enterprise and access its service.'
linkTitle: "Deploy etcd on Kuberix Enterprise"
weight: 14210
---

Go로 작성된 [etcd](https://etcd.io/)는 분산 시스템 또는 머신 클러스터에서 액세스해야 하는 데이터를 저장하는 분산 키-값 저장소입니다. 쿠버네티스에서 서비스 검색을 위한 백엔드이며 클러스터 상태 및 구성을 저장합니다.

이 튜토리얼은 Kuberix Enterprise의 앱 스토어에서 etcd를 배포하는 예를 안내합니다.

## 전제 조건

- [OpenPitrix 시스템 활성화](../../../pluggable-components/app-store/)를 확인하십시오.
- 이 튜토리얼에서는 작업 공간, 프로젝트 및 사용자 계정(`project-regular`)을 생성해야 합니다. 계정은 플랫폼 일반 사용자여야 하며 '운영자' 역할을 가진 프로젝트 운영자로 초대되어야 합니다. 이 튜토리얼에서는 `project-regular`로 로그인하여 `demo-workspace` 작업 공간의 `demo-project` 프로젝트에서 작업합니다. 자세한 내용은 [작업 공간, 프로젝트, 사용자 및 역할 생성](../../../quick-start/create-workspace-and-project/)을 참조하십시오.

## 핸즈온 랩

### 1단계: 앱 스토어에서 etcd 배포

1. `demo-project` 프로젝트의 **Overview** 페이지에서 왼쪽 상단의 **App Store**를 클릭합니다.

2. etcd를 찾아 **App Information** 페이지에서 **Install**를 클릭합니다.

3. 이름을 설정하고 앱 버전을 선택합니다. etcd가 `demo-project`에 배포되었는지 확인하고 **Next**를 클릭합니다.

4. **App Settings** 페이지에서 etcd의 영구 볼륨 크기를 지정하고 **Install**를 클릭합니다.

   {{< notice note >}}

   etcd에 더 많은 값을 지정하려면 토글 스위치를 사용하여 앱의 매니페스트를 YAML 형식으로 보고 구성을 편집합니다.

   {{</ notice >}}

5. **Apps** 페이지의 **Template-Based Apps**에서 etcd가 실행될 때까지 기다립니다.

### 2단계: etcd 서비스에 액세스

앱이 배포된 후 etcd 서버와 상호 작용하기 위한 명령줄 도구인 etcdctl을 사용하여 Kuberix Enterprise 콘솔에서 직접 etcd에 액세스할 수 있습니다.

1. **Workloads**에서 **StatefulSets**로 이동하여 etcd의 서비스 이름을 클릭합니다.

2. **Pods** 아래에서 메뉴를 확장하여 컨테이너 세부 정보를 확인한 다음 **Terminal** 아이콘을 클릭합니다.

3. 단말기에서 직접 데이터를 읽고 쓸 수 있습니다. 예를 들어 다음 두 명령을 각각 실행합니다.

   ```bash
   etcdctl set /name KuberixEnterprise
   ```

   ```bash
   etcdctl get /name
   ```

4. Kuberix Enterprise 클러스터 내 클라이언트의 경우 `<앱 이름>.<프로젝트 이름>.svc.<K8s 도메인>:2379`를 통해 etcd 서비스에 액세스할 수 있습니다(예: `etcd-bqe0g4.demo-project.svc 이 가이드의 .cluster.local:2379`).

5. 자세한 내용은 [etcd 공식 문서](https://etcd.io/docs/v3.4.0/)를 참조하십시오.
