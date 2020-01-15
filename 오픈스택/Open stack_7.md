# openstack

## Nova란

: Nova는 오픈 스택 프로젝트 중 하나이며, 컴퓨트 인스턴스(가상 서버) 프로비져닝 서비스를 제공합니다. Nova는 기본적으로 가상 머신의 생성과 Ironic을 통한 베어 메탈 서버의 생성을 지원하고, 제한적이지만 시스템 컨테이너도 지원합니다. Nova는 리눅스 서버 상에서 여러 개의 데몬으로 움직이며 서비스를 제공합니다.

Nova가 기본적인 서비스를 위해서는 다음과 같은 최소의 오픈스택 서비스가 필요하다.

- Keystone:  ID를 발급하고 인증 서비스를 제공 
- Glance: 컴퓨트 이미지 리포지토리를 제공. 모든 컴퓨트 인스턴스는 Glance 이미지로 생성
- Neutron: 컴퓨트 인스턴스가 사용하는 가상 네트워크와 물리적 네트워크를 제공 



### nova 설치

*참고

https://docs.openstack.org/nova/rocky/install/compute-install-rdo.html - CentOS를위한 컴퓨팅 노드 설치 및 구성

```
이 섹션에서는 compute 노드에서 compute 서비스를 설치하고 구성하는 방법에 대해 설명합니다. 이 서비스는 여러 하이퍼 바이저를 지원하여 인스턴스 또는 가상 머신 (VM)을 배포합니다. 간단하게 하기 위해 이 구성은 가상 머신의 하드웨어 가속을 지원하는 컴퓨팅 노드에서 커널 기반 VM (KVM) 확장이있는 QEMU (Quick EMUlator) 하이퍼 바이저를 사용합니다. 레거시 하드웨어에서이 구성은 일반 QEMU 하이퍼 바이저를 사용합니다. 추가 계산 노드로 환경을 수평으로 확장하기 위해 약간의 수정으로이 지침을 따를 수 있습니다.
```

: 인스턴스를 클라이언트에게 제공하는 컴퓨트 서비스

#### vm ware에 compute1 새 가상머신 만들기

<img src="images/image-20200114102223792.png" alt="image-20200114102223792" style="zoom:80%;" />

<img src="images/image-20200114102254678.png" alt="image-20200114102254678" style="zoom:80%;" />

​	=> openstack - compute1에 있는 파일을 이용하기 위해 working directory로  compute1폴더 지정



* vm ware 

compute1에 접속 후

```bash
[root@controller ~]# vi /etc/sysconfig/network-scripts/ifcfg-ens33
	//'/etc/sysconfig/network-scripts/ifcfg-ens33' 파일을 열어 UUID주석처리, IP주소 수정 
	//UUID=
	//IPADDR="10.0.0.101"
[root@controller ~]#systemctl restart network
[root@controller ~]#ip a
	//network를 재시작하고 ip주소를 확인해보면 정상적으로 수정된 것을 확인할 수 있다
[root@controller ~]# hostnamectl set-hostname compute
	//hostname을 compute로 변경하고 재 접속 한다.
```



* X-shell - [compute1]

```shell
[root@compute ~]# yum install openstack-nova-compute -y
.
.
.
[root@compute ~]#  cp /etc/nova/nova.conf /etc/nova/nova.conf.old

[root@compute ~]#  scp controller:/etc/nova/nova.conf /etc/nova
The authenticity of host 'controller (10.0.0.100)' can't be established.
ECDSA key fingerprint is SHA256:aWeJipExkgBiXCJ7e+I1eI9uJ1jJKqKuZR1oHxI34Q4.
ECDSA key fingerprint is MD5:09:43:ee:dd:d9:b1:d1:e7:0f:f4:ad:2c:79:e1:fe:69.
Are you sure you want to continue connecting (yes/no)? yes
...

[root@compute ~]#  ls -l /etc/nova/nova.conf
-rw-r-----. 1 root nova 394479  1월 14 09:58 /etc/nova/nova.conf

[root@compute ~]#  vi /etc/nova/nova.conf
	//'/etc/nova/nova.conf' 파일을 열어 수정
	//1254 my_ip=10.0.0.101
  	//11017 vncserver_proxyclient_address=10.0.0.101
```

