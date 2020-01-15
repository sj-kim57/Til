# openstack

## openstack CLI로 관리하기

### Identity 서비스 (keystone)

 #### 개념

* 사용자: 오픈스택 클라우드 서비스를 사용하는 대상
* 인증: 사용자의 신원 확인
* 토큰: 자원에 접근하는데 사용되는 임의의 텍스트 bit
* Tenant: 격리된 자원, identity 개체에 사용되는 컨테이너
* 엔드 포인트: 서비스로 접근하려고 하는, 즉 네트워크로 접근가능한 주소
* role: 특정 작업을 수행하도록 하는 고유 성격

```shell
[root@controller ~]# ps -ef|grep mysql
mysql      1856      1  0 10:17 ?        00:03:04 /usr/libexec/mysqld --basedir=/usr
root      46624  46575  0 15:55 pts/0    00:00:00 grep --color=auto mysql
[root@controller ~]# rpm -qa|grep -i mysql
puppet-mysql-6.0.0-1.204cfd4git.el7.noarch
perl-DBD-MySQL-4.023-6.el7.x86_64
python2-PyMySQL-0.9.2-2.el7.noarch
MySQL-python-1.2.5-1.el7.x86_64
[root@controller ~]# rpm -qa|grep -i mariadb
mariadb-server-galera-10.1.20-2.el7.x86_64
mariadb-config-10.1.20-2.el7.x86_64
mariadb-server-10.1.20-2.el7.x86_64
mariadb-10.1.20-2.el7.x86_64
mariadb-errmsg-10.1.20-2.el7.x86_64
mariadb-libs-10.1.20-2.el7.x86_64
mariadb-common-10.1.20-2.el7.x86_64
[root@controller ~]# mysql uroot
ERROR 1049 (42000): Unknown database 'uroot'
[root@controller ~]# mysql -uroot
Welcome to the MariaDB monitor.  Commands end with ; or \g.
Your MariaDB connection id is 317
Server version: 10.1.20-MariaDB MariaDB Server

Copyright (c) 2000, 2016, Oracle, MariaDB Corporation Ab and others.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

MariaDB [(none)]> show databases;
+--------------------+
| Database           |
+--------------------+
| cinder             |
| glance             |
| information_schema |
| keystone           |
| mysql              |
| neutron            |
| nova               |
| nova_api           |
| nova_cell0         |
| nova_placement     |
| performance_schema |
| test               |
+--------------------+
12 rows in set (0.08 sec)
```

#### CLI로 관리하기 - 조회

```shell
[root@controller ~(keystone_admin)]# openstack user list
+----------------------------------+-----------+
| ID                               | Name      |
+----------------------------------+-----------+
| 6ad15504bde3460581540905e151946a | nova      |
| 71b9139e1cfc4f96b62396cb1631ba59 | swift     |
| 76f1a42c58ae42e08bea1dac04a0854c | stack1    |
| 7f75ac977a754a4d97da980f82895e26 | neutron   |
| 8e258e1e5b8147128a78317ba556c378 | placement |
| b215ca95b35444b9b43bc0f47cf5f50e | cinder    |
| e4c814c0f06f4d5a9500231f045cfd8a | mgr1      |
| f08aba81aced4f6697124effb2d38468 | glance    |
| ff5b14b6caf7409ebe61458d41f5cf12 | admin     |
+----------------------------------+-----------+
[root@controller ~(keystone_admin)]# openstack project list
+----------------------------------+----------+
| ID                               | Name     |
+----------------------------------+----------+
| 083c81e3bd2641fa9d886638c42d8c39 | admin    |
| 5dcd3d371f0c4865807ba52a2c463808 | pro1     |
| aee429e75b0444068d11ea5b09a326c3 | services |
+----------------------------------+----------+
[root@controller ~(keystone_admin)]# openstack service list
+----------------------------------+-----------+--------------+
| ID                               | Name      | Type         |
+----------------------------------+-----------+--------------+
| 17dd013e444e4843afd10c147b32b5d3 | keystone  | identity     |
| 4ab0c77ecdb04f99a40520d426413d7c | cinderv3  | volumev3     |
| 4d6325567277425ca517f266771a5da3 | neutron   | network      |
| 65b4a2a86baf477f91dadceb23ccc444 | cinderv2  | volumev2     |
| 7d694680ccaf4e9cbe56cbb83900b060 | placement | placement    |
| 8d8dde903bb44b9382cbe0844cf3f5ff | nova      | compute      |
| 90e85247fcfe4f988d6c28dac3099161 | swift     | object-store |
| babae3b764104833b134e1a4f404e46d | glance    | image        |
| cb90ccbfaf184060a0e7e5d2f84ef270 | cinder    | volume       |
+----------------------------------+-----------+--------------+
[root@controller ~(keystone_admin)]# openstack role list --user admin --project admin
Listing assignments using role list is deprecated. Use role assignment list --user <user-name> --project <project-name> --names instead.
+----------------------------------+-------+---------+-------+
| ID                               | Name  | Project | User  |
+----------------------------------+-------+---------+-------+
| 9e04442d25924a5e8e0b89e71822b16b | admin | admin   | admin |
+----------------------------------+-------+---------+-------+
[root@controller ~(keystone_admin)]# openstack role list --user stack1 --project pro1
Listing assignments using role list is deprecated. Use role assignment list --user <user-name> --project <project-name> --names instead.
+----------------------------------+----------+---------+--------+
| ID                               | Name     | Project | User   |
+----------------------------------+----------+---------+--------+
| 9fe2ff9ee4384b1894a90878d3e92bab | _member_ | pro1    | stack1 |
+----------------------------------+----------+---------+--------+
```

```shell
[root@controller ~(keystone_admin)]# ls
anaconda-ks.cfg  keystonerc_admin  openstack.old  openstack.txt  stack1-key1.pem
[root@controller ~(keystone_admin)]# cp keystonerc_admin keystonerc_stack1
[root@controller ~(keystone_admin)]# vi keystonerc_stack1
unset OS_SERVICE_TOKEN
    export OS_USERNAME=stack1      //수정
    export OS_PASSWORD='abc123'
    export OS_REGION_NAME=RegionOne
    export OS_AUTH_URL=http://10.0.0.100:5000/v3
    export PS1='[\u@\h \W(keystone_stack1)]\$ '  //수정

export OS_PROJECT_NAME=pro1   //수정
export OS_USER_DOMAIN_NAME=Default
export OS_PROJECT_DOMAIN_NAME=Default
export OS_IDENTITY_API_VERSION=3
```

#### CLI로 관리하기 - demo 사용자 생성



