### LAB 2 -  Docker file과 Compose file 작성하기

* Docker file 작성

```dockerfile
# drupal 패키지 업데이트
FROM drupal:8.6
RUN  apt-get -y update

# git 설치
RUN apt-get install -y git

#소스복사

# drupal 패키지
WORKDIR /var/www/html/themes
RUN git clone --branch 8.x-3.x --single-branch --depth 1 https://git.drupal.org/project/bootstrap.git

# 권한 설정
# root가 아닌 user가 사용가능하도록
RUN chown -R www-data:www-data bootstrap
```

*  Compose file

*README.MD 파일 참고

> \- We're going to build a custom image in this compose file for drupal service. Use Compose file from previous assignment for Drupal to start with, and we'll add to it, as well as change image name.

> \- Rename image to `custom-drupal` as we want to make a new image based on the official `drupal:8.6`. 

=>  **LAB1**에서 사용했던 Compose File을 그대로 이용하되 이름을 `custom-drupal`로 만들어 준다. 

> \- We want to build the default Dockerfile in this directory by adding `build: .` to the `drupal` service. When we add a build + image value to a compose service, it knows to use the image name to write to in our image cache, rather then pull from Docker Hub.

=>  'build: .' 명령어를 이용해 디렉토리에 기본 Dockerfile을 구축한다. 합성 서비스에 빌드 + 이미지 값을 추가하면 이미지 이름을 사용하여 이미지 캐시에 쓰는 것이 아니라 Docker Hub에서 끌어오는 것을 알 수 있다.

> \- For the `postgres:9.6` service, you need the same password as in previous assignment, but also add a volume for `drupal-data:/var/lib/postgresql/data` so the database will persist across Compose restarts.

=> postgres:9.6 서비스를 추가한다. 동시에 'drupal-data:/var/lib/postgresql/data' 볼륨을 추가한다.

```yaml
version: '3'
services:
  drupal:
    image: drupal
    build: .
    ports:
      - "8080:80"
    volumes:
      - drupal-modules:/var/www/html/modules
      - drupal-profiles:/var/www/html/profiles
      - drupal-sites:/var/www/html/sites
      - drupal-themes:/var/www/html/themes

  postgres:
      image: postgres:9.6

  mysql:
    image: mysql:5.7
    environment:
      - MYSQL_ROOT_PASSWORD=mypasswd 
volumes:
  drupal-modules:
  drupal-profiles:
  drupal-sites:
  drupal-themes:
  drupal-data: /var/lib/postgresql/data
```

* *  Compose file을 이용해서 image 생성 및 확인

```shell
> docker build -t drupal-image .
  -- 'drupal-image'이라는 이름으로 이미지 build
Sending build context to Docker daemon  8.192kB
Step 1/6 : FROM drupal:8.6
 ---> de1b43d9d6a8
Step 2/6 : RUN  apt-get -y update
 ---> Running in 0262f0640159  
...
Successfully built b505b84dd216
Successfully tagged custom-drupal:latest
SECURITY WARNING: You are building a Docker image from Windows against a non-Windows Docker host. All files and directories added to build context will have '-rwxr-xr-x' permissions. It is recommended to double check and reset permissions for sensitive files and directories.

> docker images
  -- 'drupal-image' 이름의 image 생성된 모습 확인
REPOSITORY                           TAG                 IMAGE ID            CREATED             SIZE
custom-drupal                        latest              b505b84dd216        About a minute ago   515MB
```

![image-20200116152843321](C:/Users/HPE/Desktop/study 2/images/image-20200116152843321.png)

<img src="C:/Users/HPE/Desktop/study 2/images/image-20200116161517483.png" alt="image-20200116161517483"  />

![image-20200116161549092](C:/Users/HPE/Desktop/study 2/images/image-20200116161549092.png)

=> http://localhost:8080/ 로 접속하면 **Drupal** 로 연결된 것을 확인할 수 있다.