```shell
[root@compute ~]# systemctl enable libvirtd.service openstack-nova-compute.service

[root@compute ~]# systemctl start libvirtd.service

[root@compute ~]# systemctl start  openstack-nova-compute.service (starting 오류)
	//방화벽 오류
```

* X-shell - [cotroller]

```shell
[root@controller ~]# vi /etc/sysconfig/iptables
	//13번 아래에 추가
	//-A INPUT -s 10.0.0.101/32 -p tcp -m multiport --dports 5671,5672 -m comment --		  comment "001 amqp incoming amqp_10.0.0.101" -j ACCEPT
	//-A INPUT -s 10.0.0.101/32 -p tcp -m multiport --dports 5671,5672 -j ACCEPT
	//-A INPUT -s 10.0.0.100/32 -p tcp -m multiport --dports 5671,5672 -j ACCEPT

[root@controller ~]# systemctl reload iptables
[root@controller ~]# . keystonerc_admin 
[root@controller ~(keystone_admin)]# openstack compute service list --service nova-compute
+----+--------------+------------+------+---------+-------+----------------------------+
| ID | Binary       | Host       | Zone | Status  | State | Updated At                 |
+----+--------------+------------+------+---------+-------+----------------------------+
|  6 | nova-compute | controller | nova | enabled | up    | 2020-01-14T01:07:57.000000 |
|  7 | nova-compute | compute    | nova | enabled | up    | 2020-01-14T01:07:59.000000 |
+----+--------------+------------+------+---------+-------+----------------------------+
```



### neutron

:오픈스택의 구성요소 중 네트워크 서비스

:오픈스택 클라우드에서 가상 네트워크 인프라를 생성하고 관리할 수 있도록 허용하는 서비스

:Nova 서비스가 생성하는 인스턴스에게 가상 네트워크 서비스를 제공하는 역할을 한다. 인스턴스에게 제공된 네트워크 정보를 통해 클라이언트는 인스턴스에 직접 접근할 수 있으며, 그 서비스 제어도 가능하다.



*참고

https://docs.openstack.org/neutron/rocky/install/



* X-shell - [compute1]

```shell
[root@compute ~]# ip netns

[root@compute ~]# yum install openstack-neutron-linuxbridge ebtables ipset
Loaded plugins: fastestmirror
Loading mirror speeds from cached hostfile
...
Complete!

[root@compute ~]# cd /etc/neutron/

[root@compute neutron]# ls
conf.d  neutron.conf  plugins  rootwrap.conf

[root@compute neutron]# cp neutron.conf neutron.conf.old

[root@compute neutron]# scp controller:/etc/neutron/neutron.conf neutron.conf
root@controller's password: 
neutron.conf                                                                                 100%   71KB  15.9MB/s   00:00    

[root@compute neutron]# vi /etc/neutron/neutron.conf
	//761 #connection=mysql+pymysql://neutron:9e2064f267fd4602@10.0.0.100/neutron

[root@compute neutron]# vi /etc/neutron/plugins/ml2/linuxbridge_agent.ini
	//146 [linux_bridge]
   	  147 physical_interface_mappings = provider:ens33
	  205 [vxlan]
      206 enable_vxlan = true
      207 local_ip = 10.0.0.101
      208 l2_population = true
      182 [securitygroup]
      183 enable_security_group = true
      184 firewall_driver = neutron.agent.linux.iptables_firewall.IptablesFirewallDriver
     
[root@compute neutron]# modprobe br_netfilter

[root@compute neutron]# lsmod|grep br_netfilter
br_netfilter           22256  0 
bridge                151336  1 br_netfilter

[root@compute neutron]# sysctl -a|grep nf-call
net.bridge.bridge-nf-call-arptables = 0
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
sysctl: reading key "net.ipv6.conf.all.stable_secret"
sysctl: reading key "net.ipv6.conf.default.stable_secret"
sysctl: reading key "net.ipv6.conf.ens33.stable_secret"
sysctl: reading key "net.ipv6.conf.lo.stable_secret"

[root@compute neutron]# systemctl enable neutron-linuxbridge-agent.service
Created symlink from /etc/systemd/system/multi-user.target.wants/neutron-linuxbridge-agent.service to /usr/lib/systemd/system/neutron-linuxbridge-agent.service.

[root@compute neutron]# systemctl start neutron-linuxbridge-agent.service

[root@compute neutron]# systemctl status neutron-linuxbridge-agent.service
neutron-linuxbridge-agent.service - OpenStack Neutron Linux Bridge Agent
Loaded: loaded (/usr/lib/systemd/system/neutron-linuxbridge-agent.service; enabled; vendor preset: disabled)
Active: active (running) since 화 2020-01-14 14:23:55 KST; 2s ago
...

```

