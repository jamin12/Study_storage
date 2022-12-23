# REST API
- Rest Api는 REST아키텍처 스타일을 준수하는 API를 말한다.
## __<span style="color:#9999ff">API</span>__
- API(Application Programming Interface)는 응용 프로그램에서 사용할 수 있도록, 운영체제 프로그래밍 언어 또는 특정 서비스가 제공하는 기능을 제어할 수 있게 만든 인터페이스이다.
- 프로그램과 프로그램 사이의 인터페이스
- 클라이언트,서버와 같은 서로 다른 프로그램에서 요청과 응답을 주고받을 수 있도록 만든 체계
## __<span style="color:#9999ff">REST</span>__
- REST는 HTTP를 이용해서 기계간의 통신이 일어날 때 HTTP의 잠재력을 최대한으로 이끌어내는 소프트웨어 아키텍쳐의 한 형식이다.
- 서버 프로그램은 여러 웹 브라우저 뿐만 아니라 앱통신에 대응해야한다. 즉 멀티 플랫폼 멀티 디바이스를 지원해야하는데 매번 서버를 새로 만들지 않기 위해 범용적인 사용성을 보장해야한다. REST는 이러한 범용성을 보장하는 아키텍쳐이다.
- `구체적으로는 REST는 HTTP URI(Uniform Resource Identifier)을 통해 자원(Resource)을 명시하고, HTTP Method(POST, GET, PUT, DELETE)를 통해 해당 자원에 대한 CRUD Operation을 적용하는 것을 의미한다.`
- ![](https://gmlwjd9405.github.io/images/network/rest.png)
1. ### __<span style="color:#ff9933">REST 구성 요소</span>__
   1. 자원(Resource):URI
      - 모든 자원에 고유한 ID가 존재하고, 이 자원은 Server에 존재 한다.
      - 자원을 구별하는 ID는 /movies/:id와 같은 HTTP URI이다.
      - Client는 URI를 이용해서 자원을 지정하고 해당 자원의 상태에 대한 조작을 서버에 요청한다.
   2. 행위(verb): HTTP Method
      - HTTP 프로토콜의 Method를 사용한다.
      - HTTP프로토콜은 GET, POST, PUT, DELETE와 같은 메서드를 제공한다.
   3. 표현(Represenation of Resource)
      - 클라이언트가 자원의 상태에 대한 조작을 요천하면 서버는 이에 적절한 응답을 보낸다.
      - REST에서 하나의 자원은 JSON, XML, TEXT, RSS등 여러 형태의 Representation으로 나타내어 진다.
      - JSON혹은 XML을 통해 데이터를 주고 받는것이 일반적이다.
2. ### __<span style="color:#ff9933">REST 특징</span>__
   1. __<span style="color:#14a492">server-client(서버 클라이언트 구조)</span>__
      - 자원이 있는쪽이 Server, 자원을 요청하는 쪽이 Client가 된다.
        - Rest Server: API를 제공하고 비즈니스 로직 처리 및 저장을 책임진다.
        - Client: 사용자 인증이나 Context(세션, 로그인 정보)등을 직접 관리하고 잭임진다.
      - 서로간의 의존성이 줄어든다.
   2. __<span style="color:#14a492">stateless(무상태)</span>__
      - HTTP 프로토콜은 Stateless Protocol이므로 REST역시 무상태성을 갖는다.
      - Client의 Context를 Server에 저장하지 않는다.
        - 즉, 세션과 쿠키 같은 context정보를 신경쓰지 않아도 되므로 구현이 단순해진다.
      - 서버는 각각의 요청을 완전히 별개의 것으로 인식하고 처리한다.
        - 각 API서버는 Client의 요청만을 단순 처리한다.
        - 즉, 이전의 요청이 다음 요청의 처리에 연관되어서는 안된다.
        - 물론 이전 요청이 DB를 수정하여 DB에 의해 바뀌는것을 허용한다.
        - 서버의 처리 방식에 일관성을 부여하고 부담이 줄어들며, 서비스의 자유도가 높아진다.
   3. __<span style="color:#14a492">Cacheable(캐시 처리 기능)</span>__
      - 웹 표준 HTTP 프로토콜을 그대로 사용하므로 웹에서 사용하는 기존의 인프라를 그대로 활용할 수 있다.
        - 즉, HTTP가 가진 가장 강력한 특징 중 하나인 캐싱 기능을 적용할 수 있다.
        - HTTP프로토콜 표준에서 사용하는 Last-Modified 태스나 E-Tag를 이용하면 캐싱 구현이 가능하다.
      - 대량의 요청을 효율적으로 처리하기위해 캐시가 요구된다.
      - 캐시 사용을 통해 응답시간이 빨라지고 REST server 트랜잭션이 발생하기 않기 떄문에 전체 응답시간, 성능, 서버의 자원 이용률을 향상 시킬 수 있다.
   4. __<span style="color:#14a492">Layered System(계층화)</span>__
      - Client는 REST API Server만 호출한다.
      - REST server는 다중 계층으로 구성될 수 있따.
        - API server는 순수 비즈니스 로직을 수행하고 그 앞단에 보안,로드 밸런싱, 암호화, 사용자 인증 등을 추가하요 구조상의 유연성을 줄 수 있다.
        - 로드밸런싱, 공유 캐시등을 통해 확장성과 보안성을 향상시킬 수 있다.
      - Proxy, 게이트웨어 같은 네트워크 기반의 중간 매체를 사용할 수 있다.
   5. __<span style="color:#14a492">Code-On-Demand(optional)</span>__
      - Server로부터 스크립트를 받아서 Client에서 실행한다.
      - 반드시 충졸할 필요는 없다.
   6. __<span style="color:#14a492">Uniform Interface(인터페이스 일관성)</span>__
      - URI로 지장헌 자원에 대한 조작을 통일되고 한정적인 인터페이스로 수행한다.
      - HTTP표준 프로토콜에 따르는 모든 플랫폼에서 사용이 가능하다.
        - 특정 언어나 기술에 종속되지 않는다.
2. ### __<span style="color:#ff9933">REST API 설계 기본 규칙</span>__
   0. __<span style="color:#14a492">참고 리소스 원형</span>__
      - 도큐먼트: 객체 인스턴스나 데이터베이스 레코드와 유사한 개념
      - 컬렉션: 서버에서 관리하는 디렉터리라는 리소스
      - 스토어: 클라이언트에서 관리하는 리소스 저장소
   1. __<span style="color:#14a492">URL은 자원을 표현해야 한다.</span>__
      - resource는 동사보다는 명사를, 대문자 보다는 소문자를 사용한다.
      - resource의 도큐먼트 이름으로는 단수 명사를 사용해야 한다.
      - resource의 컬렉션 이름으로는 복수 명사를 사용해야 한다.
      - resource의 스토어 이름으로는 복수 명사를 사용해야한다.
      - ``` text
        get /Member/1 -> GET /members/1
   2. __<span style="color:#14a492">자원에 대한 행위는 HTTP Method로 표현한다.</span>__
      - URL에 HTTP method가 들어가면 안된다.
        - ```
            GET /members/delete/1 → DELETE /members/1
      - URL에 행위에 대한 동사 표현이 들어가면 안된다.
        - ``` 
            GET /members/show/1 → GET /members/1
            GET /members/insert/2 → POST /members/2
      - 경로 부분 중 변화하는 부분은 유일한 값으로 대체한다.(즉, :id는 하나의 특정 resource를 나타내는 고유값이다.)
        - ```
            student를 생성하는 route : POST /students
            id=12인 student를 삭제하는 route : DELETE /students/12
3. ### __<span style="color:#ff9933">REST API 설계 규칙</span>__
   - 슬래스 구분자(/)는 계층 관게를 나타내는데 사용한다.
   - URL 마지막 문자로 슬래시를 포함하지 않는다.
   - 하이픈(-)은 URL 가독성을 높이는데 사용한다.
   - 밑중(_)은 URL에 사용하지 않는다.
   - URL경로에는 소문자가 적합하다.
   - 파일확장자는 URL에 포함하지 않는다.
   - 리소스간에는 연관관계가 있는 경우
     - ```
        /리소스명/리소스 ID/관계가 있는 다른 리소스명
        GET : /users/{userid}/devices (일반적으로 소유 'has'의 관계를 표현할 때)
        ```

# 참고
- https://aws.amazon.com/ko/what-is/restful-api/
- https://github.com/hongcheol/CS-study/tree/main/CommonSense#rest-api
- https://hanamon.kr/rest-api/
- https://gmlwjd9405.github.io/2018/09/21/rest-and-restful.html