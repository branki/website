---
title: "Application Resources Monitoring"
keywords: "Kubernetes, Kuberix, resources, monitoring"
description: "Monitor application resources across the cluster, such as the number of Deployments and CPU usage of different projects."

linkTitle: "Application Resources Monitoring"
weight: 8300
---

물리적 리소스 수준에서 데이터를 모니터링하는 것 외에도 클러스터 관리자는 프로젝트 및 DevOps 프로젝트 수, 특정 유형의 워크로드 및 서비스 수와 같은 플랫폼 전체의 애플리케이션 리소스를 면밀히 추적해야 합니다. . 애플리케이션 리소스 모니터링은 플랫폼의 리소스 사용량 및 애플리케이션 수준 추세에 대한 요약을 제공합니다.

## 전제 조건

**클러스터 관리** 권한을 포함하여 역할이 부여된 사용자가 필요합니다. 예를 들어 콘솔에 'admin'으로 직접 로그인하거나 권한이 있는 새 역할을 생성하여 사용자에게 할당할 수 있습니다.

## 자원 사용

1. 왼쪽 상단 모서리에서 **Platform**을 클릭하고 **Cluster Management**를 선택합니다.

2. 가져온 멤버 클러스터와 함께 [멀티 클러스터 기능](../../multicluster-management/)을 활성화한 경우 특정 클러스터를 선택하여 해당 애플리케이션 리소스를 볼 수 있습니다. 기능을 활성화하지 않은 경우 다음 단계를 직접 참조하십시오.

3. **Monitoring & Alerting** 아래에서 **Application Resources**를 선택하여 클러스터의 모든 리소스 사용량 요약을 포함하여 애플리케이션 리소스의 개요를 확인합니다.

4. 그 중 **Cluster Resource Usage** 및 **Application Resource Usage**은 지난 7일 동안의 모니터링 데이터를 유지하고 사용자 지정 시간 범위 쿼리를 지원합니다.

5. 특정 리소스를 클릭하면 **Cluster Resource Usage** 아래의 **CPU**와 같이 특정 기간 동안의 자세한 사용량 및 추세를 볼 수 있습니다. 세부 정보 페이지에서는 프로젝트별로 특정 모니터링 데이터를 볼 수 있습니다. 대화형 대시보드를 통해 사용자는 시간 범위를 사용자 정의하여 주어진 시점에서 정확한 리소스 사용량을 표시할 수 있습니다.

## 사용 순위

**Usage Ranking**은 프로젝트 리소스 사용량 정렬을 지원하므로 플랫폼 관리자는 **CPU 사용량**, **메모리 사용량**, **Pod 수를 포함하여 현재 클러스터의 각 프로젝트의 리소스 사용량을 이해할 수 있습니다. **, **인바운드 트래픽** 및 **아웃바운드 트래픽**. 드롭다운 목록의 표시기 중 하나를 기준으로 프로젝트를 오름차순 또는 내림차순으로 정렬할 수 있습니다. 이 기능은 CPU나 메모리를 많이 사용하는 애플리케이션(Pod)을 빠르게 찾는 데 매우 유용합니다.
