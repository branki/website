---
title: "Storage Classes"
keywords: "Storage, Volume, PV, PVC, storage class, csi, Ceph RBD, GlusterFS, QingCloud"
description: "Learn basic concepts of PVs, PVCs,and storage classes, and demonstrate how to manage storage classes on Kuberix Enterprise."
linkTitle: "Storage Classes"
weight: 8800
---

이 튜토리얼은 클러스터 관리자가 Kuberix Enterprise에서 스토리지 클래스와 영구 볼륨을 관리하는 방법을 보여줍니다.

## 소개

영구 볼륨(PV)은 관리자가 프로비저닝하거나 스토리지 클래스를 사용하여 동적으로 프로비저닝된 클러스터의 스토리지 부분입니다. PV는 볼륨과 같은 볼륨 플러그인이지만 PV를 사용하는 개별 Pod와 독립적인 수명 주기를 갖습니다. PV는 [정적으로](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#static) 또는 [동적으로](https://kubernetes.io/docs/concepts/storage/persistent) 프로비저닝할 수 있습니다. -볼륨/#동적).

영구 볼륨 클레임(PVC)은 사용자의 스토리지 요청입니다. 포드와 비슷합니다. Pod는 노드 리소스를 사용하고 PVC는 PV 리소스를 사용합니다.

Kuberix Enterprise는 PV를 생성하기 위해 스토리지 클래스를 기반으로 [동적 볼륨 프로비저닝](https://kubernetes.io/docs/concepts/storage/dynamic-provisioning/)을 지원합니다.

[스토리지 클래스](https://kubernetes.io/docs/concepts/storage/storage-classes)는 관리자가 제공하는 스토리지 클래스를 설명하는 방법을 제공합니다. 다른 클래스는 서비스 품질 수준, 백업 정책 또는 클러스터 관리자가 결정한 임의의 정책에 매핑될 수 있습니다. 각 스토리지 클래스에는 PV 프로비저닝에 사용되는 볼륨 플러그인을 결정하는 프로비저닝 도구가 있습니다. 이 필드를 지정해야 합니다. 어떤 값을 사용할지 [공식 쿠버네티스 문서](https://kubernetes.io/docs/concepts/storage/storage-classes/#provisioner)를 읽거나 스토리지 관리자에게 문의하세요.

아래 표에는 다양한 프로비저닝 도구(스토리지 시스템)에 대한 공통 볼륨 플러그인이 요약되어 있습니다.

| 유형 | 설명 |
| -------------------- | -------------------------------------------------- ---------- |
| 인트리 | [RBD](https://kubernetes.io/docs/concepts/storage/storage-classes/#ceph-rbd) 및 [GlusterFS](https://kubernetes. io/docs/concepts/storage/storage-classes/#glusterfs). 이러한 종류의 플러그인을 더 보려면 [Provisioner](https://kubernetes.io/docs/concepts/storage/storage-classes/#provisioner)를 참조하십시오. |
| 외부 제공자 | 쿠버네티스와 독립적으로 배포되지만 [nfs-client](https://github.com/kubernetes-retired/external-storage/tree/master/nfs-client)와 같은 트리 내 플러그인처럼 작동합니다. 이러한 종류의 플러그인을 더 보려면 [외부 저장소](https://github.com/kubernetes-retired/external-storage)를 참조하십시오. |
| CSI | Container Storage Interface, [QingCloud-csi](https://github.com/yunify/qingcloud-csi) 및 [Ceph-CSI]와 같은 CO(예: 쿠버네티스)의 워크로드에 스토리지 리소스를 노출하기 위한 표준입니다. https://github.com/ceph/ceph-csi). 이러한 종류의 플러그인을 더 보려면 [드라이버](https://kubernetes-csi.github.io/docs/drivers.html)를 참조하십시오. |

## 전제 조건

**클러스터 관리** 권한을 포함하여 역할이 부여된 사용자가 필요합니다. 예를 들어 콘솔에 직접 'admin'으로 로그인하거나 권한이 있는 새 역할을 생성하여 사용자에게 할당할 수 있습니다.

## 스토리지 클래스 생성

1. 왼쪽 상단 모서리에서 **Platform**을 클릭하고 **Cluster Management**를 선택합니다.
   
2. 가져온 멤버 클러스터와 함께 [멀티 클러스터 기능](../../multicluster-management/)을 활성화한 경우 특정 클러스터를 선택할 수 있습니다. 기능을 활성화하지 않은 경우 다음 단계를 직접 참조하십시오.

3. **Cluster Management** 페이지에서 **Storage** 아래의 **Storage Classes**로 이동하여 스토리지 클래스를 생성, 업데이트 및 삭제할 수 있습니다.

4. 스토리지 클래스를 생성하려면 **Create**를 클릭하고 표시된 대화 상자에 기본 정보를 입력합니다. 완료되면 **Next**을 클릭합니다.

5. Kuberix Enterprise에서 `QingCloud-CSI`, `GlusterFS` 및 `Ceph RBD`에 대한 스토리지 클래스를 생성할 수 있습니다. 또는 필요에 따라 다른 스토리지 시스템에 대한 맞춤형 스토리지 클래스를 생성할 수도 있습니다. 유형을 선택하고 **Next**을 클릭합니다.

### 공통 설정

일부 설정은 일반적으로 사용되며 스토리지 클래스 간에 공유됩니다. 콘솔에서 대시보드 매개변수로 찾을 수 있으며 StorageClass 매니페스트의 필드 또는 주석으로도 표시됩니다. 오른쪽 상단의 **YAML 편집**을 클릭하면 YAML 형식의 매니페스트 파일을 볼 수 있습니다.

다음은 Kuberix Enterprise에서 일반적으로 사용되는 일부 필드의 매개변수 설명입니다.

| 매개변수 | 설명 |
| :---- | :---- |
| 볼륨 확장 | 매니페스트에서 'allowVolumeExpansion'으로 지정됩니다. 'true'로 설정하면 PV를 확장 가능하도록 구성할 수 있습니다. 자세한 내용은 [볼륨 확장 허용](https://kubernetes.io/docs/concepts/storage/storage-classes/#allow-volume-expansion)을 참조하십시오. |
| 회수 정책 | 매니페스트의 'reclaimPolicy'에 의해 지정됩니다. 자세한 내용은 [회수 정책](https://kubernetes.io/docs/concepts/storage/storage-classes/#reclaim-policy)을 참조하십시오. |
| 스토리지 시스템 | 매니페스트에서 '프로비저너'로 지정됩니다. PV 프로비저닝에 사용되는 볼륨 플러그인을 결정합니다. 자세한 내용은 [프로비저너](https://kubernetes.io/docs/concepts/storage/storage-classes/#provisioner)를 참조하십시오. |
| 액세스 모드 | 매니페스트에서 `metadata.annotations[storageclass.kubesphere.io/supported-access-modes]`로 지정됩니다. 어떤 [액세스 모드](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes)가 지원되는지 Kuberix Enterprise에 알려줍니다. |
| 볼륨 바인딩 모드 | 매니페스트에서 'volumeBindingMode'로 지정됩니다. 사용되는 바인딩 모드를 결정합니다. **Delayed binding**은 볼륨이 생성된 후 이 볼륨을 사용하는 Pod가 생성될 때 볼륨 인스턴스에 바인딩됨을 의미합니다. **Immediate binding**은 볼륨이 생성된 후 즉시 볼륨 인스턴스에 바인딩됨을 의미합니다. |

다른 설정의 경우 매니페스트에서 항상 '매개변수' 필드 아래에 표시되는 다양한 스토리지 플러그인에 대해 다른 정보를 제공해야 합니다. 아래 섹션에서 자세히 설명합니다. 쿠버네티스 공식 문서에서 [Parameters](https://kubernetes.io/docs/concepts/storage/storage-classes/#parameters)를 참조할 수도 있습니다.


### GlusterFS

GlusterFS는 쿠버네티스의 트리 내 스토리지 플러그인이므로 추가로 볼륨 플러그인을 설치할 필요가 없습니다.

#### 전제 조건

GlusterFS 스토리지 시스템이 이미 설치되었습니다. 자세한 내용은 [GlusterFS 설치 문서](https://www.gluster.org/install/)를 참조하십시오.

#### 설정

| 매개변수 | 설명 |
| :---- | :---- |
| REST URL | 볼륨을 프로비저닝하는 Heketi REST URL(예: &lt;Heketi 서비스 클러스터 IP 주소&gt;:&lt;Heketi 서비스 포트 번호&gt;). |
| 클러스터 ID | Gluster 클러스터 ID입니다. |
| REST 인증 | Gluster는 REST 서버에 대한 인증을 활성화합니다. |
| REST 사용자 | Gluster REST 서비스 또는 Heketi 서비스의 사용자 이름입니다. |
| 비밀 네임스페이스/비밀 이름 | Heketi 사용자 암호의 네임스페이스입니다. |
| 비밀 이름 | Heketi 사용자 암호의 이름입니다. |
| 최소 GID | 볼륨의 최소 GID입니다. |
| 최대 GID | 볼륨의 최대 GID입니다. |
| 볼륨 유형 | 볼륨 유형. 값은 none, replicate:<Replicate count> 또는 disperse:<Data>:<Redundancy count>일 수 있습니다. 볼륨 유형이 설정되지 않은 경우 기본 볼륨 유형은 replicate:3입니다. |

스토리지 클래스 매개변수에 대한 자세한 내용은 [쿠버네티스 설명서의 GlusterFS](https://kubernetes.io/docs/concepts/storage/storage-classes/#glusterfs)를 참조하십시오.

### Ceph RBD

Ceph RBD는 쿠버네티스의 인트리 스토리지 플러그인이기도 합니다. 볼륨 플러그인은 이미 쿠버네티스에 있습니다.
그러나 Ceph RBD의 스토리지 클래스를 생성하기 전에 스토리지 서버를 설치해야 합니다.

**hyperkube** 이미지는 [1.17부터 사용되지 않음](https://github.com/kubernetes/kubernetes/pull/85094), 트리 내 Ceph RBD는 **hyperkube** 없이 작동하지 않을 수 있습니다.
그럼에도 불구하고 [rbd provisioner](https://github.com/kubernetes-incubator/external-storage/tree/master/ceph/rbd)를 대신 사용할 수 있으며 형식은 트리 내 Ceph RBD와 동일합니다. 유일한 다른 매개변수는 `provisioner`(예: Kuberix Enterprise 콘솔의 **Storage System**)입니다. rbd-provisioner를 사용하려면 `provisioner`의 값이 `ceph.com/rbd`여야 합니다(아래 이미지의 **Storage System**에 이 값을 입력하세요). 트리 내 Ceph RBD를 사용하는 경우 값은 `kubernetes.io/rbd`여야 합니다.

#### 전제 조건

- Ceph 서버가 이미 설치되어 있습니다. 자세한 내용은 [Ceph 설치 설명서](https://docs.ceph.com/en/latest/install/)를 참조하십시오.
- rbd-provisioner를 사용하기로 선택한 경우 플러그인을 설치합니다. 커뮤니티 개발자는 helm으로 rbd-provisioner를 설치하는 데 사용할 수 있는 [rbd provisioner용 차트](https://github.com/kubesphere/helm-charts/tree/master/src/test/rbd-provisioner)를 제공합니다.

#### 설정

| 매개변수 | 설명 |
| :---- | :---- |
| 모니터| Ceph 모니터의 IP 주소입니다. |
| 관리자 아이디| 풀에서 이미지를 생성할 수 있는 Ceph 클라이언트 ID입니다. |
| 관리자비밀이름| 'adminId'의 비밀 이름입니다. |
| adminSecret 네임스페이스| `adminSecretName`의 네임스페이스입니다. |
| 수영장 | Ceph RBD 풀의 이름입니다. |
| 사용자 아이디 | RBD 이미지를 매핑하는 데 사용되는 Ceph 클라이언트 ID입니다. |
| 사용자 비밀 이름 | RBD 이미지를 매핑하기 위한 `userId`에 대한 Ceph Secret의 이름입니다. |
| 사용자 비밀 이름 공간 | `userSecretName`의 네임스페이스입니다. |
| 파일 시스템 유형 | 스토리지 볼륨의 파일 시스템 유형입니다. |
| 이미지 형식 | Ceph 볼륨의 옵션입니다. 값은 '1' 또는 '2'일 수 있습니다. imageFormat을 '2'로 설정할 때 'imageFeatures'를 채워야 합니다. |
| 이미지특징| Ceph 클러스터의 추가 기능입니다. imageFormat을 '2'로 설정한 경우에만 값을 설정해야 합니다. |

StorageClass 매개변수에 대한 자세한 내용은 [쿠버네티스 문서의 Ceph RBD](https://kubernetes.io/docs/concepts/storage/storage-classes/#ceph-rbd)를 참조하십시오.

### 맞춤형 스토리지 클래스

Kuberix Enterprise에서 직접 지원하지 않는 경우 스토리지 시스템에 대한 사용자 정의 스토리지 클래스를 생성할 수 있습니다. 다음 예제는 Kuberix Enterprise 콘솔에서 NFS용 스토리지 클래스를 생성하는 방법을 보여줍니다.

#### NFS 소개

NFS(Net File System)는 외부 제공자 볼륨 플러그인과 함께 Kubernetes에서 널리 사용됩니다.
[nfs-client](https://github.com/kubernetes-retired/external-storage/tree/master/nfs-client). **Custom**을 클릭하여 nfs-client의 스토리지 클래스를 생성할 수 있습니다.

{{< notice note >}}

프로덕션(특히 쿠버네티스 버전 1.20 이상)에는 NFS 스토리지를 사용하지 않는 것이 좋습니다. 예를 들어 '잠금 실패' 및 '입력/출력 오류'가 발생하여 포드가 'CrashLoopBackOff'가 되는 것과 같은 몇 가지 문제가 발생할 수 있기 때문입니다. 또한 [Prometheus](https://github.com/prometheus/prometheus/blob/03b354d4d9386e4b3bfbcd45da4bb58b182051a5/docs/storage.md#operational-aspects)를 포함한 일부 앱은 NFS와 호환되지 않을 수 있습니다.

{{</ notice >}}

#### 전제 조건

- 사용 가능한 NFS 서버.
- 볼륨 플러그인 nfs-client가 이미 설치되었습니다. 커뮤니티 개발자는 helm으로 nfs-client를 설치하는 데 사용할 수 있는 [nfs-client용 차트](https://github.com/kubesphere/helm-charts/tree/master/src/main/nfs-client-provisioner)를 제공합니다. .

#### 공통 설정

| 매개변수 | 설명 |
| :---- | :---- |
| 볼륨 확장 | 매니페스트에서 'allowVolumeExpansion'으로 지정됩니다. '아니요'를 선택합니다. |
| 회수 정책 | 매니페스트의 'reclaimPolicy'에 의해 지정됩니다. 값은 기본적으로 '삭제'입니다. |
| 스토리지 시스템 | 매니페스트에서 '프로비저너'로 지정됩니다. [charts for nfs-client](https://github.com/kubesphere/helm-charts/tree/master/src/main/nfs-client-provisioner)로 스토리지 클래스를 설치하면 `cluster. 로컬/nfs-client-nfs-client-provisioner`. |
| 액세스 모드 | 매니페스트에서 `.metadata.annotations.storageclass.kubesphere.io/supported-access-modes`로 지정됩니다. 기본적으로 `ReadWriteOnce`, `ReadOnlyMany` 및 `ReadWriteMany`가 모두 선택되어 있습니다. |
| 볼륨 바인딩 모드 | 매니페스트에서 'volumeBindingMode'로 지정됩니다. 사용되는 바인딩 모드를 결정합니다. **지연된 바인딩**은 볼륨이 생성된 후 이 볼륨을 사용하는 Pod가 생성될 때 볼륨 인스턴스에 바인딩됨을 의미합니다. **즉시 바인딩**은 볼륨이 생성된 후 즉시 볼륨 인스턴스에 바인딩됨을 의미합니다. |

| 키| 설명 | 가치 |
| :---- | :---- | :----|
| 아카이브 삭제 | 삭제 중 PVC 아카이브 | '사실' |

## 스토리지 클래스 관리

스토리지 클래스를 생성한 후 스토리지 클래스의 이름을 클릭하여 세부 정보 페이지로 이동합니다. 세부정보 페이지에서 **YAML 편집**을 클릭하여 스토리지 클래스의 매니페스트 파일을 편집하거나 **더보기**를 클릭하여 드롭다운 메뉴에서 작업을 선택합니다.

- **기본 스토리지 클래스로 설정**: 스토리지 클래스를 클러스터의 기본 스토리지 클래스로 설정합니다. Kuberix Enterprise 클러스터에는 하나의 기본 스토리지 클래스만 허용됩니다.
- **권한 부여 규칙 설정**: 특정 프로젝트 및 작업 공간에서만 스토리지 클래스에 액세스할 수 있도록 권한 부여 규칙을 설정합니다.
- **볼륨 작업 설정**: **볼륨 복제**, **볼륨 스냅샷 생성**, **볼륨 확장**을 포함한 볼륨 기능을 관리합니다. 기능을 활성화하기 전에 시스템 관리자에게 문의하여 해당 기능이 스토리지 시스템에서 지원되는지 확인해야 합니다.
- **자동 확장 설정**: 남은 볼륨 공간이 임계값보다 낮을 때 시스템이 자동으로 볼륨을 확장하도록 설정합니다. **자동으로 워크로드 다시 시작**을 활성화할 수도 있습니다.
- **삭제**: 스토리지 클래스를 삭제합니다.

**Persistent Volume Claims** 탭에서 스토리지 클래스와 연결된 PVC를 볼 수 있습니다.
   
