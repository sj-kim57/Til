# 도커(Docker)

* 도커를 사용하기 위해서는 작업관리자-성능의 가상화 기능이 활성화되어 있어야 한다.

<img src="C:\Users\HPE\AppData\Roaming\Typora\typora-user-images\image-20191230144750077.png" alt="image-20191230144750077" style="zoom:50%;" />

* 도커는 컨테이너형 가상화 기술을 구현하기 위한 상주 애플리케이션과 이 애플리케이션을 조작하기 위한 명령행 도구로 구성되는 프로덕트다. 애플리케이션 배포에 특화돼 있기 떄문에 애플리케이션 개발 및 운영을 컨테이너 중심으로 할 수 있다. 

*컨테이너: 도커가 만들어내는 게스트 운영 체제

* 과거에는 Virtual Machine을 많이 사용했으나 Docker의 등장으로 Container의 관심이 증가했다.

  * Virtual Machine: 하이퍼 바이저를 통한 컴퓨팅 가상화로 여러 Guset OS를 동작시킴

    *하이퍼바이저(Hypervisor): Virtual Machine을 생성, 실행, 관리하기 위한 소프트웨어 플램폼

  ![image-20191230150706802](C:\Users\HPE\AppData\Roaming\Typora\typora-user-images\image-20191230150706802.png)

* 도커 컨테이너는 운영 체제의 동작을 완전히 재현하지는 못하기 때문에 좀 더 엄밀한 linux 계열 운영 체제의 동작이 요구되는 가상 환경을 구축해야 한다면 기존 방법대로 VM Ware나 Virtual BOX를 사용하는 것이 낫다.  



## 도커 스타일 체험하기

* Visual Studio Code에서 helloworld라는 셸 스크랍트 파일을 만든다. 단순 스크립트이지만 여기서는 이것이 애플리케이션 역할을 한다. 

   <img src="images/image-20191230164030774.png" alt="image-20191230164030774" style="zoom:67%;" />

  ```bash
  #! /bin.sh
  echo "hello, world!"
  ```

* 



<img src="images/image-20191230174802869.png" alt="image-20191230174802869" style="zoom:50%;" />

