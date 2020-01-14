# 도커

## 네트워크

### 도커 네트워크 기능

:도커에서 제공하는 대표적인 네트워크 드라이버로는 `브릿지(bridge)`, `호스트(host)`, `논(none)`, `컨테이너(container)` `오버레이(overlay)`가 있다.

* 브릿지 네트워크:





## 컴포즈(Compose)

docker는 여러가지 인프라에 대한 개발환경을 구축시 각각의 포트와 이미지와의 연결이 필요하기 때문에 많은 시간이 소요될 수 있다. 

이러한 문제를 해결하기 위한 도구가 compose이다. compose는 multi container docker 어플리케이션을 정의하고 실행하는 톨로서 어플리케이션의 서비스를 yaml파일에 설정하여 사용할 수 있다. 설정된 yaml 파일을 이용하면 단지 하나의 명령어로서 어플리케이션 실행에 필요한 모든 환경 구성을 완료할 수 있다.

* Compose 제공 기능
  * 하나의 시스템에서 여러개의 고립된 환경을 제공
  * Container 생성 시 데이터 보존
  * 변경된 Container들만 재생성
  * 변수와 각 환경간의 요소 이동



###  compose 명령으로 컨테이너 실행하기



> Visual code
>
> ![image-20200102140651079](C:%5CUsers%5CHPE%5CAppData%5CRoaming%5CTypora%5Ctypora-user-images%5Cimage-20200102140651079.png)
>
> =>  임의의 디렉터리 "mongo"를 생성하고 docker-compose.yml 파일을 작성한다.

```yaml
version: "3"
services: 
    mongo1:
        image: "mongo"
        ports: 
            - "16031:27017"
        volumes: 
            - $HOME/mongoRep1/mongo1:/data/db
        networks: 
            - mongo-networks
        command: mongod --replSet myapp
    
    mongo2:
        image: "mongo"
        ports: 
            - "16032:27017"
        volumes: 
            - $HOME/mongoRep1/mongo2:/data/db
        networks: 
            - mongo-networks
        command: mongod --replSet myapp
        depends_on: 
            - mongo1

    mongo3:
        image: "mongo"
        ports: 
            - "16030:27017"
        volumes: 
            - $HOME/mongoRep1/mongo3:/data/db
        networks: 
            - mongo-networks
        command: mongod --replSet myapp
        depends_on: 
            - mongo2


networks: 
    mongo-networks:
        driver: bridge
```



docker-compose.yml 파일이 저장된 경로에서 docker-compose up 명령어 실행

```powershell
> docker-compose up
   # 여러 컨테이너를 실행하는 명령어
WARNING: The HOME variable is not set. Defaulting to a blank string.
Creating network "mongo_mongo-networks" with driver "bridge"
Creating mongo_mongo1_1 ... done
Creating mongo_mongo2_1 ... done
Creating mongo_mongo3_1 ... done
```



새로운  powershell  창을 열어 파일 저장 경로로 들어가 컨테이너가 잘 실행되었는지 확인

```powershell
> docker ps
CONTAINER ID        IMAGE                           COMMAND                  CREATED             STATUS              PORTS                                      NAMES
aafb119d0799        mongo                           "docker-entrypoint.s…"   13 seconds ago      Up 10 seconds       0.0.0.0:16030->27017/tcp                   mongo_mongo3_1
dc5a8c1cab06        mongo                           "docker-entrypoint.s…"   16 seconds ago      Up 12 seconds       0.0.0.0:16032->27017/tcp                   mongo_mongo2_1
c198a2e6fb33        mongo                           "docker-entrypoint.s…"   18 seconds ago      Up 15 seconds       0.0.0.0:16031->27017/tcp                   mongo_mongo1_1
...
```



> Visual code
>
> ![image-20200102142622527](C:%5CUsers%5CHPE%5CAppData%5CRoaming%5CTypora%5Ctypora-user-images%5Cimage-20200102142622527.png)
>
> => replication setting.js 파일을 작성해준다.

```javascript
rs.initiate()
rs.add("172.17.0.7:27017")
rs.add("172.17.0.6:27017")
rs.add("172.17.0.4:27017")
```



replication setting.js  파일이 저장된 경로에서 docker-compose up 명령어 실행

```powershell
docker natwork ls #network 목록 출력
docker netwirk inspect "id" # 참여 컨테이너 확인
# 참여 컨테이너 명과 같이 js파일 수정(정적 ip주소가 아닐 경우 계속 바뀔 수 있음)
```

```sql
> show dbs;
> use bookstore;
> show collections;
> rs.
```

docker build -t test_mysql:latest .