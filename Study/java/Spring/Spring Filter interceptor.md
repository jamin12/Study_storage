# SpringBoot의 Filter,그리고 Interceptor
- 웹을 개발하다보면 공통적으로 처리해야 할 업무들이 많다.</br> 예를들어 로그인관련,권한체크,등등이 있다. 이러한 공통 업무들은 하나의 페이지가 아니라 여러 페이지에 공통적으로 적용된다. 공통 업무들의 코드를 모든 페이지에 적용한다면, 중복 코드가 많아지게 되고 프로젝트 단위가 커질수록 서버에 부하를 줄 수 있으며 소스 관리조차 어려워지게 된다.</br>때문에 **공통 코드 부분은 따로 빼서 관리**하는 것이 좋다.</br> 이러한 공통 업무를 프로그램 흐름의 앞, 중간, 뒤에 추가해서 자동으로 처리할 수 있는 방법이 크게 3가지가 있다.
  1. Filter
  2. Interceptor
  3. AOP
- 위 3가지는 각각 다른 특징들을 가지고 있다.
- ![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2Fb0DgB5%2FbtrcgPF7khm%2FKXudi8dL48kQEwfbkJeFKK%2Fimg.png)
1. 적용시점이 다르다
   - Filter -> interceptor -> AOP -> interceptor -> Filter
2. 적용 방식이 다르다
   - Filter -> web.xml, InterCeptor - servlet-context.xml
3. 실행 형식이 다르다
   - Interceptor와 Filter는 Servlet 단위에서 실행되는 반면, AOP는 메소드 앞에 Proxy형태로 실행된다.
## __<span style="color:#9999ff">Filter</span>__
- Filter란 Web Application에서 관리되는 영역으로써, Spring Boot Framwork에서는 Client로부터 오는 요청/응답에 대해서 최초/ 최종 단계의 위치에 존재하며, 이를 통해서 요청/ 응답의 정보를 변경하거나 Spring에 의해서 데이터가 변환되기 전의 순수한 Client의 요청/ 응답값을 확인할 수 있다.</br>때문에 유일하게 ServletRequest, ServletResponse의 객체를 변환할 수 있다.</br> 주로 Spring Framework에서는 Request/Response의 Logging용도로 활용하거나, 인증과 관련된 Logic들을 해당 Filter에서 처리한다. 이를 선/후 처리함으로써 Service Business Logic과 분리시킨다
- ![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FcWUKCK%2Fbtrb8UuANHA%2FVIHEKxMVZukj8Qb1LGLxEk%2Fimg.png)
1. __<span style="color:#ff9933">실행 메소드</span>__
   - init()- 필터 인스턴스 초기화
   - doFilter() - 전 / 후 처리
   - destroy() - 필터 인스턴스 종료
2. __<span style="color:#ff9933">예시 코드</span>__
   - ApiController.java
     - ``` java
          @Slf4j // logging을 위한 롬복. 이제 하나하나 System.out.println안해도 된다.
          @RestController
          @RequestMapping("/api/user")
          public class ApiController {
              @PostMapping("")
              public User user(@RequestBody User user){
                  log.info("User : {}" ,user); // System.out.println 대체
                  return user;
              }
          }
    - GlobalFilter.java
      - ``` java
          @Slf4j
          @Component
          public class GlobalFilter implements Filter {
              @Override
              public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain) throws IOException, ServletException{
                  // 전처리 (Request)
                  ContentCachingRequestWrapper httpServletRequest  = new ContentCachingRequestWrapper((HttpServletRequest)request);
                  ContentCachingResponseWrapper httpServletResponse = new ContentCachingResponseWrapper((HttpServletResponse)response);

                  // doFlter를 기점으로 전 / 후 처리
                  chain.doFilter(httpServletRequest,httpServletResponse);
                  String url = httpServletRequest.getRequestURI();

                  // 후처리 (Response)
                  String reqContent = new String(httpServletRequest.getContentAsByteArray());
                  log.info("request url : {}, request body : {}", url, reqContent);

                  String resContent = new String(httpServletResponse.getContentAsByteArray());
                  int httpStatus = httpServletResponse.getStatus();

                  httpServletResponse.copyBodyToResponse(); // Body내용을 읽고 모두 다 빼버려서 다시 한번 copy해서 넣어준다. 그래야 client도 return된 body를 확인할 수 있다.

                  log.info("response status : {}, responseBody : {}", httpStatus, resContent);
              }
          }
    -  ContentCachingRequestWrapper / ResponseWrapper을 통해 Request body와 Reponse body의 데이터를 캐치하여 로그를 남길 수 있다.