* X-shell - [controller]

```bash
[root@controller ~(keystone_admin)]# openstack network agent list
	// 'Linux bridge agent' agent-type 올라온 것 확인 가능
+--------------------------------------+--------------------+------------+-------------------+-------+-------+---------------------------+
| ID                                   | Agent Type         | Host       | Availability Zone | Alive | State | Binary                    |
+--------------------------------------+--------------------+------------+-------------------+-------+-------+---------------------------+
| 0fc03641-6cba-4bce-9c6a-accae6876dfb | L3 agent           | controller | nova              | :-)   | UP    | neutron-l3-agent          |
| 83985104-5138-4fef-9ae0-f179e9c8d1bc | Linux bridge agent | compute    | None              | :-)   | UP    | neutron-linuxbridge-agent |
| 90c53ec2-76d7-48e1-9fe0-8ce6b2d85ba8 | Metadata agent     | controller | None              | :-)   | UP    | neutron-metadata-agent    |
| b479d1b5-7dd6-4de3-90e6-ecfa8ec359bf | Open vSwitch agent | controller | None              | :-)   | UP    | neutron-openvswitch-agent |
| c757816a-22f0-4eb8-a61e-e5d883cba982 | Metering agent     | controller | None              | :-)   | UP    | neutron-metering-agent    |
| c82a5ef9-7c67-461f-aca1-7fbb2e76e761 | DHCP agent         | controller | nova              | :-)   | UP    | neutron-dhcp-agent        |
+--------------------------------------+--------------------+------------+-------------------+-------+-------+---------------------------+
```



### CLI로 인스턴스 시작

#### 가상 네트워크 생성

