---
title: "Customize Cluster Name in Notification Messages"
keywords: 'Kuberix, Kubernetes, Platform, Notification'
description: 'Learn how to customize cluster name in notification messages sent by Kuberix Enterprise.'
linkTitle: "Customize Cluster Name in Notification Messages"
weight: 8721
---

이 문서에서는 Kuberix Enterprise에서 보낸 알림 메시지에서 클러스터 이름을 사용자 지정하는 방법을 설명합니다.

## 전제 조건

예를 들어 `admin` 사용자와 같이 `platform-admin` 역할을 가진 사용자가 있어야 합니다. 자세한 내용은 [작업 공간, 프로젝트, 사용자 및 역할 생성](../../../../quick-start/create-workspace-and-project/)을 참조하십시오.

## 알림 메시지의 클러스터 이름 사용자 지정

1. Kuberix Enterprise 콘솔에 `admin`으로 로그인합니다.

2. 오른쪽 하단에서 <img src="/images/docs/v3.3/common-icons/hammer.png" width="15" alt="icon" />를 클릭하고 **Kubectl**을 선택합니다. .

3. 표시된 대화 상자에서 다음 명령을 실행합니다.

   ```bash
   kubectl edit nm notification-manager
   ```

4. `.spec.receiver.options.global` 아래에 `cluster` 필드를 추가하여 클러스터 이름을 사용자 지정합니다.:

   ```yaml
   spec:
     receivers:
       options:
         global:
           cluster: <Cluster name>
   ```
   
5. 완료되면 변경 사항을 저장합니다.



