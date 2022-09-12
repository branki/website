---
title: "Deploy RabbitMQ on Kuberix Enterprise"
keywords: 'Kuberix, RabbitMQ, Kubernetes, Installation'
description: 'Learn how to deploy RabbitMQ from the App Store of Kuberix Enterprise and access its service.'
linkTitle: "Deploy RabbitMQ on Kuberix Enterprise"
weight: 14290
---
[RabbitMQ](https://www.rabbitmq.com/)는 가장 널리 배포된 오픈 소스 메시지 브로커입니다. 가볍고 온프레미스 및 클라우드에 배포하기 쉽습니다. 여러 메시징 프로토콜을 지원합니다. RabbitMQ는 대규모, 고가용성 요구 사항을 충족하기 위해 분산 및 연합 구성으로 배포할 수 있습니다.

이 튜토리얼은 Kuberix Enterprise의 앱 스토어에서 RabbitMQ를 배포하는 방법의 예를 안내합니다.

## 전제 조건

- [OpenPitrix 시스템 활성화](../../../pluggable-components/app-store/)를 확인하십시오.
- 이 튜토리얼에서는 작업 공간, 프로젝트 및 사용자 계정을 생성해야 합니다. 계정은 플랫폼 일반 사용자여야 하며 '운영자' 역할을 가진 프로젝트 운영자로 초대되어야 합니다. 이 튜토리얼에서는 `project-regular`로 로그인하여 `demo-workspace` 작업 공간의 `demo-project` 프로젝트에서 작업합니다. 자세한 내용은 [작업 공간, 프로젝트, 사용자 및 역할 생성](../../../quick-start/create-workspace-and-project/)을 참조하십시오.

## 실습 (핸즈온)

### 1단계: 앱 스토어에서 RabbitMQ 배포

1. `demo-project` 프로젝트의 **Overview** 페이지에서 왼쪽 상단의 **App Store**를 클릭합니다.

2. RabbitMQ를 찾아 **App Information** 페이지에서 **Install**를 클릭합니다.

3. 이름을 설정하고 앱 버전을 선택합니다. RabbitMQ가 `demo-project`에 배포되었는지 확인하고 **Next**를 클릭합니다.

4. **App Settings**에서 기본 설정을 직접 사용하거나 양식의 필드를 지정하거나 YAML 파일을 편집하여 설정을 사용자 지정할 수 있습니다. **Root Username** 값과 **Root Password** 값을 기록합니다. 이 값은 나중에 로그인에 사용됩니다. 계속하려면 **Install**를 클릭하세요.

   {{< notice note >}}

   매니페스트 파일을 보려면 **YAML Edit** 스위치를 토글합니다.

   {{</ notice >}}

5. RabbitMQ가 실행될 때까지 기다립니다.

### 2단계: RabbitMQ 대시보드에 액세스

클러스터 외부에서 RabbitMQ에 액세스하려면 먼저 NodePort를 통해 앱을 노출해야 합니다.

1. **Services**로 이동하여 RabbitMQ의 서비스 이름을 클릭합니다.

2. **More**를 클릭하고 드롭다운 목록에서 **Edit External Access**을 선택합니다.

3. **Access Method**으로 **NodePort**를 선택하고 **OK**을 클릭합니다. 자세한 내용은 [프로젝트 게이트웨이](../../../project-administration/project-gateway/)를 참조하십시오.

4. **Ports**에서 포트가 노출된 것을 볼 수 있습니다.

5. `<NodeIP>:<NodePort>`를 통해 RabbitMQ **management**에 액세스합니다. 사용자 이름과 비밀번호는 **Step 1**에서 설정한 것입니다.
   
   ![rabbitmq-dashboard](/images/docs/v3.3/appstore/built-in-apps/rabbitmq-app/rabbitmq-dashboard.png)

   ![rabbitma-dashboard-detail](/images/docs/v3.3/appstore/built-in-apps/rabbitmq-app/rabbitma-dashboard-detail.png)

   {{< notice note >}}

   쿠버네티스 클러스터가 배포된 위치에 따라 보안 그룹에서 포트를 열고 관련 포트 전달 규칙을 구성해야 할 수 있습니다.

   {{</ notice >}}

6. RabbitMQ에 대한 자세한 내용은 [RabbitMQ 공식 문서](https://www.rabbitmq.com/documentation.html)를 참조하세요.
