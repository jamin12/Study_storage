# Spring MVC
## __<span style="color:#9999ff">MVC란</span>__
- ![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FbdEZm6%2FbtrgVRNFj9a%2FLyPM5NdJixcvu8Qka8ULAK%2Fimg.png)
  ### __<span style="color:#ff9933">Model</span>__
    - 어플리케이션의 데이터이며 모든 데이터 정보를 가공하여 가지고 있는 컴포넌트이다.
    - 사용자가 이용하려는 모든 데이터를 가지고 있어야하며 View또는 controller에 대해 어떠한 정보도 알 수 없어야 한다.
    - 변경이 일어나면 처리 방법을 구현해야한다.
  ### __<span style="color:#ff9933">view</span>__
    - 시각적인 UI요소를 지칭하는 용어이다.
    - Model이 가지고 있는 데이터를 저장하면 안된다.
    - model이나 Controller에대해 정보를 알면 안되며 단순히 표시를 해주는 역할을 가지고 있다.
    - 변경이 일어나면 처리 방법을 구현해야한다.
  ### __<span style="color:#ff9933">Controller</span>__
    - Model이과 View를 연결해주는 역할을 한다
    - Model또는 View에 대한 정보를 알아야 한다.
    - Model또는 View에 변경을 인지하여 대처를 해야한다.
## __<span style="color:#9999ff">Spring MVC란</span>__
- ![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FbQMVxE%2FbtrgU1pt1jN%2FVANQneGCYtUBkfJKVf0gnk%2Fimg.png)
- ![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FbBqbyV%2FbtrsysIeeyv%2FWP43RUhxvA9rQVmBfUQZHK%2Fimg.png)
- __<span style="color:#14a492">HandlerMapping</span>__
  - 요정을 직접 처리할 컨트롤러를 탐색한다. 구체적인 mapping은 xml파일이나 java config관련 어노테이션등을 총해 처리할 수 있다.
- __<span style="color:#14a492">HandlerAdapter</span>__
  - 매핑된 컨트롤러의 실행을 요청한다.
- __<span style="color:#14a492">controller</span>__
  - 직접 요청을 처리하며 처리결과를 반환한다.
  - 여기서 결과가 반환 되면 HandlerAdapter가 ModelAndView객체로 변환되며, 여기에는 View Name과 같이 응답을 통해 보여줄 View에 대한 정보가 관련된 데이터가 포함되어 있다.
- __<span style="color:#14a492">view Resolver</span>__
  - View Name을 확인한 후, 실제 컨트롤러부터 받은 로직 처리결과를 반영할 View파일을 탐색한다.
- __<span style="color:#14a492">view</span>__
  - 로직 처리 결과를 반영한 최종 화면을 생성한다.

### __<span style="color:#ff9933">MVC패턴 동작 순서</span>__
  1. 클라이언트가 서버에 요청을 하면 FrontController인 DispatcherServlet클래스가 요청을 받는다.
  2. DispatcherServlet은 프로젝트 파일내의 Servlet-context.xml파일의 @controller인자를 통해 등록한 요청 위힘 컨트롤러를 찾아 매핑이된 컨트롤러가 존재하면 @RequestMapping을 통해 요청을 처리할 메소드로 이동한다.
  3. 컨트롤러는 해당 요청을 처리한 Service를 받아 비지니스 로직을 서비스에게 위임한다.
  4. Service는 요청에 필요한 작업을 수행하고 요청에 대해 DB접근을 해야한다면 DAO에 요청하여 처리를 위임한다.
  5. DAO는 DB정보를 DTO를 통해 받아 서비스에게 전달한다.
  6. 서비스는  전달받은 데이터를 컨트롤러에게 전달한다.
  7. 컨트롤러는 Model객체에 요청에 맞는 view를 정보를담바 DispatcherServlet에게 전송한다.
  8. DispatcherServlet은 ViewResolver에게 전달받은 View정보를 전달한다.
  9. Viewresolver는 응답할 View에대한 JSP를 찾아 DispatcherServlet에게 전달한다.
  10. DispatcherServlet은 응답할 뷰의 Render를 지시하고 뷰는 로직을 처리한다.
  11. DispatcherServlet은 클라이언트에게 Rending된 뷰를 응답하며 요청을 마친다.

# 참고
- https://ss-o.tistory.com/160
- https://catsbi.oopy.io/f52511f3-1455-4a01-b8b7-f10875895d5b
- https://the-greatest-developer.tistory.com/13
- https://kotlinworld.com/326
#java #spring