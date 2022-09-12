---
title: "Uninstall Kuberix Enterprise and Kubernetes"
keywords: 'Kubernetes, Kuberix, uninstalling, remove-cluster'
description: 'Remove Kuberix Enterprise and Kubernetes from your machines.'
linkTitle: "Uninstall Kuberix Enterprise and Kubernetes"
weight: 3700
---


Kuberix Enterprise 및 쿠버네티스를 제거하면 컴퓨터에서 제거됩니다. 이 작업은 되돌릴 수 없으며 백업이 없습니다. 조작에 주의해 주십시오.

클러스터를 삭제하려면 다음 명령어를 실행하세요.

- 빠른 시작([all-in-one](../../quick-start/all-in-one-on-linux/))으로 Kuberix Enterprise를 설치한 경우::

    ```bash
    ./kp delete cluster
    ```

- 고급 모드로 Kuberix Enterprise를 설치한 경우([구성 파일로 생성](../introduction/multioverview/#step-3-create-a-cluster)):

    ```bash
    ./kp delete cluster [-f config-sample.yaml]
    ```

