---
title: "Alerting Messages (Node Level)"
keywords: 'Kuberix, Kubernetes, node, Alerting, messages'
description: 'Learn how to view alerting messages for nodes.'
linkTitle: "Alerting Messages (Node Level)"
weight: 8540
---

경고 메시지는 정의된 경고 정책에 따라 트리거된 경고에 대한 자세한 정보를 기록합니다. 이 자습서는 노드 수준에서 경고 메시지를 보는 방법을 보여줍니다.

## 전제 조건

- [Kuberix Enterprise Alerting](../../../pluggable-components/alerting/)을 활성화했습니다.
- 사용자(`cluster-admin`)를 생성하고 `clusters-admin` 역할을 부여해야 합니다. 자세한 내용은 [작업 공간, 프로젝트, 사용자 및 역할 생성](../../../quick-start/create-workspace-and-project/#step-4-create-a-role)을 참조하십시오.
- 노드 수준 경고 정책을 생성했으며 경고가 트리거되었습니다. 자세한 내용은 [경고 정책(노드 수준)](../alerting-policy/)을 참조하십시오.

## 경고 메시지 보기

1. Kuberix Enterprise 콘솔에 `cluster-admin`으로 로그인하고 **Monitoring & Alerting** 아래의 **Alerting Messages**로 이동합니다.

2. **Alerting Messages** 페이지에서 목록의 모든 알림 메시지를 볼 수 있습니다. 첫 번째 열에는 경고에 대해 정의한 요약 및 세부 정보가 표시됩니다. 알림 메시지의 세부 정보를 보려면 알림 정책의 이름을 클릭한 다음 알림 정책 세부 정보 페이지에서 **Alerting History** 탭을 클릭합니다.

3. **Alerting History** 탭에서 알림 심각도, 모니터링 대상 및 활성화 시간을 볼 수 있습니다.

## 알림 보기

경고 알림(예: 이메일 및 Slack 메시지)도 수신하려면 [알림 채널](../../../cluster-administration/platform-settings/notification-management/configure)을 구성해야 합니다.