```shell
[root@controller ~(keystone_admin)]# openstack network create selfservice

Created a new network:
+-------------------------+--------------------------------------+
| Field                   | Value                                |
+-------------------------+--------------------------------------+
| admin_state_up          | UP                                   |
| availability_zone_hints |                                      |
| availability_zones      |                                      |
| created_at              | 2016-11-04T18:20:59Z                 |
| description             |                                      |
| headers                 |                                      |
| id                      | 7c6f9b37-76b4-463e-98d8-27e5686ed083 |
| ipv4_address_scope      | None                                 |
| ipv6_address_scope      | None                                 |
| mtu                     | 1450                                 |
| name                    | selfservice                          |
| port_security_enabled   | True                                 |
| project_id              | 3828e7c22c5546e585f27b9eb5453788     |
| project_id              | 3828e7c22c5546e585f27b9eb5453788     |
| revision_number         | 3                                    |
| router:external         | Internal                             |
| shared                  | False                                |
| status                  | ACTIVE                               |
| subnets                 |                                      |
| tags                    | []                                   |
| updated_at              | 2016-11-04T18:20:59Z                 |
+-------------------------+--------------------------------------+

[root@controller ~(keystone_admin)]# openstack subnet create --network selfservice \
  --dns-nameserver 8.8.4.4 --gateway 172.16.1.1 \
  --subnet-range 172.16.1.0/24 selfservice

Created a new subnet:
+-------------------+--------------------------------------+
| Field             | Value                                |
+-------------------+--------------------------------------+
| allocation_pools  | 172.16.1.2-172.16.1.254              |
| cidr              | 172.16.1.0/24                        |
| created_at        | 2016-11-04T18:30:54Z                 |
| description       |                                      |
| dns_nameservers   | 8.8.4.4                              |
| enable_dhcp       | True                                 |
| gateway_ip        | 172.16.1.1                           |
| headers           |                                      |
| host_routes       |                                      |
| id                | 5c37348e-e7da-439b-8c23-2af47d93aee5 |
| ip_version        | 4                                    |
| ipv6_address_mode | None                                 |
| ipv6_ra_mode      | None                                 |
| name              | selfservice                          |
| network_id        | b9273876-5946-4f02-a4da-838224a144e7 |
| project_id        | 3828e7c22c5546e585f27b9eb5453788     |
| project_id        | 3828e7c22c5546e585f27b9eb5453788     |
| revision_number   | 2                                    |
| service_types     | []                                   |
| subnetpool_id     | None                                 |
| updated_at        | 2016-11-04T18:30:54Z                 |
+-------------------+--------------------------------------+


[root@controller ~(keystone_admin)]# openstack router create router
Created a new router:
+-------------------------+--------------------------------------+
| Field                   | Value                                |
+-------------------------+--------------------------------------+
| admin_state_up          | UP                                   |
| availability_zone_hints |                                      |
| availability_zones      |                                      |
| created_at              | 2016-11-04T18:32:56Z                 |
| description             |                                      |
| external_gateway_info   | null                                 |
| flavor_id               | None                                 |
| headers                 |                                      |
| id                      | 67324374-396a-4db6-9443-c70be167a42b |
| name                    | router                               |
| project_id              | 3828e7c22c5546e585f27b9eb5453788     |
| project_id              | 3828e7c22c5546e585f27b9eb5453788     |
| revision_number         | 2                                    |
| routes                  |                                      |
| status                  | ACTIVE                               |
| updated_at              | 2016-11-04T18:32:56Z                 |
+-------------------------+--------------------------------------+

[root@controller ~(keystone_admin)]# openstack router add subnet router selfservice

[root@controller ~(keystone_admin)]# openstack router set router --external-gateway ext1

[root@controller ~(keystone_admin)]# openstack port list --router router

+--------------------------------------+------+-------------------+-------------------------------------------------------------------------------+--------+
| ID                                   | Name | MAC Address       | Fixed IP Addresses                                                            | Status |
+--------------------------------------+------+-------------------+-------------------------------------------------------------------------------+--------+
| bff6605d-824c-41f9-b744-21d128fc86e1 |      | fa:16:3e:2f:34:9b | ip_address='172.16.1.1', subnet_id='3482f524-8bff-4871-80d4-5774c2730728'     | ACTIVE |
| d6fe98db-ae01-42b0-a860-37b1661f5950 |      | fa:16:3e:e8:c1:41 | ip_address='203.0.113.102', subnet_id='5cc70da8-4ee7-4565-be53-b9c011fca011'  | ACTIVE |
+--------------------------------------+------+-------------------+-------------------------------------------------------------------------------+--------+
```

#### m1.nano flavor 생성

```bash
[root@controller ~(keystone_admin)]# openstack flavor create --id 0 --vcpus 1 --ram 64 --disk 1 m1.nano
+----------------------------+---------+
| Field                      | Value   |
+----------------------------+---------+
| OS-FLV-DISABLED:disabled   | False   |
| OS-FLV-EXT-DATA:ephemeral  | 0       |
| disk                       | 1       |
| id                         | 0       |
| name                       | m1.nano |
| os-flavor-access:is_public | True    |
| properties                 |         |
| ram                        | 64      |
| rxtx_factor                | 1.0     |
| swap                       |         |
| vcpus                      | 1       |
+----------------------------+---------+
```

#### 키 페어 생성

```bash
[root@controller ~(keystone_admin)]# openstack keypair create --public-key ~/.ssh/id_rsa.pub mykey

+-------------+-------------------------------------------------+
| Field       | Value                                           |
+-------------+-------------------------------------------------+
| fingerprint | ee:3d:2e:97:d4:e2:6a:54:6d:0d:ce:43:39:2c:ba:4d |
| name        | mykey                                           |
| user_id     | 58126687cbcc4888bfa9ab73a2256f27                |
+-------------+-------------------------------------------------+

[root@controller ~(keystone_admin)]# openstack keypair list

+-------+-------------------------------------------------+
| Name  | Fingerprint                                     |
+-------+-------------------------------------------------+
| mykey | ee:3d:2e:97:d4:e2:6a:54:6d:0d:ce:43:39:2c:ba:4d |
+-------+-------------------------------------------------+
```

