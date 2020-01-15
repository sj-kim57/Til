# openstack

## keystone installation

### 1. install and configure

#### prerequisites

``` shell
[root@controller ~]# mysql -u root -p
Enter password: 
Welcome to the MariaDB monitor.  Commands end with ; or \g.
Your MariaDB connection id is 11
Server version: 10.1.20-MariaDB MariaDB Server

Copyright (c) 2000, 2016, Oracle, MariaDB Corporation Ab and others.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.
```

```mysql
MariaDB [(none)]> CREATE DATABASE keystone;
Query OK, 1 row affected (0.00 sec)

MariaDB [(none)]> GRANT ALL PRIVILEGES ON keystone.* TO 'keystone'@'localhost' \
    -> IDENTIFIED BY 'KEYSTONE_DBPASS';
Query OK, 0 rows affected (0.00 sec)

MariaDB [(none)]> GRANT ALL PRIVILEGES ON keystone.* TO 'keystone'@'%' \
    -> IDENTIFIED BY 'KEYSTONE_DBPASS';
Query OK, 0 rows affected (0.00 sec)
```



#### install and configure components

```shell
[root@controller ~]#  yum install openstack-keystone httpd mod_wsgi
Loaded plugins: fastestmirror
Loading mirror speeds from cached hostfile

 * base: data.aonenetworks.kr
 * centos-qemu-ev: data.aonenetworks.kr
   ...
   
[root@controller ~]# vi /etc/keystone/keystone.conf
   //742 라인에  connection = mysql+pymysql://keystone:KEYSTONE_DBPASS@controller/keystone 	추가 
   //2829 라인에 provider = fernet 추가
   //아래에 이미지 첨부
```

![image-20200110110855108](images/image-20200110110855108.png)

![image-20200110110931437](images/image-20200110110931437.png)

```shell
[root@controller ~]# su -s /bin/sh -c "keystone-manage db_sync" keystone

//keystone과 관련된 table을 만들어주는 명령어
```

```shell
[root@controller ~]# su -s /bin/sh -c "keystone-manage db_sync" keystone
	//keystone과 관련된 table을 만들어주는 명령어

[root@controller ~]# ls /var/lib/mysql/keystone/
	//table 생성 결과 확인
access_token.frm                 group.frm              policy.ibd                  service.frm
access_token.ibd                 group.ibd              policy_association.frm      service.ibd
application_credential.frm       id_mapping.frm         policy_association.ibd      service_provider.frm
...
```

```shell
	//initialize 명령어
[root@controller ~]# keystone-manage fernet_setup --keystone-user keystone --keystone-group keystone

[root@controller ~]# keystone-manage credential_setup --keystone-user keystone --keystone-group keystone

[root@controller ~]# keystone-manage bootstrap --bootstrap-password ADMIN_PASS \
   //url을 3개 포함하고 있는 명령어 //
>   --bootstrap-admin-url http://controller:5000/v3/ \
>   --bootstrap-internal-url http://controller:5000/v3/ \
>   --bootstrap-public-url http://controller:5000/v3/ \
>   --bootstrap-region-id RegionOne
```



#### configure the Apache HTTP server

```shell
[root@controller ~]# vi  /etc/httpd/conf/httpd.conf
   //96 라인에 ServerName controller 추가
```

![image-20200110112400386](images/image-20200110112400386.png)

```shell
[root@controller ~]#  ln -s /usr/share/keystone/wsgi-keystone.conf /etc/httpd/conf.d/
```



#### Finalize the installation

```shell
[root@controller ~]# systemctl enable httpd.service
Created symlink from /etc/systemd/system/multi-user.target.wants/httpd.service to /usr/lib/systemd/system/httpd.service.

[root@controller ~]# systemctl start httpd.service
```

```shell
[root@controller ~]# export OS_USERNAME=admin

[root@controller ~]# export OS_PASSWORD=ADMIN_PASS

[root@controller ~]# export OS_PROJECT_NAME=admin

[root@controller ~]# export OS_USER_DOMAIN_NAME=Default

[root@controller ~]# export OS_PROJECT_DOMAIN_NAME=Default

[root@controller ~]# export OS_AUTH_URL=http://controller:5000/v3

[root@controller ~]# export OS_IDENTITY_API_VERSION=3

[root@controller ~]# openstack user list
+----------------------------------+-------+
| ID                               | Name  |
+----------------------------------+-------+
| dcb491be635b4781a30c25d3bc608817 | admin |
+----------------------------------+-------+
```



### 2. Create a domain, projects, users, and roles

```shell
[root@controller ~]# openstack project create --domain default description "Service Project" service
+-------------+----------------------------------+
| Field       | Value                            |
+-------------+----------------------------------+
| description | Service Project                  |
| domain_id   | default                          |
| enabled     | True                             |
| id          | 7002ea94c2cf4daf85966e1c90d93410 |
| is_domain   | False                            |
| name        | service                          |
| parent_id   | default                          |
| tags        | []                               |
+-------------+----------------------------------+

[root@controller ~]# openstack user create --domain default password abc123 myuser
usage: openstack user create [-h] [-f {json,shell,table,value,yaml}]
                             [-c COLUMN] [--max-width <integer>] [--fit-width]
                             [--print-empty] [--noindent] [--prefix PREFIX]
                             [--domain <domain>] [--project <project>]
                             [--project-domain <project-domain>]
                             [--password <password>] [--password-prompt]
                             [--email <email-address>]
                             [--description <description>]
                             [--enable | --disable] [--or-show]
                             <name>
openstack user create: error: unrecognized arguments: abc123 myuser

[root@controller ~]# openstack role create myrole openstack role add --project myproject --user myuser myrole
usage: openstack role create [-h] [-f {json,shell,table,value,yaml}]
                             [-c COLUMN] [--max-width <integer>] [--fit-width]
                             [--print-empty] [--noindent] [--prefix PREFIX]
                             [--domain <domain>] [--or-show]
                             <role-name>
openstack role create: error: unrecognized arguments: openstack role add --project myproject --user myuser myrole
```

