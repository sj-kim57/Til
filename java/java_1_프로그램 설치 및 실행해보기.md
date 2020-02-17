# java

## 프로그램 설치

**JDK 설치**https://myanjini.tistory.com/122

**Eclipse 설치**https://myanjini.tistory.com/123



## 실행해보기

### 메모장과 터미널 이용해서 실행해보기

![image-20200217102849208](C:%5CUsers%5CHPE%5CAppData%5CRoaming%5CTypora%5Ctypora-user-images%5Cimage-20200217102849208.png)

![img](https://lh5.googleusercontent.com/en_Oqk2pzsjIovYal6XyoNMAsBZJFnlXHK2oGpVacDxACApbH0ITOVGcuAttJkgyfRz5MNJdPqnytS7Qz2bBi-PnlgKpADtH9XhDz7GlwZqeBHfXNkuJ9liTjna_94f40H3P6Vvs)

*utf-8 아닌 ANSI로 할 것!!!



[시작]> [시스템 환경 변수 편집] > [환경변수]

1. JAVA_HOME 변수 추가

![img](https://lh5.googleusercontent.com/BDbSGGXrojjDudZG2OEGEXKXje94Dv7kvBPGoNOXcEMIFKGCv2e3DfPiL3Z09xYdWSlsARSEXn7udvnKUM08XMPP3Nk_1ky-ng7cib0b5pkdjT0iZF8ISFfgNTpnQ_CezZxR8tli)

2. 

![image-20200217103417379](C:%5CUsers%5CHPE%5CAppData%5CRoaming%5CTypora%5Ctypora-user-images%5Cimage-20200217103417379.png)

3. CMD 창을 새로 실행

```powershell
C:\Users\HPE>set JAVA_HOME
JAVA_HOME=C:\Program Files\Java\jdk1.8.0_241

C:\Users\HPE>set PATH
Path=C:\Program Files (x86)\Common Files\Oracle\Java\javapath;C:\ProgramData\DockerDesktop\version-bin;C:\Program Files\Docker\Docker\Resources\bin;C:\Windows\system32;C:\Windows;C:\Windows\System32\Wbem;C:\Windows\System32\WindowsPowerShell\v1.0\;C:\Windows\System32\OpenSSH\;C:\Program Files\Git\cmd;C:\Program Files\nodejs\;C:\HashiCorp\Vagrant\bin;C:\Program Files\Java\jdk1.8.0_241\bin;C:\Program Files\MySQL\MySQL Shell 8.0\bin\;C:\Users\HPE\AppData\Local\Microsoft\WindowsApps;C:\Users\HPE\AppData\Local\Programs\Microsoft VS Code\bin;C:\Users\HPE\AppData\Roaming\npm;C:\Program Files\MySQL\MySQL Server 8.0\bin;C:\Users\HPE\Work\mongodb-4.2.2\bin;C:\Program Files\Java\jdk1.8.0_241\bin;
PATHEXT=.COM;.EXE;.BAT;.CMD;.VBS;.VBE;.JS;.JSE;.WSF;.WSH;.MSC

C:\Users\HPE>javac
Usage: javac <options> <source files>
where possible options include:
  -g                         Generate all debugging info
  -g:none                    Generate no debugging info
  -g:{lines,vars,source}     Generate only some debugging info
  -nowarn                    Generate no warnings
  ...
```

```powershell
C:\workspaces>javac HelloJava.java

C:\workspaces>dir
 C 드라이브의 볼륨에는 이름이 없습니다.
 볼륨 일련 번호: A0B4-FAD1

 C:\workspaces 디렉터리

2020-02-17  오전 10:39    <DIR>          .
2020-02-17  오전 10:39    <DIR>          ..
2020-02-17  오전 08:41    <DIR>          .metadata
2020-02-17  오전 10:39               425 HelloJava.class ⇐ Java ByteCode File
2020-02-17  오전 10:39               120 HelloJava.java  ⇐ Java Source File
               2개 파일                 545 바이트
               3개 디렉터리  856,509,513,728 바이트 남음

C:\workspaces>java HelloJava
Hello Java!!!    ⇐ HelloJava 실행 결과
```



### Eclipse 이용해서 실행해보기

![img](https://lh6.googleusercontent.com/PBjGOOT-039l7u3veZBfc-aQv6KHBfxyfV4gQYHw8-Imz-p6-6ltYQ5_FcBhA3GGwvybN2z6ZxPqdHMM-xzQJn6Hha46mo0gcot2yf2y-0NCQXkAqovCVXcLyOS8tbPE7bfN7kDo)

![img](https://lh3.googleusercontent.com/oL6tQKemE096eHkpwx_zHp_JVp4bh4iCpEZ4yUIAymQfj1-BFQprMkcvKFPiVB3TtQwXmO2m1Gns4dc3glk1lkSc5GBNhLMi9kZLmbL89y9R66cuVWK7jjvE45tYSVamZAcwAVHy)

![img](https://lh5.googleusercontent.com/f1rZaB3XYT9bYaS8yT7yuW2ksLqCCJYEghqT1rDMa62LuBCD8SaX4rduV4LOZQ5V4nZQ4khi8-BymnPuKssrYoWwUrL8kp_fgtWszGk9pnbzjHn0k-4eUtXh49uQ4Gs7XjdkdGxb)

![img](https://lh6.googleusercontent.com/SSsIWCweZqdeaJtWT-IWDAC9SrAmOd51Y6hIZ2rKCjAyPAoRKmXIRUCfwXnCBWIjdpHiG-sslhY4uYRNGZG-ZqnWaEy4qIU03PrNmkYsXmpxpZ-A-dDU__6F9RHSIvM9bM4BMJQb)

```java
// 코드 작성
public class HelloJava {

	public static void main(String[] args) {
		System.out.println("Hello Java !!!");   ⇐ 코드 추가
	}

}
```

![img](https://lh4.googleusercontent.com/7y3ugJet4K_vcK4sKJ-smeEcxJsTtm6PpxbnI3dlyv48FKO49PziS4jFnMNU61VkhLw3LifoHDRsTzkz5L4RIMqCY2r9zRdzBxI5WV6yeQTTbfa2dnEEtcbQFjxBQlUPOGnSZQb4)

![img](images/pasted%20image%200.png)

-> 콘솔 창에서 실행 결과 확인 가능

![image-20200217110736321](images/image-20200217110736321.png)

-> workpaces 폴더에 HelloJava 폴더가 생성된 것 확인 가능 (프로젝트 폴더)