#### security group rule 생성

```shell
[root@controller ~(keystone_admin)]# openstack security group rule create --proto icmp default
+-------------------+--------------------------------------+
| Field             | Value                                |
+-------------------+--------------------------------------+
| created_at        | 2017-03-30T00:46:43Z                 |
| description       |                                      |
| direction         | ingress                              |
| ether_type        | IPv4                                 |
| id                | 1946be19-54ab-4056-90fb-4ba606f19e66 |
| name              | None                                 |
| port_range_max    | None                                 |
| port_range_min    | None                                 |
| project_id        | 3f714c72aed7442681cbfa895f4a68d3     |
| protocol          | icmp                                 |
| remote_group_id   | None                                 |
| remote_ip_prefix  | 0.0.0.0/0                            |
| revision_number   | 1                                    |
| security_group_id | 89ff5c84-e3d1-46bb-b149-e621689f0696 |
| updated_at        | 2017-03-30T00:46:43Z                 |
+-------------------+--------------------------------------+

[root@controller ~(keystone_admin)]# openstack security group rule create --proto tcp --dst-port 22 default
+-------------------+--------------------------------------+
| Field             | Value                                |
+-------------------+--------------------------------------+
| created_at        | 2017-03-30T00:43:35Z                 |
| description       |                                      |
| direction         | ingress                              |
| ether_type        | IPv4                                 |
| id                | 42bc2388-ae1a-4208-919b-10cf0f92bc1c |
| name              | None                                 |
| port_range_max    | 22                                   |
| port_range_min    | 22                                   |
| project_id        | 3f714c72aed7442681cbfa895f4a68d3     |
| protocol          | tcp                                  |
| remote_group_id   | None                                 |
| remote_ip_prefix  | 0.0.0.0/0                            |
| revision_number   | 1                                    |
| security_group_id | 89ff5c84-e3d1-46bb-b149-e621689f0696 |
| updated_at        | 2017-03-30T00:43:35Z                 |
+-------------------+--------------------------------------+
```

####  인스턴스 시작

