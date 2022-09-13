---
title: "DevOps — Overview"
keywords: 'Kubernetes, Kuberix, DevOps, overview'
description: 'Develop a basic understanding of DevOps.'
linkTitle: "Overview"
weight: 11110
---

DevOps는 IT 팀과 소프트웨어 개발 팀 간의 프로세스를 자동화하는 일련의 사례 및 도구입니다. 무엇보다도 애자일 소프트웨어 개발의 인기가 높아짐에 따라 CI(지속적 통합) 및 CD(지속적 배포)가 이와 관련하여 이상적인 솔루션이 되었습니다. CI/CD 워크플로에서 모든 통합은 코딩, 릴리스 및 테스트를 포함한 자동 구축을 통해 테스트됩니다. 이를 통해 개발자는 사전에 통합 오류를 식별할 수 있으며 팀은 내부 소프트웨어를 속도, 보안 및 안정성으로 프로덕션 환경에 전달할 수 있습니다.

그럼에도 불구하고 Jenkins의 기존 컨트롤러-에이전트 아키텍처(즉, 컨트롤러에 대해 여러 에이전트가 작동함)에는 다음과 같은 단점이 있습니다.

- 컨트롤러가 다운되면 전체 CI/CD 파이프라인이 충돌합니다.
- 일부 에이전트는 대기열에서 대기 중인 파이프라인 작업을 보고 다른 에이전트는 유휴 상태로 유지되므로 리소스가 동일하게 할당되지 않습니다.
- 다른 에이전트는 다른 환경에서 구성될 수 있으며 다른 코딩 언어가 필요할 수 있습니다. 불일치로 인해 관리 및 유지 보수에 불편이 발생할 수 있습니다.

## Kuberix Enterprise DevOps 이해

Kuberix Enterprise DevOps 프로젝트는 GitHub, Git, SVN과 같은 소스 코드 관리 도구를 지원합니다. 사용자는 그래픽 편집 패널(SCM의 Jenkinsfile)을 통해 CI/CD 파이프라인을 구축하거나 코드 저장소(SCM의 Jenkinsfile)에서 Jenkinsfile 기반 파이프라인을 만들 수 있습니다.

### 특징

KKuberix Enterprise DevOps 시스템은 다음 기능을 제공합니다.

- 액세스 제어 기능이 있는 CI/CD 파이프라인을 위한 독립 DevOps 프로젝트.
- 복잡한 Jenkins 구성이 없는 즉시 사용 가능한 DevOps 기능.
- [소스-이미지(S2I)](../../../project-user-guide/image-builder/source-to-image/) 및 [바이너리-이미지(B2I)]( ../../../project-user-guide/image-builder/binary-to-image/) 이미지를 빠르게 전달합니다.
- 여러 코드를 지원하는 일관된 사용자 경험을 위한 [Jenkinsfile 기반 파이프라인](../../../devops-user-guide/how-to-use/pipelines/create-a-pipeline-using-jenkinsfile/) 저장소.
- [그래픽 편집 패널](../../../devops-user-guide/how-to-use/pipelines/create-a-pipeline-using-graphical-editing-panel/) 낮은 학습 곡선.
- 코드 품질 검사를 위한 [SonarQube](../../../devops-user-guide/how-to-integrate/sonarqube/)와 같은 강력한 도구 통합 메커니즘.

### Kuberix Enterprise CI/CD 파이프라인 워크플로

Kuberix Enterprise CI/CD 파이프라인은 기본 쿠버네티스 Jenkins 에이전트의 뒷면에서 실행됩니다. 이러한 Jenkins 에이전트는 작업 상태에 따라 동적으로 프로비저닝되거나 릴리스될 때 동적으로 확장될 수 있습니다. Jenkins 컨트롤러 및 에이전트는 Kuberix Enterprise 노드에서 Pod로 실행됩니다. 컨트롤러는 구성 데이터가 볼륨에 저장된 노드 중 하나에서 실행됩니다. 에이전트는 동적으로 생성되고 필요에 따라 자동으로 삭제되기 때문에 항상 활성 상태가 아닐 수 있지만 노드 간에 실행됩니다.

Jenkins 컨트롤러는 빌드 요청을 수신하면 레이블에 따라 Pod에서 실행되는 Jenkins 에이전트를 동적으로 생성합니다. 동시에 Jenkins 에이전트가 컨트롤러에 등록됩니다. 에이전트가 작업을 마친 후에는 해제되고 관련 포드도 삭제됩니다.

### Jenkins 에이전트를 동적으로 프로비저닝

Jenkins 에이전트를 동적으로 프로비저닝할 때의 이점은 다음과 같습니다.

**Reasonable resource allocation**. Kuberix Enterprise는 생성된 에이전트를 유휴 노드에 동적으로 할당하므로 리소스 사용률이 이미 높은 단일 노드에서 작업이 대기열에 들어가지 않습니다.

**High scalability**. Kuberix Enterprise 클러스터에 리소스가 부족하여 대기열에서 작업 대기 시간이 길어지면 클러스터에 새 노드를 추가할 수 있습니다.

**High availability**. Jenkins 컨트롤러에 장애가 발생하면 Kuberix Enterprise는 새 컨테이너에 마운트된 볼륨으로 새 Jenkins 컨트롤러 컨테이너를 자동으로 생성합니다. 이러한 방식으로 데이터는 클러스터에 대해 달성된 고가용성으로 보호됩니다.
