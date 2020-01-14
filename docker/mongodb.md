

# mongodb

## RePlica: 하나의 DB 정보를 다른 서버와 공유 및 복제 기능

* #### replica set 설정할 DB 준비

  ```bash
  C:\Users\HPE\Work\mongodb-4.2.2\data> $mongod --dbpath .\data\node1 --port 27018 --replSet myapp --(mongod: server버전)
  ```

* ####  replica set 설정(client)  

  ```bash
  C:\Users\HPE> $mongo --host 127.0.0.1 --port 27018
  ```

  

1. Primary, Secondary 동작 확인

   ```bash
   $ show dbs; -db목록 출력
   $ use bookstore; --db 생성
   $ show collections; --db출력
   $ db.books.save({title: "MongoDb basic"});  --데이터 입력
   ```

2.  Primary 작동 중지

   -`Ctrl + C`또는 `db.shutdownServer();`

3. Secondary 중에서 Primary로 승격 된 것 확인

   * Secondary 중 하나의 노드에 접속
   * db.isMasyer()로 Master가 누구인지 확인

4.  이전 Primary였던 Node 다시 실행 

   * db.isMasyer()에서 현재 상태 확인
   * 기존에 primary였어도 현재는 Secondary





## mongdb 설치

```bash
$ cd C:\Users\HPE\Work\git\vagrant

# 파일의 주석을 풀어 node1,2,3 실행상태로 전환
$ code vagrantfile

$ vagrant up
Bringing machine 'node01' up with 'virtualbox' provider...
Bringing machine 'node02' up with 'virtualbox' provider...
Bringing machine 'node03' up with 'virtualbox' provider...
==> node01: Checking if box 'centos/7' version '1905.1' is up to date...
==> node01: Machine already provisioned. Run `vagrant provision` or use the `--provision`
==> node01: flag to force provisioning. Provisioners marked to run always will still run.
==> node02: Checking if box 'centos/7' version '1905.1' is up to date...
==> node02: Machine already provisioned. Run `vagrant provision` or use the `--provision`
==> node02: flag to force provisioning. Provisioners marked to run always will still run.
==> node03: Checking if box 'centos/7' version '1905.1' is up to date...
==> node03: Machine already provisioned. Run `vagrant provision` or use the `--provision`
==> node03: flag to force provisioning. Provisioners marked to run always will still run.

# running 상태 확인
$ vagrant status
Current machine states:

node01                    running (virtualbox)
node02                    running (virtualbox)
node03                    running (virtualbox)

This environment represents multiple VMs. The VMs are all listed
above with their current state. For more information about a specific
VM, run `vagrant status NAME`.
```

```bash
$ sudo yum -y update

# MongoDB 4.0 저장소 메타 정보 생성
$ sudo touch /etc/yum.repos.d/mongodb-org.repo
$ sudo bash -c 'echo "[mongodb-org-4.0]" >> /etc/yum.repos.d/mongodb-org.repo'
$ sudo bash -c 'echo "name=MongoDB Repository" >> /etc/yum.repos.d/mongodb-org.repo'
$ sudo bash -c 'echo "baseurl=https://repo.mongodb.org/yum/redhat/7/mongodb-org/4.0/x86_64/" >> /etc/yum.repos.d/mongodb-org.repo'
$ sudo bash -c 'echo "gpgcheck=1" >> /etc/yum.repos.d/mongodb-org.repo'
$ sudo bash -c 'echo "enabled=1" >> /etc/yum.repos.d/mongodb-org.repo'
$ sudo bash -c 'echo "gpgkey=https://www.mongodb.org/static/pgp/server-4.0.asc" >> /etc/yum.repos.d/mongodb-org.repo'

# MongoDB 4.0 설치
$ sudo yum install -y mongodb-org

# MongoDB 버전 확인
$ mongo 
   #MongoDB shell version v4.0.14 확인 


# 환경 설정
$ sudo vi /etc/mongod.conf
net:
  port: 27017
  #내부망에 연결된 모든 노드로부터의 원격 접속을 허용
  #개발 및 테스트 환경에서만 사용, 운영 환경에서는 보안 문제로 비추천
  bindIp: 0.0.0.0
```

```bash

```



cfg.vm.network "private_network", ip: "10.0.0.11"

cfg.vm.network "forwarded_port", guest: 27017, host: 27017



node1 => 40001 (최종 Master)

node2 => 40002 (최종)

node3 => 40003 (최종 )

```bash
## node01, 02, 03 server 에서 아래 과정을 똑같이 반복

[vagrant@node01 ~]$ mongod --repleSet myapp --dbpath ./mongo/data --port 40001 --bind_ip_all
   #--bind_ip_all옵션은 외부의 ip접속 허용
   
[vagrant@node01 ~]$ sudo vi /etc/hosts
   #/etc/hosts 파일에 node01, node02, node03의 IP address 등록
10.0.0.11       node01
10.0.0.12       node02
10.0.0.13       node03
10.0.0.14       node04      
```

```bash
#node01, 02, 03에 대해 새로운 콘솔 창(client)을 열어서 아래 과정을 똑같이 반복(11,40001/12,40002/13,40003)
[vagrant@node02 ~]$ mongo --host 10.0.0.11 --port 40001
$ rs.initiate()
   #입력 후 enter키를 치면 primary로 넘어감
```

```bash
## node01 server창

[vagrant@node01 ~]$ rs.initiate()
[vagrant@node01 ~]$ rs.add("10.0.0.12:40002")
[vagrant@node01 ~]$ rs.add("10.0.0.13:40003",{arbiterOnly: true})
   #arbiterOnly로 설정시 Primary 선정에만 관여, 복제는 하지 않음

[vagrant@node01 ~]$ show dbs;
   #현재 db목록 출력 
[vagrant@node01 ~]$ use bookstore;
   #bookstore라는 db 추가
[vagrant@node01 ~]$ db.books.insert({title: "Oliver Twist"})
   #books라는 table에 행 추가
[vagrant@node01 ~]$ db.books.find();
   #'books' table의 모든 column 확인 가능
```

```bash
## node01창



```

![image-20191230140107487](images/image-20191230140107487.png)

```bash

```



db.

use bookstore

db.books.sav

