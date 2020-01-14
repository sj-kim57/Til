# 도커

## MongoDB 설치

![image-20200102093917456](images/image-20200102093917456.png)

1. 이미지 받아오기

   ```powershell
   > docker pull mongo
      # mongo:4.1과 같이 버전명을 쓰지 않는 경우 최신 버전으로 받아오게 된다
   Using default tag: latest
   ...
   ```

2. 기동

   ```powershell
   > docker run --name mongodb_server -d -p 16010:27017 mongo
   feb0881969069de16d13e256373b2e474cc30d3eaf8713e4fa1427f27b4c3d29
   ```

3. Bash 접근, Mongo 접속

   ```powershell
   > docker exec -it mongodb_server bash
   ```

4. 관리자 계정 생성

5. 관리자 로그인, 일반계정 생성

6. ReplicaSet 설정

   ```powershell
      # MongoDB추가 X 2개(mongodb_server_2, mongodb_server_3... )
   > rs.initiate()
   > rs.add("mongodb_server_2:27017"), rs.add("mongodb_server_3:27017")... (with IP address)
   > db.ismaster
      # mongodb_server_1 에서 실행
     
      
      docker exec -it mongodb_server_1 bash ( # 각각 다른 powershell 접속)
      docker exec -it mongodb_server_2 bash
      docker exec -it mongodb_server_3 bash
      
      rs.initiate()
   
    ## 컨테이너 생성 및 실행
   > docker run --name mongodb_server_1 -d -p 16010:27017  mongo --replSet myapp
   bb23ff9b13d5840688584f5b1eb6283e2f04ec9ffa60c547de042f50ef56249b
   
   > docker run --name mongodb_server_2 -d -p 26020:27017 mongo --replSet myapp
   57c29e385356fa3a0affca43ca7cbce575ecca63248c33a933337ef2fad72e22
   
   3> docker run --name mongodb_server_3 -d -p 36030:27017 mongo --replSet myapp
   cf739008eb505f20c3a24a3f8ffd42ed435fb91d9685e45177fa14d6f013c79d
   
   > docker ps -a
   CONTAINER ID        IMAGE                           COMMAND                  CREATED             STATUS                      PORTS                                      NAMES
   cf739008eb50        mongo                           "docker-entrypoint.s…"   8 seconds ago       Exited (2) 6 seconds ago                                               mongodb_server_3
   57c29e385356        mongo                           "docker-entrypoint.s…"   15 seconds ago      Exited (2) 13 seconds ago                                              mongodb_server_2
   bb23ff9b13d5        mongo                           "docker-entrypoint.s…"   23 seconds ago      Exited (2) 21 seconds ago                                              mongodb_server_1
   ...
   
   > docker inspect "id"
      #ip 조사
      
   > db.ismaster
      #db host에 서버가 등록되어있지 않음
   > rs.initiate 
   > rs.add("ip주소:27017") # 3가지 모두
   > db.ismaster
      #db host에 서버가 등록됨
      
   > show dbs;   
   > use bookstore;
   > db.books.save({title:"Docker1"});
   > db.books.save({title:"java2"});
   
    ##secondary 접속
   > db.ismaster()
   > docker exec -it mongodb_server_2 bash (?)
   
   >cfg.membrers[0].host = "ip주소:27017"
   >rs.reconfig(cfg)
   > db.ismaster() #하면 내 ip주소랑 일차하는 host 확인 가능
   ```

   

