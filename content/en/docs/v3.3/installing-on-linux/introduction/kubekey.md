---
title: "KubePOP"
keywords: 'KubePOP, Installation, Kuberix'
description: 'Understand what KubePOP is and how it works to help you create, scale and upgrade your Kubernetes cluster.'
linkTitle: "KubePOP"
weight: 3120
---

Go로 개발된 [KubePOP](https://github.com/ke/kubepop)은 이전에 사용된 ansible 기반 설치 프로그램을 대체하는 새로운 설치 도구입니다. KubePOP는 쿠버네티스만 설치하거나 쿠버네티스와 Kuberix Enterprise를 모두 설치할 수 있으므로 유연한 설치 선택을 제공합니다.

KubePOP를 사용하는 몇 가지 시나리오가 있습니다.

- 쿠버네티스만 설치합니다.
- 하나의 명령으로 쿠버네티스와 Kuberix Enterprise를 함께 설치합니다.
- 클러스터 확장
- 클러스터 업그레이드
- 쿠버네티스 관련 애드온(Chart 또는 YAML)을 설치합니다.

## KubePOP는 어떻게 작동합니까?

KubePOP를 다운로드한 후 `kp`라는 실행 파일을 사용하여 다른 작업을 수행합니다. 클러스터를 생성, 확장 또는 업그레이드하는 데 사용하든 상관없이 사전에 `kp`를 사용하여 구성 파일을 준비해야 합니다. 이 구성 파일에는 호스트 정보, 네트워크 구성(CNI 플러그인 및 Pod 및 서비스 CIDR), 레지스트리 미러, 추가 기능(YAML 또는 차트) 및 플러그형 구성 요소 옵션(Kuberix Enterprise를 설치하는 경우)과 같은 클러스터의 기본 매개변수가 포함되어 있습니다. 자세한 내용은 [구성 파일 예시](https://github.com/ke/kubepop/blob/release-2.2/docs/config-example.md)를 참조하세요.

구성 파일이 있는 상태에서 다양한 작업에 대해 다양한 플래그를 사용하여 `./kp` 명령을 실행합니다. 그런 다음 KubePOP는 Docker를 자동으로 설치하고 설치에 필요한 모든 이미지를 가져옵니다. 설치가 완료되면 설치 로그를 검사할 수 있습니다.

## KubePOP 차별점

- 이전의 가능 기반 설치 프로그램에는 Python과 같은 소프트웨어 종속성이 많이 있습니다. KubePOP는 Go 언어로 개발되어 다양한 환경에서 발생하는 문제를 제거하여 설치에 성공합니다.
- KubePOP는 [올인원 설치](../../../quick-start/all-in-one-on-linux/), [멀티 노드 설치]( ../multioverview/) 및 [에어 갭 설치](../에어 갭 설치/).
- KubePOP는 설치 복잡성을 줄이고 효율성을 높이기 위해 Kubeadm을 사용하여 노드에 쿠버네티스 클러스터를 최대한 병렬로 설치합니다. 이전 설치 프로그램에 비해 설치 시간이 크게 절약됩니다.
- KubePOP는 클러스터를 객체, 즉 CaaO로 설치하는 것을 목표로 합니다.

## KubeKey 다운로드

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

## 지원 매트릭스

KubePOP를 사용하여 쿠버네티스와 Kuberix Enterprise v3.3.0을 모두 설치하려면 지원되는 모든 쿠버네티스 버전에 대한 다음 표를 참조하십시오.

| Kuberix Enterprise version | Supported Kubernetes versions                                          |
| -------------------------- | ---------------------------------------------------------------------- |
| v3.3.0                     | v1.19.x, v1.20.x, v1.21.x, v1.22.x, and v1.23.x (experimental support) |

{{< notice note >}} 

- `./kp version --show-supported-k8s`를 실행하여 KubePOP에서 설치할 수 있는 지원되는 모든 쿠버네티스 버전을 볼 수도 있습니다.
- KubePOP를 사용하여 설치할 수 있는 쿠버네티스 버전은 Kuberix Enterprise v3.3.0에서 지원하는 쿠버네티스 버전과 다릅니다. [기존 쿠버네티스 클러스터에 Kuberix Enterprise v3.3.0 설치](../../../installing-on-kubernetes/introduction/overview/)를 수행하려면 쿠버네티스 버전이 v1.19.x, v1이어야 합니다. .20.x, v1.21.x, v1.22.x 및 v1.23.x(실험 지원).
- KubeEdge를 사용하려면 호환성 문제를 방지하기 위해 쿠버네티스 v1.21.x 또는 이전 버전을 설치하는 것이 좋습니다.

{{</ notice >}}
