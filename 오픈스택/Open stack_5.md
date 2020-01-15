# openstack

## Environment - installation

### 1. Security

### 2. Host networking 

* VMware Workstation

``` shell
# vi /etc/sysconfig/network-scripts/ifcfg-ens33
   // 파일 내용 수정 //uuid 앞에 '#' 붙여서 주석처리하기 //IPADDR을 100에서 11로 변경 
   // 아래 이미지 첨부
#systemctl restart network
#ip a
```

![image-20200110092631048](C:%5CUsers%5CHPE%5CAppData%5CRoaming%5CTypora%5Ctypora-user-images%5Cimage-20200110092631048.png)

* X-shell

10.0.0.11로 연결하는 새 세션 생성

![image-20200110093607121](C:%5CUsers%5CHPE%5CAppData%5CRoaming%5CTypora%5Ctypora-user-images%5Cimage-20200110093607121.png)

```shell
[root@controller ~]# cat /etc/hosts
   // 수정
127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4
::1         localhost localhost.localdomain localhost6 localhost6.localdomain6
10.0.0.11 controller
10.0.0.31 compute1
```



### 3. 시간 동기화하기

```shell
[root@controller ~]# yum install chrony -y
Loaded plugins: fastestmirror
Loading mirror speeds from cached hostfile
 * base: data.aonenetworks.kr
...

[root@controller ~]# vi /etc/chrony.conf
    //파일 내용 수정 //server2, 3을 주석처리한 후 10.0.0.100 주소로 추가 서버 생성 //총 3개 서버
    //아래 이미지 첨부 
```

![image-20200110093507632](C:%5CUsers%5CHPE%5CAppData%5CRoaming%5CTypora%5Ctypora-user-images%5Cimage-20200110093507632.png)

```shell
[root@controller ~]# date
2020. 01. 10. (금) 09:40:43 KST

[root@controller ~]# systemctl restart chronyd

[root@controller ~]# chronyc sources
210 Number of sources = 3
MS Name/IP address         Stratum Poll Reach LastRx Last sample               
===============================================================================
^? controller                    0   7     0     -     +0ns[   +0ns] +/-    0ns
^* 106.247.248.106               2   6    17     4   -157us[ -403us] +/-   20ms
^- 210.183.236.141               2   6    17     4   -667us[ -667us] +/-   55ms
```



### 4. package 설치 (rocky)

```shell
#yum install python-openstackclient -y
...

#yum install openstack-selinux -y
...

```



### 5. mariadb 설치

```shell
#yum install mariadb mariadb-server python2-PyMySQL -y
Loaded plugins: fastestmirror
Loading mirror speeds from cached hostfile
 * base: data.aonenetworks.kr
 * centos-qemu-ev: data.aonenetworks.kr
...

#vi /etc/my.cnf.d/openstack.cnf
   //빈 파일에 다음 내용 붙여넣기
[mysqld]
bind-address = 10.0.0.11

default-storage-engine = innodb
innodb_file_per_table = on
max_connections = 4096
collation-server = utf8_general_ci
character-set-server = utf8

[root@controller ~]# systemctl enable mariadb.service
Created symlink from /etc/systemd/system/multi-user.target.wants/mariadb.service to /usr/lib/systemd/system/mariadb.service.

[root@controller ~]# systemctl start mariadb.service

[root@controller ~]# systemctl status mariadb.service
● mariadb.service - MariaDB 10.1 database server
   Loaded: loaded (/usr/lib/systemd/system/mariadb.service; enabled; vendor preset: disabled)
   Active: active (running) since 금 2020-01-10 09:58:17 KST; 2min 41s ago
...

[root@controller ~]# mysql_secure_installation
   //all -> y로 진행
NOTE: RUNNING ALL PARTS OF THIS SCRIPT IS RECOMMENDED FOR ALL MariaDB
      SERVERS IN PRODUCTION USE!  PLEASE READ EACH STEP CAREFULLY!
...
```



### 5.Message queue

```bash
[root@controller ~]# yum install rabbitmq-server
Loaded plugins: fastestmirror
Loading mirror speeds from cached hostfile
 * base: data.aonenetworks.kr
 * centos-qemu-ev: data.aonenetworks.kr
...

[root@controller ~]# systemctl enable rabbitmq-server.service
Created symlink from /etc/systemd/system/multi-user.target.wants/rabbitmq-server.service to /usr/lib/systemd/system/rabbitmq-server.service.

[root@controller ~]# systemctl start rabbitmq-server.service

[root@controller ~]# rabbitmqctl add_user openstack RABBIT_PASS
Creating user "openstack"

[root@controller ~]# rabbitmqctl set_permissions openstack ".*" ".*" ".*"
Setting permissions for user "openstack" in vhost "/"

[root@controller ~]# rabbitmqctl status
   // 아래와 같이 controller가 잘 올라와있어야 한다
Status of node rabbit@controller
...
```



### 6. Memcached

```bash
[root@controller ~]# yum install memcached python-memcached -y

[root@controller ~]# vi /etc/sysconfig/memcached

[root@controller ~]# systemctl enable memcached.service
Created symlink from /etc/systemd/system/multi-user.target.wants/memcached.service to /usr/lib/systemd/system/memcached.service.

[root@controller ~]# systemctl start memcached.service

```



### 

