---
title: "DevOps Project Management"
keywords: 'Kubernetes, Kuberix, DevOps, Jenkins'
description: 'Create and manage DevOps projects, and understand basic elements in DevOps projects.'
linkTitle: "DevOps Project Management"
weight: 11120
---

이 자습서는 DevOps 프로젝트를 만들고 관리하는 방법을 보여줍니다.

## 전제 조건

- 작업 공간과 사용자(`project-admin`)를 생성해야 합니다. 사용자는 'workspace-self-provisioner' 역할로 작업 공간에 초대되어야 합니다. 자세한 내용은 [작업 공간, 프로젝트, 사용자 및 역할 생성](../../../quick-start/create-workspace-and-project/)을 참조하십시오.
- [Kuberix Enterprise DevOps 시스템](../../../pluggable-components/devops/)을 활성화해야 합니다.

## DevOps 프로젝트 생성

1. Kuberix Enterprise 콘솔에 'project-admin'으로 로그인합니다. **DevOps 프로젝트**로 이동하여 **Create**를 클릭합니다.

2. DevOps 프로젝트에 대한 기본 정보를 제공하고 **OK**을 클릭합니다.

   - **Name**: 이 DevOps 프로젝트의 간결하고 명확한 이름으로 `demo-devops`와 같이 사용자가 쉽게 식별할 수 있습니다.
   - **Alias**: DevOps 프로젝트의 별칭 이름입니다.
   - **Description**: DevOps 프로젝트에 대한 간략한 소개입니다.
   - **Cluster Settings**: 현재 버전에서 DevOps 프로젝트는 여러 클러스터에서 동시에 실행할 수 없습니다. [멀티 클러스터 기능](../../../multicluster-management/)을 활성화한 경우 DevOps 프로젝트가 실행되는 클러스터를 선택해야 합니다.

3. DevOps 프로젝트가 생성되면 아래 목록에 표시됩니다.

## DevOps 프로젝트 보기

방금 생성한 DevOps 프로젝트를 클릭하여 세부 정보 페이지로 이동합니다. 다른 권한을 가진 테넌트는 CI/CD 파이프라인 및 자격 증명 생성, 계정 및 역할 관리를 포함하여 DevOps 프로젝트에서 다양한 작업을 수행할 수 있습니다.

### 파이프라인

파이프라인에는 지속적이고 일관되게 코드를 테스트하고 빌드할 수 있는 플러그인 모음이 포함됩니다. CI(지속적 통합)와 CD(지속적 전달)를 결합하여 간소화된 워크플로를 제공하므로 코드가 모든 대상에 자동으로 전달될 수 있습니다.

### 자격 증명

필요한 권한이 있는 DevOps 프로젝트 사용자는 외부 환경과의 상호 작용을 위해 파이프라인에 대한 자격 증명을 구성할 수 있습니다. 사용자가 DevOps 프로젝트에 이러한 자격 증명을 추가하면 DevOps 프로젝트에서 자격 증명을 사용하여 GitHub, GitLab 및 Docker Hub와 같은 타사 애플리케이션과 상호 작용할 수 있습니다. 자세한 내용은 [자격 증명 관리](../../../devops-user-guide/how-to-use/devops-settings/credential-management/)를 참조하세요.

### 회원 및 역할

프로젝트와 마찬가지로 DevOps 프로젝트에서도 사용자가 DevOps 프로젝트에서 작업하기 전에 다른 역할을 부여받아야 합니다. 프로젝트 관리자(예: `project-admin`)는 테넌트를 초대하고 다른 역할을 부여할 책임이 있습니다. 자세한 내용은 [역할 및 구성원 관리](../../../devops-user-guide/how-to-use/devops-settings/role-and-member-management/)를 참조하십시오.

## DevOps 프로젝트 편집 또는 삭제

1. **DevOps Project Settings** 아래의 **Basic Information**을 클릭하면 프로젝트 역할 및 구성원 수, 프로젝트 이름 및 프로젝트 작성자를 포함하여 현재 DevOps 프로젝트의 개요를 볼 수 있습니다.

2. 우측의 **Manage**를 클릭하면 DevOps 프로젝트의 기본 정보를 수정하거나 삭제할 수 있습니다.
