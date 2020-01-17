# docker exercise

## docker compose

### LAB 1 -  compose file 작성하기

- DURAPLE 서비스 Compose file

  [github](https://github.com/joneconsulting/cloud-computing/tree/master/04.Docker/lab) 에 있는 compose file을 download

![image-20200116115437831](C:\Users\HPE\AppData\Roaming\Typora\typora-user-images\image-20200116115437831.png)

=> study2 폴더를 만들어 파일을 다운로드했다.

```bash
> cd C:\Users\HPE\Desktop\study 2\04.Docker\lab\1.compose\compose-assignment-1
  -- Docker compose 파일이 있는 폴더로 이동 
> docker-compose up -d
  -- compose file 실행
Creating network "compose-assignment-1_default" with the default driver
Creating volume "compose-assignment-1_drupal-modules" with default driver
...
Status: Downloaded newer image for drupal:latest
Creating compose-assignment-1_drupal_1 ... done
Creating compose-assignment-1_mysql_1  ... done

C:\Users\HPE\Desktop\study 2\04.Docker\lab\1.compose\compose-assignment-1> docker container ls -a
  -- container에 drupal, mysql 가 추가된 모습 확인 
CONTAINER ID        IMAGE                  COMMAND                  CREATED             STATUS                      PORTS                                                                   NAMES
ff88253f4e36        drupal                 "docker-php-entrypoi…"   49 seconds ago      Up 43 seconds               0.0.0.0:8080->80/tcp                                                    compose-assignment-1_drupal_1
331d235b5d89        mysql:5.7              "bin/true"               49 seconds ago      Exited (0) 44 seconds ago                                                                           compose-assignment-1_mysql_1
```

