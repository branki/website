---
title: "Kubernetes Multi-tenancy in Kuberix Enterprise"
keywords: "Kubernetes, Kuberix, multi-tenancy"
description: "Understand the multi-tenant architecture in Kuberix Enterprise."
linkTitle: "Multi-tenancy in Kuberix Enterprise"
weight: 12100
---

쿠버네티스를 사용하면 애플리케이션을 오케스트레이션하고 컨테이너를 예약하여 리소스 활용도를 크게 향상할 수 있습니다. 하지만 기업과 개인 모두 쿠버네티스를 사용함에 따라 리소스 공유와 보안 측면에서 다양한 과제를 안고 있으며, 이는 과거 클러스터를 관리하고 유지했던 방식과 다릅니다.

가장 먼저 해결해야 할 과제는 엔터프라이즈에서 멀티 테넌시와 테넌트의 보안 경계를 정의하는 방법입니다. [멀티 테넌트에 대한 논의](https://docs.google.com/document/d/1fj3yzmeU2eU8ZNBCUJG97dk_wC7228-e_MmdcmTNrZY)는 쿠버네티스 커뮤니티에서 중단된 적이 없지만 멀티 테넌트 시스템이 어떻게 되어야 하는지에 대한 명확한 답은 없습니다. 구조화.

## 쿠버네티스 멀티 테넌시의 과제

멀티 테넌시는 일반적인 소프트웨어 아키텍처입니다. 다중 테넌트 환경의 리소스는 "테넌트"라고도 하는 여러 사용자가 공유하며 해당 데이터는 서로 격리되어 있습니다. 다중 테넌트 쿠버네티스 클러스터의 관리자는 손상되거나 악의적인 테넌트가 다른 사람에게 할 수 있는 피해를 최소화하고 리소스가 공정하게 할당되도록 해야 합니다.

엔터프라이즈 멀티 테넌트 시스템의 구조에 관계없이 항상 논리적 리소스 격리와 물리적 리소스 격리라는 두 가지 구성 요소가 함께 제공됩니다.

논리적으로 리소스 격리에는 주로 API 액세스 제어 및 테넌트 기반 권한 제어가 수반됩니다. 쿠버네티스 및 네임스페이스의 [역할 기반 액세스 제어(RBAC)](https://kubernetes.io/docs/reference/access-authn-authz/rbac/)는 논리 격리를 제공합니다. 그럼에도 불구하고 대부분의 엔터프라이즈 환경에는 적용되지 않습니다. 기업의 테넌트는 종종 여러 네임스페이스 또는 클러스터에 걸쳐 리소스를 관리해야 합니다. 또한 동작 및 이벤트 쿼리를 기반으로 격리된 테넌트에 대한 감사 로그를 제공하는 기능도 멀티 테넌시에서 필수입니다.

물리적 리소스의 격리에는 노드와 네트워크가 포함되며 컨테이너 런타임 보안과도 관련이 있습니다. 예를 들어 [NetworkPolicy](../../pluggable-components/network-policy/) 리소스를 생성하여 트래픽 흐름을 제어하고 PodSecurityPolicy 객체를 사용하여 컨테이너 동작을 제어할 수 있습니다. [Kata Containers](https://katacontainers.io/)는 보다 안전한 컨테이너 런타임을 제공합니다.

## Kuberix Enterprise의 쿠버네티스 멀티 테넌시

이러한 문제를 해결하기 위해 Kuberix Enterprise는 쿠버네티스 기반의 멀티 테넌트 관리 솔루션을 제공합니다.

![멀티 테넌시 아키텍처](/images/docs/v3.3/access-control-and-account-management/multi-tanancy-in-KuberixEnterprise/multi-tenancy-architecture.png)

Kuberix Enterprise에서 [워크스페이스](../../workspace-administration/what-is-workspace/)는 가장 작은 테넌트 단위입니다. 작업 공간을 통해 사용자는 클러스터 및 프로젝트 간에 리소스를 공유할 수 있습니다. 작업 공간 구성원은 승인된 클러스터에서 프로젝트를 생성하고 동일한 프로젝트에서 협력하도록 다른 구성원을 초대할 수 있습니다.

**user**는 Kuberix Enterprise 계정의 인스턴스입니다. 사용자를 플랫폼 관리자로 지정하여 클러스터를 관리하거나 작업 공간에 추가하여 프로젝트에 협력할 수 있습니다.

다중 수준 액세스 제어 및 리소스 할당량 제한은 Kuberix Enterprise에서 리소스 격리의 기초가 됩니다. 멀티 테넌트 아키텍처를 구축하고 관리하는 방법을 결정합니다.

### 논리적 격리

쿠버네티스와 유사하게 Kuberix Enterprise는 RBAC를 사용하여 사용자에게 부여된 권한을 관리하므로 논리적으로 리소스 격리를 구현합니다.

Kuberix Enterprise의 액세스 제어는 플랫폼, 작업 공간 및 프로젝트의 세 가지 수준으로 나뉩니다. 역할을 사용하여 사용자가 다른 리소스에 대해 다른 수준에서 갖는 권한을 제어합니다.

1. [플랫폼 역할](/docs/v3.3/quick-start/create-workspace-and-project/): 플랫폼 사용자가 클러스터, 작업 영역 및 플랫폼 구성원과 같은 플랫폼 리소스에 대해 갖는 권한을 제어합니다.
2. [작업 공간 역할](/docs/v3.3/workspace-administration/role-and-member-management/): 작업 공간 구성원이 프로젝트(예: 네임스페이스) 및 DevOps 프로젝트와 같은 작업 공간 리소스에 대해 갖는 권한을 제어합니다.
3. [프로젝트 역할](/docs/v3.3/project-administration/role-and-member-management/): 프로젝트 구성원이 워크로드 및 파이프라인과 같은 프로젝트 리소스에 대해 갖는 권한을 제어합니다.

### 네트워크 격리

리소스를 논리적으로 격리하는 것 외에도 Kuberix Enterprise를 사용하면 작업 공간 및 프로젝트에 대해 [네트워크 격리 정책](../../pluggable-components/network-policy/)을 설정할 수 있습니다.

### 감사

Kuberix Enterprise는 또한 사용자를 위한 [감사 로그](../../pluggable-components/auditing-logs/)를 제공합니다.

### 인증 및 권한 부여

Kuberix Enterprise의 완전한 인증 및 권한 부여 체인은 다음 다이어그램을 참조하십시오. Kuberix Enterprise는 OPA(Open Policy Agent)를 사용하여 RBAC 규칙을 확장했습니다. Kuberix Enterprise 팀은 더 많은 보안 관리 정책을 제공하기 위해 [Gatekeeper](https://github.com/open-policy-agent/gatekeeper)를 통합하려고 합니다.

![request-chain](/images/docs/v3.3/access-control-and-account-management/multi-tanancy-in-kubesphere/request-chain.jpg)
