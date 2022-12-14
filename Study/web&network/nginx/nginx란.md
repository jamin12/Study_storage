# Nginx란?
## __<span style="color:#9999ff">Nginx란</span>__
- 트래픽이 많은 웹사이트에서 서버(WAS)를 도와주는 비동기 이벤트 기반구조의 웨 서버 프로그램이다.
## __<span style="color:#9999ff">Nginx의 배경</span>__
  1. ### __<span style="color:#ff9933">Apache</span>__
     - 아파치 서버는 요청이 들어오면 커넥션을 형성하기 위해 프로세스를 생성한다.
     - ![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FHsQhw%2FbtrzQH0nHt2%2FsUNTsv4ufuA8ZdFMv0FpJ1%2Fimg.png)
     - 새로운 요청이 들어올 때마다 프로레스를 새로 만든다. 프로세스는 생성 시간이 오래 걸리므로 요청이 들어오기 전에 프로세스를 생성하는 prefork방식을 사용했다. 그래서 새로운 클라이언트 요청이 들어오면 미리 만들어 놓은 프로세스를 가져다 사용했다.
     - 만약 만들어 놓은 프로세스를 모두 할당되면 추가로 프로세스를 만들었다.
     - 이러한 구조는 개발하기 쉽다는 장점이 있다. 
     - ![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FdS4uEZ%2FbtrzQFBeEsU%2FCKKps6QnC1JDkPMJgFwrK0%2Fimg.png)
     - 덕분에 개발자는 다양한 모듈을 만들어서 서버에 빠르게 기능을 추가할 수 있었다.
     - 이런식으로 아파치 서버는 동적 컨텐츠를 처리할수 있게 되었다.
     - 확장성이 좋다는 장점 덕분에 요청을 받고 응답을 처리하는 과정을 하나의 서버에서 해결하기 좋았다.
     - ![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FydfCO%2FbtrzLIFPFLs%2FXeENKRemCRef3ZCMuECEk1%2Fimg.png)
     - 하지만 서버에 동시 연결된 커넥션이 많아졌을 때 더이상 커넥션을 형성하지 못하는 문제가 생겼다.
     - 이를 C10K(Connection 10000 problem)문제 라고 한다.
       - 커넥션마다 프로세스를 할당하기엔 메모리 부족으로 이어짐
       - 확장성이라는 장점이 프로세스의 리소스 양을 늘려서 무거운 프로그램이 되었음
       - 많은 커넥션에서 요청이 들어오면 CPU 부하가 높아짐
     - 즉, 수많은 동시 커넥션을 감당하기엔 아파지 서버의 구조가 적합하지 않았다.
  2. ### __<span style="color:#ff9933">NginX</span>__
     - ![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FcfXvlr%2FbtrzUiES8cJ%2FL3e0u4bUl4xyk7hAHnH41k%2Fimg.png)
     - 2004년에 새로운 구조를 채택하면서 아파지 서버를 보완하기 위한 소프트웨어가 나왔는데 이것이 NGINX이다. 초창기에는 아파치 서버와 NGINX는 함께 사용하기 위해 만들어 졌다.
     - 수많은 동시 커넥션을 NGINX가 유지하고 웹서버이기에 정적 파일에 대한 요청을 스스로 처리하여 아파치 서버의 부하를 줄였따.
     - 웹 서버의 역할의 NGINX는 클라이언트로부터 동적 파일 요청을 받았을 때만 뒤에 있는 아파치 서버와 커넥션을 형성했다.
## __<span style="color:#9999ff">Nginx의 구조</span>__
- ![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FbUefWi%2FbtrzUix8nEV%2Fk7IDvC3SjVYZbR5S3ekQkK%2Fimg.png)
- Nginx는 가장 먼저 마스터 프로세스(master process)라는 것을 볼 수 있다.
- 설정 파일을 읽고 워커 프로세스(worker process)를 생성하는 프로세스이다.
- 이 워커 프로세스가 실제로 일을 하는데, 워커 프로세스가 생성될 때 각자 지정된 listen 소켓을 배정 받는다.
- 그 소켓에 새로운 클라이언트 요청이 들어오면 커넥션을 형성하고 처리한다.
- 커넥션은 정해진 Keep Alive시간만큼 유지되는데, 이렇게 커넥션이 형성되었다고 해서 워커 프로세스가 커넥션 하나만 담당하진 않는다. 
- 형성된 커넥션에 아무런 요청이 없으면 새로운 커넥션을 형성하거나 이미 만들어진 다른 커넥션으로부터 들어온 요청을 처리한다.
- Nginx에서는 이런 커넥션 형성과 제거 새로운 요청을 처리하는 것을 이벤트(event)라고 부른다.
- ![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FMJmKR%2FbtrzRqXWYVL%2FqE1yuidBZCH2tc1PhSE2wk%2Fimg.png)
- 이벤트들은 OS 커널이 큐 형식을 워커 프로세스에게 전달해준다.
- 이벤트는 큐에 담긴 상태에서 워커 프로세스가 처리할 때까지 비동기 방식으로 대기한다.
- 그리고 워커 프로세스는 하나의 스레드로 이벤트를 꺼내어 처리해 나간다.
- 아파치 서버는 커넥션 연결 후 요청이 없다면 방치되는 반면  NGINX는 커넥션 연결 후 요청이 없으면 다른 커넥션의 요청을 처리하거나 새로운 커넥션을 형성하므로 아파치 서버에 비해 자원을 효율적으로 쓰는 것을 알 수 있다.
- ![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FIG1lI%2FbtrtXr85ltE%2FwVqobDmK5GnDX7USX4P8v1%2Fimg.jpg)
- 막약 큐에 담긴 요충 중 하나가 시간이 오래 걸린다면
- 스레드 풀을 만들어 그 요청을 따로 수행하게 된다.
- 워커 프로세스는 처리할 요청이 시간이 오래 걸릴 것 같으면 스레드 풀에 이벤트를 위임하고 이벤트를 처리한다.
- 이런 워커 프로세스는 보통 CPU 코어 수만큼 생성하게 된다.
- 그래서 코어가 담당하는 프로세스를 바꾸는 횟수를 줄이기에 CPU의 컨텍스트 스위칭을 줄이게 된다. 이것이 NGINX가 사용하는 EVENT-Driven Model(이벤트 기반 구조)이다.
## __<span style="color:#9999ff">Nginx의 장 단점</span>__
  1. ### __<span style="color:#ff9933">단점 </span>__
     - 개발자가 직접 모듈을 만들기가 까다롭다.
  2. ### __<span style="color:#ff9933">장점</span>__
     - 프로세스를 적게 만들다 보니 가볍다.
     - 이런 프로세스를 적게 만드는 구조는 Nginx의 설정을 동적으로 바꾸는 것을 가능하게 한다.
       - 동시 커넥셩 양 최소 10배 증가(일반적으로 100 ~ 1000배 증가)
       - 동일한 커넥션 수일 때 속도 2배 향상
       - 동적 설정 변경

# 참고
- https://ssdragon.tistory.com/60
- https://dkswnkk.tistory.com/513