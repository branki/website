---
title: 'Deploy Chaos Mesh on Kuberix Enterprise'
tag: 'Kuberix, Kubernetes, Applications, Chaos Engineering, Chaos experiments, Chaos Mesh'
keywords: 'Chaos Mesh, Kubernetes, Helm, kuberix'
description: 'Learn how to deploy Chaos Mesh on Kuberix Enterprise and start running chaos experiments.'
linkTitle: "Deploy Chaos Mesh on Kuberix Enterprise"
---

[카오스 메시](https://github.com/chaos-mesh/chaos-mesh)는 쿠버네티스 환경에서 혼돈을 오케스트레이션하는 클라우드 네이티브 카오스 엔지니어링 플랫폼입니다. 카오스 메시를 사용하면 다양한 유형의 결함을 Pod, 네트워크, 파일 시스템, 심지어 커널에 주입하여 쿠버네티스에서 시스템의 복원력과 견고성을 테스트할 수 있습니다.

![카오스 메시 아키텍처](/images/docs/v3.3/appstore/built-in-apps/deploy-chaos-mesh/chaos-mesh-architecture-v2.png)

## Kuberix Enterprise 에서 앱 스토어 활성화

1. [Kuberix Enterprise App Store](../../../pluggable-components/app-store/)를 설치하고 활성화했는지 확인합니다.

2. 이 튜토리얼에서는 작업 공간, 프로젝트 및 사용자 계정(project-regular)을 생성해야 합니다. 계정은 플랫폼 일반 사용자여야 하며 운영자 역할이 있는 프로젝트 운영자로 초대되어야 합니다. 자세한 내용은 [작업 공간, 프로젝트, 사용자 및 역할 생성](../../../quick-start/create-workspace-and-project/)을 참조하십시오.

## 카오스 메시를 사용한 카오스 실험

### 1단계: 카오스 메시 배포
  
1. Kuberix Enterprise에 `project-regular`로 로그인하고 **App Store**에서 **chaos-mesh**를 검색한 후 검색 결과를 클릭하여 앱에 진입합니다.
      
    ![카오스 메시 앱](/images/docs/v3.3/appstore/built-in-apps/deploy-chaos-mesh/chaos-mesh-app.png)
        
2. **앱 정보** 페이지에서 오른쪽 상단의 **설치**를 클릭합니다.

    ![카오스 메시 설치](/images/docs/v3.3/appstore/built-in-apps/deploy-chaos-mesh/install-chaos-mesh.png)
        
3. **앱 설정** 페이지에서 애플리케이션 **이름,** **위치**(네임스페이스로), **앱 버전**을 설정한 후 **다음**을 클릭합니다. 오른쪽 상단 모서리.

    ![카오스 메시 기본 정보](/images/docs/v3.3/appstore/built-in-apps/deploy-chaos-mesh/chaos-mesh-basic-info.png)

4. 필요에 따라 `values.yaml` 파일을 구성하거나 **설치**를 클릭하여 기본 구성을 사용합니다.

    ![카오스 메시 구성](/images/docs/v3.3/appstore/built-in-apps/deploy-chaos-mesh/chaos-mesh-config.png)

5. 배포가 완료될 때까지 기다립니다. 완료되면 Kuberix Enterprise에서 Chaos Mesh가 **Running**으로 표시됩니다.

    ![카오스 메시 배치](/images/docs/v3.3/appstore/built-in-apps/deploy-chaos-mesh/chaos-mesh-deployed.png)


### 2단계: 카오스 대시보드 방문

1. **Resource Status** 페이지에서 `카오스 대시보드`의 **NodePort **를 복사합니다.
       
    ![카오스 메시 노드포트](/images/docs/v3.3/appstore/built-in-apps/deploy-chaos-mesh/chaos-mesh-nodeport.png)

2. 브라우저에 `${NodeIP}:${NODEPORT}`를 입력하여 Chaos Dashboard에 액세스합니다. [사용자 권한 관리](https://chaos-mesh.org/docs/manage-user-permissions/)를 참조하여 토큰을 생성하고 카오스 대시보드에 로그인하십시오.

    ![카오스 대시보드에 로그인](/images/docs/v3.3/appstore/built-in-apps/deploy-chaos-mesh/login-to-dashboard.png)

### 3단계: 혼돈 실험 만들기

혼돈 실험을 생성하기 전에 예를 들어 네트워크 대기 시간에서 애플리케이션이 작동하는 방식을 테스트하기 위해 실험 대상을 식별하고 배포해야 합니다. 여기서는 테스트 대상 애플리케이션으로 데모 애플리케이션 'web-show'를 사용하며 테스트 목표는 시스템 네트워크 대기 시간을 관찰하는 것입니다. `web-show` 명령을 사용하여 데모 애플리케이션 `web-show`를 배포할 수 있습니다.

```배쉬
컬 -sSL https://mirrors.chaos-mesh.org/latest/web-show/deploy.sh | 세게 때리다
```
    
> 참고: Pod의 네트워크 대기 시간은 web-show 애플리케이션 패드에서 kube-system Pod로 직접 관찰할 수 있습니다.
    
1. 웹 브라우저에서 ${NodeIP}:8081을 방문하여 **Web Show** 애플리케이션에 액세스합니다.

    ![카오스 메시 웹 쇼 앱](/images/docs/v3.3/appstore/built-in-apps/deploy-chaos-mesh/web-show-app.png)

2. 카오스 대시보드에 로그인하여 카오스 실험을 생성합니다. 네트워크 지연이 애플리케이션에 미치는 영향을 관찰하기 위해 **Target **을 "Network Attack"으로 설정하여 네트워크 지연 시나리오를 시뮬레이션했습니다.

    ![카오스 대시보드](/images/docs/v3.3/appstore/built-in-apps/deploy-chaos-mesh/chaos-dashboard-networkchaos.png)
        
    실험의 **범위**는 `app: web-show`로 설정됩니다.

    ![카오스 실험 범위](/images/docs/v3.3/appstore/built-in-apps/deploy-chaos-mesh/chaos-experiment-scope.png)
        
3. 제출하여 카오스 실험을 시작합니다.

    ![카오스 실험 제출](/images/docs/v3.3/appstore/built-in-apps/deploy-chaos-mesh/start-chaos-experiment.png)

이제 **Web Show**를 방문하여 실험 결과를 관찰할 수 있습니다.

![카오스 실험 결과](/images/docs/v3.3/appstore/built-in-apps/deploy-chaos-mesh/experiment-result.png)
