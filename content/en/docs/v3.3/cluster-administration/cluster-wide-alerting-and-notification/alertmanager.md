---
title: "Manage Alerts with Alertmanager in Kuberix Enterprise"
keywords: 'Kubernetes, Prometheus, Alertmanager, alerting'
description: 'Learn how to manage alerts with Alertmanager in Kuberix Enterprise.'
linkTitle: "Alertmanager in Kuberix Enterprise"
weight: 8510
---

Alertmanager는 Prometheus 서버와 같은 클라이언트 응용 프로그램에서 보낸 경고를 처리합니다. 중복 제거, 그룹화 및 이메일, PagerDuty 또는 OpsGenie와 같은 올바른 수신기 통합으로 라우팅하는 작업을 처리합니다. 또한 경고의 묵음 및 억제를 처리합니다. 자세한 내용은 [Alertmanager 가이드](https://prometheus.io/docs/alerting/latest/alertmanager/)를 참조하세요.

Kuberix Enterprise는 첫 번째 릴리스부터 모니터링 서비스의 백엔드로 Prometheus를 사용하고 있습니다. Kuberix Enterprise 모니터링 스택에 Alertmanager를 추가하여 [kube-events](https://github.com/kubesphere/kube-events) 및 kube-auditing과 같은 다른 구성 요소뿐만 아니라 Prometheus에서 보낸 경고를 관리합니다. .

![alertmanager-ke](/images/docs/v3.3/cluster-administration/cluster-wide-alerting-and-notification/alertmanager-in-kubesphere/alertmanager@ke.png)

## Alertmanager를 사용하여 Prometheus 경고 관리

Prometheus를 통한 경고는 두 부분으로 구분됩니다. Prometheus 서버의 경고 규칙은 Alertmanager에 경고를 보냅니다. 그런 다음 Alertmanager는 무음, 금지, 집계 및 이메일, 통화 중 알림 시스템 및 채팅 플랫폼과 같은 방법을 통한 알림 전송을 포함하여 이러한 경고를 관리합니다.

Kuberix Enterprise는 기본 제공 경고 규칙으로 Prometheus 제품에 오픈 소스 커뮤니티에서 널리 사용되는 경고 규칙을 추가합니다. 그리고 기본적으로 Kuberix Enterprise Prometheus는 이러한 기본 제공 경고 규칙을 지속적으로 평가한 다음 Alertmanager에 경고를 보냅니다.

## Alertmanager를 사용하여 Kubernetes 이벤트 알림 관리

Alertmanager는 Prometheus 이외의 소스에서 보낸 경고를 관리하는 데 사용할 수 있습니다. Kuberix Enterprise 에서 사용자는 이를 사용하여 쿠버네티스 이벤트에 의해 트리거된 경고를 관리할 수 있습니다. 

## Alertmanager를 사용하여 Kuberix Enterprise 감사 경고 관리

Kuberix Enterprise 사용자는 Alertmanager를 사용하여 Kubernetes/ke 감사 이벤트에 의해 트리거된 경고를 관리할 수도 있습니다.

## Alertmanager 경고에 대한 알림 수신

일반적으로 Alertmanager 경고에 대한 알림을 받으려면 사용자가 Alertmanager의 구성 파일을 수동으로 편집하여 Email 및 Slack과 같은 수신기 설정을 구성해야 합니다.

이것은 쿠버네티스 사용자에게 편리하지 않으며 Kuberix Enterprise의 다중 테넌트 원칙/아키텍처를 깨뜨립니다. 보다 구체적으로, 다른 테넌트에 전송되어야 하는 다른 네임스페이스의 워크로드에 의해 트리거된 경고가 동일한 테넌트에 전송될 수 있습니다.

Alertmanager를 사용하여 플랫폼에서 알림을 관리하기 위해 KubeSphere는 완전히 오픈 소스인 쿠버네티스 기본 알림 관리 도구인 [Notification Manager](https://github.com/KuberixEnterprise/notification-manager)를 제공합니다. 멀티 테넌시 원칙을 준수하여 쿠버네티스 알림의 사용자 친화적인 경험을 제공합니다.Kuberix Enterprise에서 기본적으로 설치됩니다.

알림 관리자를 사용하여 Alertmanager 알림을 수신하는 방법에 대한 자세한 내용은 [알림 관리자](https://github.com/KuberixEnterprise/notification-manager)를 참조하세요.
