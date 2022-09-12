---
title: "Set up an HA Kubernetes Cluster Using Keepalived and HAproxy"
keywords: 'Kubernetes, Kuberix, HA, high availability, installation, configuration, Keepalived, HAproxy'
description: 'Learn how to create a highly available cluster using Keepalived and HAproxy.'
linkTitle: "Set up an HA Cluster Using Keepalived and HAproxy"
weight: 3230
showSubscribe: true
---

고가용성 쿠버네티스 클러스터는 프로덕션에 필요한 중단 없이 애플리케이션이 실행되도록 합니다. 이와 관련하여 고가용성을 달성하기 위해 선택할 수 있는 많은 방법이 있습니다.

이 자습서는 부하 분산을 위해 Keepalived 및 HAproxy를 구성하고 고가용성을 달성하는 방법을 보여줍니다. 단계는 다음과 같습니다.

1. 호스트를 준비합니다.
2. Keepalive 및 HAproxy를 구성합니다.
3. KubePOP를 사용하여 쿠버네티스 클러스터를 설정하고 Kuberix Enterprise를 설치합니다.

## 클러스터 아키텍처

예제 클러스터에는 마스터 노드 3개, 작업자 노드 3개, 로드 밸런싱을 위한 노드 2개, 가상 IP 주소 1개가 있습니다. 이 예에서 가상 IP 주소는 "유동 IP 주소"라고도 합니다. 즉, 노드 오류가 발생하는 경우 노드 간에 IP 주소를 전달하여 장애 조치를 허용하여 고가용성을 달성할 수 있습니다.

![architecture-ha-k8s-cluster](/images/docs/v3.3/installing-on-linux/high-availability-configurations/set-up-ha-cluster-using-keepalived-haproxy/architecture-ha-k8s-cluster.png)

이 예에서 Keepalived 및 HAproxy는 마스터 노드에 설치되어 있지 않습니다. 물론 그렇게 할 수 있으며 고가용성도 달성할 수 있습니다. 즉, 로드 밸런싱을 위해 두 개의 특정 노드를 구성하는 것이 더 안전합니다(필요에 따라 이러한 종류의 노드를 더 추가할 수 있음). Keepalived 및 HAproxy만 이 두 노드에 설치되어 쿠버네티스 구성 요소 및 서비스와의 잠재적 충돌을 방지합니다.

## 호스트 준비

| IP Address  | Hostname | Role                 |
| ----------- | -------- | -------------------- |
| 172.16.0.2  | lb1      | Keepalived & HAproxy |
| 172.16.0.3  | lb2      | Keepalived & HAproxy |
| 172.16.0.4  | master1  | master, etcd         |
| 172.16.0.5  | master2  | master, etcd         |
| 172.16.0.6  | master3  | master, etcd         |
| 172.16.0.7  | worker1  | worker               |
| 172.16.0.8  | worker2  | worker               |
| 172.16.0.9  | worker3  | worker               |
| 172.16.0.10 |          | Virtual IP address   |