```shell
[root@controller ~(keystone_admin)]# yum install -y wget
Loaded plugins: fastestmirror
Loading mirror speeds from cached hostfile
...
Complete!

[root@controller ~(keystone_admin)]# wget http://download.cirros-cloud.net/0.3.5/cirros-0.3.5-x86_64-disk.img
...
2020-01-14 15:36:48 (2.57 MB/s) - ‘cirros-0.3.5-x86_64-disk.img’ saved [13267968/13267968]

[root@controller ~(keystone_admin)]# openstack image list
+--------------------------------------+------------+--------+
| ID                                   | Name       | Status |
+--------------------------------------+------------+--------+
| 67adccb1-a464-468b-b1c9-51356cae3876 | class      | active |
| 98a02ae4-795a-48e3-8101-a98febf75663 | class-snap | active |
+--------------------------------------+------------+--------+

[root@controller ~(keystone_admin)]# openstack network list
+----------------------------------+-------------+--------------------------------------+
| ID                                   | Name        | Subnets                          |
+----------------------------------+-------------+--------------------------------------+
| 050585cf-24d9-42be-a1f0-c0f7b4aa80f8 |selfservice|                                    |
| 1e2a0885-4b0f-4b19-bda3-3a4d252776e9 |int1       |e853817f-751d-4482-84c8-7ada9d697dc8|
| 6f434769-769e-4a41-8915-ce3eefdfa46c | ext1      |e8b8ea01-2f7a-4c79-96fd-c1ce3ab90a4b|
+----------------------------------+-------------+--------------------------------------+

[root@controller ~(keystone_admin)]# openstack server create --flavor m1.nano --image class --nic net-id=6f434769-769e-4a41-8915-ce3eefdfa46c --security-group default --key-name mykey selfservice-instance
More than one SecurityGroup exists with the name 'default'.

[root@controller ~(keystone_admin)]# openstack security group list
	//이름 'default'가 중복되기 때문에 'default' 이름을 갖는 security group의 id로 명시
	//admin 계정이기 때문에 모든 project의 security group가 조회되어 발생하는 오류
+--------------------------------------+---------+----------------+----------------------------------+------+
| ID                                   | Name    | Description    | Project                          | Tags |
+--------------------------------------+---------+----------------+----------------------------------+------+
| 42e272a2-256c-4955-b208-47d2b63a4c1d | default | 기본 보안 그룹 |                                  | []   |
| 49c4dad1-f932-48b1-bed8-af28d890153a | default | 기본 보안 그룹 | aee429e75b0444068d11ea5b09a326c3 | []   |
| 614b4cd0-3e13-4826-8b01-ea477659d3d3 | default | 기본 보안 그룹 | 083c81e3bd2641fa9d886638c42d8c39 | []   |
| 686931f4-a051-445e-a660-f0f680ec2d39 | class1  | http and ssh   | 5dcd3d371f0c4865807ba52a2c463808 | []   |
| efa7e51e-5a71-4b00-9a19-70f7a103f7c6 | default | 기본 보안 그룹 | 5dcd3d371f0c4865807ba52a2c463808 | []   |
+--------------------------------------+---------+----------------+----------------------------------+------+

[root@controller ~(keystone_admin)]# openstack server create --flavor m1.nano --image class --nic net-id=6f434769-769e-4a41-8915-ce3eefdfa46c --security-group 42e272a2-256c-4955-b208-47d2b63a4c1d --key-name mykey selfservice-instance
+-------------------------------------+----------------------------------------------+
| Field                               | Value                                        |
+-------------------------------------+----------------------------------------------+
| OS-DCF:diskConfig                   | MANUAL                                       |
| OS-EXT-AZ:availability_zone         |                                              |
| OS-EXT-SRV-ATTR:host                | None                                         |
| OS-EXT-SRV-ATTR:hypervisor_hostname | None                                         |
| OS-EXT-SRV-ATTR:instance_name       |                                              |
| OS-EXT-STS:power_state              | NOSTATE                                      |
| OS-EXT-STS:task_state               | scheduling                                   |
| OS-EXT-STS:vm_state                 | building                                     |
| OS-SRV-USG:launched_at              | None                                         |
| OS-SRV-USG:terminated_at            | None                                         |
| accessIPv4                          |                                              |
| accessIPv6                          |                                              |
| addresses                           |                                              |
| adminPass                           | s9quoVSS3EkD                                 |
| config_drive                        |                                              |
| created                             | 2020-01-14T07:04:16Z                         |
| flavor                              | m1.nano (0)                                  |
| hostId                              |                                              |
| id                                  | fa8675bf-6089-4828-9c6d-ff8376f93198         |
| image                               | class (67adccb1-a464-468b-b1c9-51356cae3876) |
| key_name                            | mykey                                        |
| name                                | selfservice-instance                         |
| progress                            | 0                                            |
| project_id                          | 083c81e3bd2641fa9d886638c42d8c39             |
| properties                          |                                              |
| security_groups                     | name='42e272a2-256c-4955-b208-47d2b63a4c1d'  |
| status                              | BUILD                                        |
| updated                             | 2020-01-14T07:04:17Z                         |
| user_id                             | ff5b14b6caf7409ebe61458d41f5cf12             |
| volumes_attached                    |                                              |
+-------------------------------------+----------------------------------------------+

[root@controller ~(keystone_admin)]# openstack server list
+--------------------------------------+----------------------+--------+----------+-------+---------+
| ID                                   | Name                 | Status | Networks | Image | Flavor  |
+--------------------------------------+----------------------+--------+----------+-------+---------+
| fa8675bf-6089-4828-9c6d-ff8376f93198 | selfservice-instance | ERROR  |          | class | m1.nano |
+--------------------------------------+----------------------+--------+----------+-------+---------+
```





