# Redhat Community Server Connectors 사용 가이드

> VSCode와 이 계열  IDE 들에서 (Antigravity, Cursor)
>
> Java 웹 프로젝트를 개발하면서 톰켓 연동해서 쓰기가 힘든 부분이 있는데, 
>
> 설정 방법에 대해 가이드를 작성해보기로 했다.



## 테스트 환경 IDE

VSCode는 많이 복잡해진 상태라... 건드리기가 싫어서 Antigravity IDE에서 확인해보기로 했다.

* Antigravity IDE 다운로드
  * https://antigravity.google/product/antigravity-ide





## 확장 설치

기본 Java 환경 확장들이나 설정들은 되었다고 간주하고, 

* **Extension Pack for Java**
  * https://open-vsx.org/vscode/item?itemName=vscjava.vscode-java-pack
  * 이 확장이면 거의 필요한것 다 들어가 있음.



추가로 설치해야할 확장은...  아래 두가지이다.

* **Runtime Server Protocol UI**
  * https://open-vsx.org/vscode/item?itemName=redhat.vscode-rsp-ui



* **Community Server Connectors**
  * https://open-vsx.org/vscode/item?itemName=redhat.vscode-community-server-connector





## IDE에서 웹 프로젝트 로드

[spring-mvc-sample](spring-mvc-sample) 이 프로젝트를 루트로 해서 Antigravity IDE에서 로드하면, 다음과 같은 화면으로 나타남.

* 샘플 프로젝트에 워크스페이스 설정에 약간의 편의를 위한 maven 확장용 즐겨찾는 명령과 rsp 관련 설정을 **.vscode/settings.json**에 미리 넣어놨음.

  ![image-20260528184229515](doc-resources/image-20260528184123327.png)

  * Maven의 즐겨찾기 설정한 명령이 좌하단에 나타난 것을 볼 수 있고, 해당 부분을 눌러서 바로 실행이 가능함.
  * rsp-ui의 startOnActivation는 Community Server Connector의 백앤드 서버를 바로 시작해둘지 여부이고, 웹프로젝트에서는 켜두는게 나아보임
  * rsp-ui.rsp.java.home은... 프로젝트의 Java 버전과 일치하게 하면 됨.



## 예제 프로젝트 빌드와 war:exploded

![image-20260528184736984](doc-resources/image-20260528184736984.png)

즐겨찾기 명령에서 바로 실행할 수 있고.

실행 후 target을 보면 Tomcat에 배포할 파일들이 잘 생성된 것을 확인할 수 있음

![image-20260528185022301](doc-resources/image-20260528185022301.png)



## Tomcat 서버 준비

Community Server Connector의 내장기능으로 WAS들의 자동 다운로드 기능이 있긴하지만....

뭔가 관리가 부실해보여서... Tomcat을 모아둘 별도 경로를 만들어두고 거기에 수동 다운로드 받은 후 연동하는게 나아보였다.



나는 아래 처럼 G 드라이브에 redhat-community-server-connector라는 폴더를 만들고, apache-tomcat-10.1.55-windows-x64.zip 압축파일을 공식 tomcat 홈페이지에서 받아서 압축을 풀었음.

```
G:\redhat-community-server-connector
  │   apache-tomcat-10.1.55-windows-x64.zip
  │
  └───apache-tomcat-10.1.55\
```

* 윈도우 64bit용 Tomcat 10.1.55 다운로드
  * https://dlcdn.apache.org/tomcat/tomcat-10/v10.1.55/bin/apache-tomcat-10.1.55-windows-x64.zip



## Community Server Connector에 서버 추가

IDE로 돌아와서...  Community Server Connector에서 오른쪽 메뉴를 열면..

Create New Server... 라고 있음.. 클릭!!

![image-20260528185542204](doc-resources/image-20260528185542204.png)

다운로드할 거냐고 묻는데, 위에서 미리 다운로드 받은 것을 쓸 것이므로 No 선택

![image-20260528185713445](doc-resources/image-20260528185713445.png)

경로는 아까 준비했던... 경로로 설정해주고..

![image-20260528185903853](doc-resources/image-20260528185903853.png)

나는 서버 네임만 Tomcat 10.x라고 기본 값 나타나던거 10.1.55로 명시적으로 써줬음.

이제 war:exploded 한 경로와 연결을 해줘야하는데...

![image-20260528190047609](doc-resources/image-20260528190047609.png)

방금 추가한 Tomcat 10.1.55 에서 오른쪽 마우스 메뉴 열어서 **Add Deployment**를 눌러줌.



그러면 배포 타입 선택창이 나옴...

![image-20260528190227045](doc-resources/image-20260528190203022.png)

WAR 파일을 사용하지 않고, Exploded된 경로를 사용하기로 했으므로 Exploded 선택하자!

`{프로젝트 루트}\target\spring-mvc-sample` 이 경로를 선택하면 됨



배포 파라미터 추가하길 원하는지 물어보는데...

![image-20260528190422589](doc-resources/image-20260528190422589.png)

Yes라고 해주자! 파라미터를 안주면...  

Context Root가 프로젝트 이름으로 고정되어버림..



다음 나타나는 아래 입력 창에서 출력이름을 ROOT로 아래처럼 지정해줘야한다.

![image-20260528190620760](doc-resources/image-20260528190620760.png)

만약 Exploded 방식이 아니라 war 파일 방식이였다면 ROOT.war로 적어야함.



또 다른 입력창이 또 뜨는데...

![image-20260528190906205](doc-resources/image-20260528190906205.png)

여기는 빈내용으로 넘어간다.



배포설정이 다음과 같이 추가되는데..

![image-20260528191016054](doc-resources/image-20260528191016054.png)

Full Publish를 해달라고 한다...😅

![image-20260528191124258](doc-resources/image-20260528191124258.png)

해주도록 하자!!!

![image-20260528191204766](doc-resources/image-20260528191204766.png)

상태가 동기화된것을 알 수 있음...





이제 실행해보자!!

아래처럼 Tomcat 10.1.55 부분에서 오른쪽 마우스 메뉴 열어서 Start Server 눌러주자!!

![image-20260528191256799](doc-resources/image-20260528191256799.png)





Output 창에서 Tomcat 서버 실행로그가 잘 뜨는 것을 확인할 수 있음.

![image-20260528191436967](doc-resources/image-20260528191436967.png)



브라우저에서 `http://localhost:8080` 으로 접근해보면..

![image-20260528191530736](doc-resources/image-20260528191530736.png)

잘 실행됨을 확인할 수 있음...👍👍





## 의견

확실히 Eclipse나 IntelliJ에서 Tomcat 설정해서 사용하는 것보단 불편한 점이 있긴해도... 

그래도 아무것도 없이 수동설정하는 것 보단 훨씬 나아서...

잘 쓸 수 있을 것 같다. 😊🎉🎉