노드, 네트워크 및 종속성에 대한 요구 사항에 대한 자세한 내용은 [다중 노드 설치](../../../installing-on-linux/introduction/multioverview/#step-1-prepare-linux-hosts)를 참조하십시오. ).

## 로드 밸런싱 구성

[Keepalived](https://www.keepalived.org/)는 VRPP 구현을 제공하고 로드 밸런싱을 위해 Linux 시스템을 구성하여 단일 실패 지점을 방지할 수 있습니다. 안정적인 고성능 로드 밸런싱을 제공하는 [HAProxy](https://www.haproxy.org/)는 Keepalived와 완벽하게 작동합니다.

Keepalived와 HAproxy가 `lb1`과 `lb2`에 설치되어 있기 때문에 둘 중 하나가 다운되면 가상 IP 주소(즉, 유동 IP 주소)가 자동으로 다른 노드와 연결되어 클러스터가 여전히 잘 작동하므로 다음을 달성할 수 있습니다. 고가용성. 원하는 경우 Keepalived 및 HAproxy가 해당 목적으로 설치된 노드를 더 추가할 수 있습니다.

다음 명령을 실행하여 Keepalived 및 HAproxy를 먼저 설치합니다.

```bash
yum install keepalived haproxy psmisc -y
```

### HAproxy 구성

1. HAproxy의 구성은 로드 밸런싱을 위해 두 시스템에서 정확히 동일합니다. 다음 명령을 실행하여 HAproxy를 구성합니다.

   ```bash
   vi /etc/haproxy/haproxy.cfg
   ```

2. 다음은 참조용 구성의 예입니다(`server` 필드에 주의하십시오. `6443`은 `apiserver` 포트임에 유의하십시오):

   ```bash
   global
       log /dev/log  local0 warning
       chroot      /var/lib/haproxy
       pidfile     /var/run/haproxy.pid
       maxconn     4000
       user        haproxy
       group       haproxy
       daemon
   
      stats socket /var/lib/haproxy/stats
   
   defaults
     log global
     option  httplog
     option  dontlognull
           timeout connect 5000
           timeout client 50000
           timeout server 50000
   
   frontend kube-apiserver
     bind *:6443
     mode tcp
     option tcplog
     default_backend kube-apiserver
   
   backend kube-apiserver
       mode tcp
       option tcplog
       option tcp-check
       balance roundrobin
       default-server inter 10s downinter 5s rise 2 fall 2 slowstart 60s maxconn 250 maxqueue 256 weight 100
       server kube-apiserver-1 172.16.0.4:6443 check # Replace the IP address with your own.
       server kube-apiserver-2 172.16.0.5:6443 check # Replace the IP address with your own.
       server kube-apiserver-3 172.16.0.6:6443 check # Replace the IP address with your own.
   ```

3. 파일을 저장하고 다음 명령을 실행하여 HAproxy를 다시 시작합니다.

   ```bash
   systemctl restart haproxy
   ```

4. 재부팅 후에도 유지되도록 합니다.

   ```bash
   systemctl enable haproxy
   ```

5. 다른 머신(`lb2`)에서도 HAproxy를 구성해야 합니다.

### 연결 유지 구성

Keepalived는 구성이 약간 다르지만 두 시스템에 모두 설치되어야 합니다.

1. 다음 명령을 실행하여 Keepalive를 구성합니다.

   ```bash
   vi /etc/keepalived/keepalived.conf
   ```

2. 다음은 참조용 구성(`lb1`)의 예입니다.:

   ```bash
   global_defs {
     notification_email {
     }
     router_id LVS_DEVEL
     vrrp_skip_check_adv_addr
     vrrp_garp_interval 0
     vrrp_gna_interval 0
   }
   
   vrrp_script chk_haproxy {
     script "killall -0 haproxy"
     interval 2
     weight 2
   }
   
   vrrp_instance haproxy-vip {
     state BACKUP
     priority 100
     interface eth0                       # Network card
     virtual_router_id 60
     advert_int 1
     authentication {
       auth_type PASS
       auth_pass 1111
     }
     unicast_src_ip 172.16.0.2      # The IP address of this machine
     unicast_peer {
       172.16.0.3                         # The IP address of peer machines
     }
   
     virtual_ipaddress {
       172.16.0.10/24                  # The VIP address
     }
   
     track_script {
       chk_haproxy
     }
   }
   ```

   {{< notice note >}} 

    - '인터페이스' 필드에는 자신의 네트워크 카드 정보를 제공해야 합니다. 값을 얻기 위해 머신에서 `ifconfig`를 실행할 수 있습니다.
    - `unicast_src_ip`에 제공된 IP 주소는 현재 컴퓨터의 IP 주소입니다. 로드 밸런싱을 위해 HAproxy 및 Keepalived도 설치된 다른 시스템의 경우 'unicast_peer' 필드에 해당 IP 주소를 제공해야 합니다.

     {{</ notice >}} 

3. 파일을 저장하고 다음 명령을 실행하여 Keepalive를 다시 시작합니다.

   ```bash
   systemctl restart keepalived
   ```

4. 재부팅 후에도 유지되도록 합니다.:

   ```bash
   systemctl enable keepalived
   ```

5. 다른 머신(`lb2`)에서도 Keepalived를 구성했는지 확인하십시오.

## 고가용성 확인

쿠버네티스 클러스터 생성을 시작하기 전에 고가용성을 테스트했는지 확인하십시오.

1. `lb1` 머신에서 다음 명령을 실행합니다.

   ```bash
   [root@lb1 ~]# ip a s
   1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
       link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
       inet 127.0.0.1/8 scope host lo
          valid_lft forever preferred_lft forever
       inet6 ::1/128 scope host
          valid_lft forever preferred_lft forever
   2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc mq state UP group default qlen 1000
       link/ether 52:54:9e:27:38:c8 brd ff:ff:ff:ff:ff:ff
       inet 172.16.0.2/24 brd 172.16.0.255 scope global noprefixroute dynamic eth0
          valid_lft 73334sec preferred_lft 73334sec
       inet 172.16.0.10/24 scope global secondary eth0 # The VIP address
          valid_lft forever preferred_lft forever
       inet6 fe80::510e:f96:98b2:af40/64 scope link noprefixroute
          valid_lft forever preferred_lft forever
   ```

2. 위와 같이 가상 IP 주소가 성공적으로 추가되었습니다. 이 노드에서 오류를 시뮬레이션합니다.:

   ```bash
   systemctl stop haproxy
   ```

3. 유동 IP 주소를 다시 확인하면 `lb1`에서 사라지는 것을 확인할 수 있습니다.

   ```bash
   [root@lb1 ~]# ip a s
   1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
       link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
       inet 127.0.0.1/8 scope host lo
          valid_lft forever preferred_lft forever
       inet6 ::1/128 scope host
          valid_lft forever preferred_lft forever
   2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc mq state UP group default qlen 1000
       link/ether 52:54:9e:27:38:c8 brd ff:ff:ff:ff:ff:ff
       inet 172.16.0.2/24 brd 172.16.0.255 scope global noprefixroute dynamic eth0
          valid_lft 72802sec preferred_lft 72802sec
       inet6 fe80::510e:f96:98b2:af40/64 scope link noprefixroute
          valid_lft forever preferred_lft forever
   ```

4. 이론적으로 가상 IP는 구성이 성공하면 다른 머신(`lb2`)으로 장애 조치됩니다. `lb2`에서 다음 명령을 실행하고 예상되는 출력은 다음과 같습니다.:

   ```bash
   [root@lb2 ~]# ip a s
   1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
       link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
       inet 127.0.0.1/8 scope host lo
          valid_lft forever preferred_lft forever
       inet6 ::1/128 scope host
          valid_lft forever preferred_lft forever
   2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc mq state UP group default qlen 1000
       link/ether 52:54:9e:3f:51:ba brd ff:ff:ff:ff:ff:ff
       inet 172.16.0.3/24 brd 172.16.0.255 scope global noprefixroute dynamic eth0
          valid_lft 72690sec preferred_lft 72690sec
       inet 172.16.0.10/24 scope global secondary eth0   # The VIP address
          valid_lft forever preferred_lft forever
       inet6 fe80::f67c:bd4f:d6d5:1d9b/64 scope link noprefixroute
          valid_lft forever preferred_lft forever
   ```

5. 위에서 볼 수 있듯이 고가용성이 성공적으로 구성되었습니다.

## KubePOP를 사용하여 쿠버네티스 클러스터 생성

[KubePOP](https://github.com/ke/kubepop)는 쿠버네티스 클러스터를 생성하는 효율적이고 편리한 도구입니다. 아래 단계에 따라 KubePOP를 다운로드하십시오

{{< tabs >}}

{{< tab "Good network connections to GitHub/Googleapis" >}}

[GitHub 릴리스 페이지](https://github.com/ke/kubepop/releases)에서 KubePOP를 다운로드하거나 다음 명령을 직접 사용합니다.

```bash
curl -sfL https://get-kp.kuberix.io | VERSION=v2.2.2 sh -
```

{{</ tab >}}

{{< tab "Poor network connections to GitHub/Googleapis" >}}

다음 명령을 먼저 실행하여 올바른 영역에서 KubePOP를 다운로드했는지 확인하십시오.

```bash
export KPZONE=cn
```

다음 명령을 실행하여 KubePOP를 다운로드하십시오.:

```bash
curl -sfL https://get-kp.kuberix.io | VERSION=v2.2.2 sh -
```

{{< notice note >}}

KubePOP를 다운로드한 후 Googleapis에 대한 네트워크 연결이 좋지 않은 새 컴퓨터로 전송하는 경우 아래 단계를 진행하기 전에 `export KPZONE=cn`을 다시 실행해야 합니다.

{{</ notice >}} 

{{</ tab >}}

{{</ tabs >}}

{{< notice note >}}

위의 명령은 KubePOP의 최신 릴리스(v2.2.2)를 다운로드합니다. 명령에서 버전 번호를 변경하여 특정 버전을 다운로드할 수 있습니다.

{{</ notice >}} 

`kp` 파일을 실행 가능하게 만듭니다.:

```bash
chmod +x kp
```

기본 구성으로 예제 구성 파일을 만듭니다. 여기에서 쿠버네티스 v1.22.10이 예로 사용됩니다.

```bash
./kp create config --with-kuberixEnterprise v3.3.0 --with-kubernetes v1.22.10
```

{{< notice note >}}

- Kuberix Enterprise 3.3.0의 권장 쿠버네티스 버전: v1.19.x, v1.20.x, v1.21.x, v1.22.x 및 v1.23.x(실험 지원). 쿠버네티스 버전을 지정하지 않으면 KubePOP는 기본적으로 쿠버네티스 v1.23.7을 설치합니다. 지원되는 쿠버네티스 버전에 대한 자세한 내용은 [지원 매트릭스](../../../installing-on-linux/introduction/kubepop/#support-matrix)를 참조하십시오.

- 이 단계에서 명령에 `--with-kuberixEnterprise` 플래그를 추가하지 않으면 구성 파일의 `addons` 필드를 사용하여 설치하거나 `를 사용할 때 이 플래그를 다시 추가하지 않는 한 Kuberix Enterprise가 배포되지 않습니다. ./kp는 나중에 클러스터를 생성합니다.
- Kuberix Enterprise 버전을 지정하지 않고 `--with-kuberixEnterprisee` 플래그를 추가하면 최신 버전의 Kuberix Enterprise가 설치됩니다.

{{</ notice >}}

## Kuberix Enterprise 및 쿠버네티스 배포

위의 명령어를 실행하면 'config-sample.yaml' 구성 파일이 생성됩니다. 파일을 편집하여 시스템 정보를 추가하고 로드 밸런서를 구성하는 등의 작업을 수행합니다.

{{< notice note >}}

사용자 정의하면 파일 이름이 다를 수 있습니다.

{{</ notice >}} 

### config-sample.yaml 

```yaml
...
spec:
  hosts:
  - {name: master1, address: 172.16.0.4, internalAddress: 172.16.0.4, user: root, password: Testing123}
  - {name: master2, address: 172.16.0.5, internalAddress: 172.16.0.5, user: root, password: Testing123}
  - {name: master3, address: 172.16.0.6, internalAddress: 172.16.0.6, user: root, password: Testing123}
  - {name: worker1, address: 172.16.0.7, internalAddress: 172.16.0.7, user: root, password: Testing123}
  - {name: worker2, address: 172.16.0.8, internalAddress: 172.16.0.8, user: root, password: Testing123}
  - {name: worker3, address: 172.16.0.9, internalAddress: 172.16.0.9, user: root, password: Testing123}
  roleGroups:
    etcd:
    - master1
    - master2
    - master3
    control-plane:
    - master1
    - master2
    - master3
    worker:
    - worker1
    - worker2
    - worker3
  controlPlaneEndpoint:
    domain: lb.kuberix.local
    address: 172.16.0.10   # The VIP address
    port: 6443
...
```

{{< notice note >}}

- `controlPlaneEndpoint.address`의 값을 자신의 VIP 주소로 바꿉니다.
- 이 구성 파일의 다른 매개변수에 대한 자세한 내용은 [멀티 노드 설치](../../../installing-on-linux/introduction/multioverview/#2-edit-the-configuration-file)를 참조하십시오. .

{{</ notice >}} 

### 설치 시작

구성을 완료한 후 다음 명령을 실행하여 설치를 시작할 수 있습니다.:

```bash
./kp create cluster -f config-sample.yaml
```

### 설치 확인

1. 다음 명령어를 실행하여 설치 로그를 확인합니다.

   ```bash
   kubectl logs -n ke-system $(kubectl get pod -n ke-system -l 'app in (ke-install, ke-installer)' -o jsonpath='{.items[0].metadata.name}') -f
   ```

2. 다음 메시지가 표시되면 HA 클러스터가 성공적으로 생성되었음을 의미합니다.

   ```bash
   #####################################################
   ###          Welcome to Kuberix Enterprise!       ###
   #####################################################
   
   Console: http://172.16.0.4:30880
   Account: admin
   Password: P@88w0rd
   
   NOTES：
     1. After you log into the console, please check the
        monitoring status of service components in
        the "Cluster Management". If any service is not
        ready, please wait patiently until all components
        are up and running.
     2. Please change the default password after login.
   
   #####################################################
   https://kuberix.io                2020-xx-xx xx:xx:xx
   #####################################################
   ```