## __<span style="color:#9999ff">Interceptor</span>__
- 필터는 스프링 컨텍스트 외부에 존재하며 스프링과 무관한 자원에 대해 동작한다.</br> 하지만 인터셉터는 스프링의 DispatcherServlet이 컨트롤러를 호출하기 전 / 후로 끼어들기 때문에 스프링 Context 내부에서 Controller(Handler)에 관한 요청과 응답에 대해 처리한다.
- 스프링의 모든 빈 객체에 접근할 수 있으며, 여러 개의 인터셉터 사용이 가능하다.
- interceptor란 Filter와 매우 유사한 형태로 존재하지만, 차이점은 Spring Context에 등록된다.</br>AOP와 유사한 기능을 제공할 수 있으면 주로 인증 단계를 처리하거나 Loggging을 하는데 사용된다.
- ![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FI8SpM%2FbtrhFsTVbPb%2F4YqABGkbK5zDkY4VVpdX0K%2Fimg.png)
1. __<span style="color:#ff9933">실행 메소드</span>__
   - preHandler() - 컨트롤러 메소드가 실행되기 전
   - PostHandler() - 컨트롤러 메소드 실행 직후 view 페이지가 렌더링 되기 전
   - afterCompletion() - view 페이지가 렌더링 되고 난 후
2. __<span style="color:#ff9933">예시코드</span>__
   - publicController
     - ``` java
        @RestController
        @RequestMapping("/api")
        public class PublicController {
            @GetMapping("/hello")
            public String hello(){
                return "public hello";
            }
        }
    - PrivateController
      - ``` java
        @RestController
        @RequestMapping("/api/private")
        @Auth
        @Slf4j
        public class PrivateController {
            @GetMapping("/hello")
            public String hello(){
                return "private hello";
            }
        }
    - 보다시피 public와는 다르게 Auth 어노테이션이 붙여있다.
    - InterceptorApplication
      - ``` java
            @SpringBootApplication
            public class InterceptorApplication {

                public static void main(String[] args) {
                    SpringApplication.run(InterceptorApplication.class, args);
                }

            }
    - MvcConfig
      - ``` java
            @Configuration
            @RequiredArgsConstructor
            public class MvcConfig implements WebMvcConfigurer {
                private final AuthInterceptor authInterceptor;
                @Override
                public void addInterceptors(InterceptorRegistry registry){
                    registry.addInterceptor(authInterceptor);
                }
            }
    - AuthInterpretor
      - ``` java
            @Slf4j
            @Component
            public class AuthInterceptor implements HandlerInterceptor {
                @Override
                public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
                    String url = request.getRequestURI();
                    log.info("request URL : {}", url);
                    boolean hasAnnotation = checkAnnotation(handler, Auth.class);
                    log.info("has annotation : {}", hasAnnotation);
                    return true;
                }

                private boolean checkAnnotation(Object handler, Class clazz){
                    // resuorce에 대한 요청일때는 통과 ex : javascript, html..
                    if(handler instanceof ResourceHttpRequestHandler){
                        return true;
                    }
                    // annotation이 달려있나 확인
                    HandlerMethod handlerMethod = (HandlerMethod) handler;
                    if(null != handlerMethod.getMethodAnnotation(clazz)|| null != handlerMethod.getBeanType().getAnnotation(clazz)){
                        return true;
                    }
                    return false;
                }
            }
# 참고
- https://astrid-dm.tistory.com/465
- https://astrid-dm.tistory.com/m/466
#java #spring