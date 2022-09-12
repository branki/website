---
title: "Volume Snapshot Classes"
keywords: 'Kuberix, Kubernetes, PVC, PV, Snapshot, Snapshot Classes'
description: 'Learn how to manage snapshot classes on Kuberix Enterprise.'
linkTitle: "Volume Snapshot Classes"
weight: 8900
---

## 전제 조건

- 작업 공간, 프로젝트 및 사용자(`project-regular`)를 생성해야 합니다. 사용자는 '운영자' 역할로 프로젝트에 초대되어야 합니다. 자세한 내용은 [작업 공간, 프로젝트, 사용자 및 역할 생성](../../../quick-start/create-workspace-and-project/)을 참조하십시오.

- 쿠버네티스 1.20 이상을 설치해야 합니다.

- 기본 저장소 플러그인은 스냅샷을 지원합니다.

## 절차

1. Kuberix Enterprise의 웹 콘솔에 'project-regular'로 로그인합니다. 왼쪽 탐색 창에서 **Storage > Volume Snapshot Classes**를 클릭합니다.

2. **Volume Snapshot Classes** 페이지에서 **Create**를 클릭합니다.

3. 표시된 **Create Volume Snapshot Class** 대화 상자에서 볼륨 스냅샷의 이름을 설정하고 **Next**를 클릭합니다. 또한 별칭을 설정하고 설명을 추가할 수 있습니다.

4. **Volume Snapshot Class Settings** 탭에서 다음 유형을 지원하는 프로비저닝 및 삭제 정책을 선택합니다.

   - 삭제: 기본 저장소의 스냅샷이 VolumeSnapshotContent와 함께 삭제됩니다.
   - 유지: 기본 스토리지의 스냅샷과 VolumeSnapshotContent가 모두 유지됩니다.

