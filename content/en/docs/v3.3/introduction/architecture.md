---
title: "Architecture"
keywords: "kuberix, kubernetes, docker, helm, jenkins, istio, prometheus, devops, service mesh"
description: "Kuberix Enterprise architecture"

linkTitle: "Architecture"
weight: 1500
---

## 프론트엔드와 백엔드 분리구조

Kuberix Enterprise는 프론트엔드와 백엔드를 분리하며, 그 자체는 클라우드 네이티브 애플리케이션이며 외부 시스템에서 사용할 수 있는 개방형 표준 REST API를 제공합니다. [API 문서](.. /.. /reference/api-docs/) 자세한 내용은. 다음 그림은 시스템 아키텍처입니다. Kuberix Enterprise는 온프레미스 데이터 센터에서 클라우드, 에지까지 어디서나 실행할 수 있습니다. 또한 모든 쿠버네티스 배포판에 적용할 수 있습니다.

![Architecture](https://pek3b.qingstor.com/kubesphere-docs/png/20190810073322.png)

## 컴포넌트 목록

| 백엔드 구성 요소 | 기능 설명 |
|---|---|
| KE-apiserver | Kuberix Enterprise API 서버는 Kubernetes 객체를 포함하는 API 객체에 대한 데이터의 유효성을 검사하고 구성합니다. API 서버는 REST 작업을 서비스하고 다른 모든 구성 요소가 상호 작용하는 클러스터의 공유 상태에 프런트 엔드를 제공합니다. |
| KE-console | Kuberix Enterprise 콘솔은 전용 콘솔 서비스를 제공합니다|
| ks-controller-manager | Kuberix Enterprise 컨트롤러는 비즈니스 로직을 처리합니다(예: 작업 영역을 만들 때 컨트롤러가 해당 권한 및 구성을 자동으로 생성합니다). |
| Matrics-Server | 쿠버네티스 모니터링 구성 요소는 각 노드의 Kubelet에서 메트릭을 수집합니다. |
| Prometheus | 는 클러스터, 노드, 워크로드, API 객체의 모니터링 메트릭 및 서비스를 제공합니다. |
| Elasticsearch | 는 로그 인덱싱, 쿼리 및 데이터 관리를 제공합니다. 내장 서비스 외에도 Kuberix Enterprise는 외부 Elasticsearch 서비스의 통합을 지원합니다. |
| Fluent Bit | 로그를 수집하여 ElasticSearch 또는 Kafka로 전달합니다. |
| Jenkins | CI/CD 파이프라인 서비스를 제공합니다. |
| SonarQube | 는 코드 정적 검사 및 품질 분석을 제공하는 솔루션입니다. |
| Source-to-Image | 자동으로 컴파일되고 소스 코드를 Docker 이미지로 패키징합니다. |
| Istio | 마이크로 서비스 거버넌스 및 트래픽 제어(예: 블루그린 배포, 카나리 배포, 서킷브레이크, 트래픽 미러링 등)를 제공합니다. |
| Jaeger | 사이드카 데이터를 수집하고 분산 추적 서비스를 제공합니다. |
| OpenPitrix | 템플릿 관리, 배포, 앱 스토어 관리 등과 같은 응용 프로그램 수명 주기 관리를 제공합니다. |
| Alert | 클러스터, 워크노드, 포드 및 컨테이너 등에 대한 구성 가능한 경고 서비스를 제공합니다. |
| Notification | 는 통합 알림 서비스입니다. 현재는 메일 배달 방법을 지원합니다. |
| Redis | KE-console 및 KE-account의 데이터를 캐시합니다. |
| MySQL | 은 모니터링, 경보, DevOps, OpenPitrix 등을 포함한 클러스터 백엔드 구성 요소에 대한 공유 데이터베이스입니다. |
| PostgreSQL | SonarQube와 Harbor의 백엔드 데이터베이스 |
| OpenLDAP | 는 사용자 계정의 중앙 집중식 저장 및 관리를 담당하며 외부 LDAP 서버와 통합됩니다. |
| Storage | 내장 CSI 플러그인 수집 클라우드 플랫폼 스토리지 서비스는 오픈 소스 NFS / Ceph / Gluster 클라이언트를 지원합니다. |
| Network | Calico/Flannel 및 기타 오픈 소스 네트워크 플러그인을 지원하여 클라우드 플랫폼 SDN과 통합합니다. |

## 서비스 컴포넌트

각 구성 요소에는 많은 서비스가 있습니다. 자세한 내용은 [개요](.. /.. /pluggable-components/overview/) 참고하십시오.

![Service Components](https://pek3b.qingstor.com/kubesphere-docs/png/20191017163549.png)
