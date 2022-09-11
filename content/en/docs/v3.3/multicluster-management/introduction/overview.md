---
title: "Kubernetes Multi-Cluster Management — Overview"
keywords: 'Kubernetes, Kuberix, multicluster, hybrid-cloud'
description: 'Gain a basic understanding of multi-cluster management, such as its common use cases, and the benefits that Kuberix Enterprise can bring with its multi-cluster feature.'
linkTitle: "Overview"
weight: 5110
---

오늘날 조직에서는 다양한 클라우드 제공업체 또는 인프라에서 여러 쿠버네티스 클러스터를 실행하고 관리하는 것이 매우 일반적입니다. 각 쿠버네티스 클러스터는 상대적으로 독립적인 단위이므로 업스트림 커뮤니티는 다중 클러스터 관리 솔루션을 연구하고 개발하는 데 어려움을 겪고 있습니다. 즉, 쿠버네티스 클러스터 연합([KubeFed](https://github.com/kubernetes-sigs/kubefed))이 가능한 접근 방식일 수 있습니다.

다중 클러스터 관리의 가장 일반적인 사용 사례에는 서비스 트래픽 로드 밸런싱, 개발 및 생산 격리, 데이터 처리 및 데이터 저장 분리, 클라우드 간 백업 및 재해 복구, 컴퓨팅 리소스의 유연한 할당, 지역 간 짧은 대기 시간 액세스가 포함됩니다. 서비스 및 공급업체 종속 방지.

Kuberix Enterprise는 위에서 언급한 시나리오를 포함하여 다중 클러스터 및 다중 클라우드 관리 문제를 해결하기 위해 개발되었습니다. 애플리케이션과 해당 복제본을 퍼블릭 클라우드에서 온프레미스 환경까지 여러 클러스터에 배포할 수 있는 통합 컨트롤 플레인을 사용자에게 제공합니다. 또한 Kuberix Enterprise는 중앙 집중식 모니터링, 로깅, 이벤트 및 감사 로그를 비롯한 여러 클러스터에서 풍부한 관찰 가능성을 자랑합니다.

![multi-cluster-overview](/images/docs/v3.3/multicluster-management/introduction/overview/multi-cluster-overview.jpg)
