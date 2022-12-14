# web과 was
## __<span style="color:#9999ff">웹 서버</span>__
- 웹 브라우저 클라이언트로부터 HTTP 요청을 받아들이고 HTML 문서와 같은 웹 페이지를 반환한느 컴퓨터 프로그램
- 웹 서버란 클라이언트가 웹 브라우저에 어떠한 페이지 요청을 하면 웹 서버에서 그 요청을 받아 **정적 컨텐츠를 제공한느 서버이다.** 여기서 정적 컨텐츠랑 **단순 HTML,CSS, javascript, 이미지, 파일 등 즉시 응답 가능한 컨텐츠이다.**
- 웹 서버가 동적 컨텐츠 요청을 받으면 was에게 해당 요청을 넘겨주고, was에서 처리한 결과를 클라이언트에게 전달해주는 역할도 한다.
- ![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FpxLJe%2FbtqBTL7f4OE%2FO54n7FkOZUKAItvIPUpwGK%2Fimg.png)
## __<span style="color:#9999ff">WAS</span>__
- 인터넷 상에서 HTTP 프로토콜을 통해 사용자 컴퓨터나 장치에 애플리케이션을 수행해주는 미들웨어로서, 주로 동적 서버 컨텐츠를 수행하는 것으로 웹 서버와 구별이 되며, 주로 데이터베이스 서버와 같이 수행 된다.
- WAS는 웹 서버와 웹 컨테이너가 합쳐진 형태로서, 웹 서버 단독으로는 처리할 수 없는 **데이터베이스의 조회다 다양한 처리가 필요한 동적 컨텐츠를 제공한다.** 덕분에 다양한 요구에 맞춰 웹 서비스를 제공할 수 있다. WAS는 JSP, Servlet 구동환경을 제공해주기 때문에 웹 컨테이너 혹은 서블릿 컨테이너라고도 불린다.
- ![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FpAqij%2FbtqBS7bDSam%2FGJDanZaV3kMKPqfXtlEqL0%2Fimg.png)

## __<span style="color:#9999ff">WAS vs WEB</span>__
- WAS는 DB 조회 및 다양한 로직을 처리하는데 집중해야 한다. 따라서 단순한 정적 컨텐츠는 웹 서버에게 맡기며 기능을 분리시켜 서버 부하를 방지한다. 만약 WAS가 정적 컨텐츠 요청까지 처리하면, 부하가 커지고 동적 컨텐츠 처리가 지연되면서 수행속도가 느려지고 이로인해 페이지 노출 시간이 늘어나는 문제가 발생하여 효율성이 크게 떨어진다.
- 그렇기 떄문에 둘다 써야한다.

# 참고
- https://codechasseur.tistory.com/25