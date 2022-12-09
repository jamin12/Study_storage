# Spring boot Spring 차이점
## __<span style="color:#9999ff">Spring</span>__
- 스프링 프레임워크는 자바 플랫폼을 위한 오픈소스 애플리케이션 프레임워크로서 스프링이라고 불린다. 동적인 웹 사이트를 개발하기 위한 여러 가지 서비스를 제공하고 있다.
  ### __<span style="color:#ff9933">특징</span>__
  - __<span style="color:#14a492">DI</span>__
    - 개발자가 Spring 프레임워크에 의존성을 주입하면서 객체 간 결합을 느슨하게 하는 것이다.
    - 객체 간 결합이 느슨하면 코드의 재사용성이 증가하고 단위 테스트가 용이해진다.
  - __<span style="color:#14a492">IOC</span>__
    - IOC는 컨트롤러의 제어권이 개발자에게 있는 것이 아닌 프레임워크가 대신해서 해주 것을 말한다. Servlet이나 Bean같은 코드를 개발자가 직접 작성하지 않고, 프레임워크가 대신 수행한다.
  - __<span style="color:#14a492">AOP</span>__
    - AOP는 핵심기능을 제외한 부수적인 기능을 프레임워크가 제공하는 특징이다.
  - __<span style="color:#14a492">중복코드 제거</span>__ 
    - 예를 들어 JDBC 같은 템플릿을 사용할 때 중복되는 코드도 많고 복잡한데 이를 모두 제거한다.
  - __<span style="color:#14a492">다른 프레임워크와의 통합</span>__
    - JUnit, Mockito와 같은 유닛 테스트 프레임워크와 통합이 간단하다.
## __<span style="color:#9999ff">Spring boot</span>__
- 스프링 프레임워크는 기능이 많은만큼 환경설정이 복잡한 편이다. 이에 어려움을 느끼는 사용자들을 위해 나온것이 바로 스프링 부트다. 스프링 부트는 스프링 프레임워크를 사용하기 위한 설정의 많은 부분을 자동화하여 사용자가 정말 편하게 스프링을 활용할 수 있도록 도와준다.
## __<span style="color:#9999ff">차이점</span>__ 
  ### __<span style="color:#ff9933">Dependency</span>__
    - Spring의 경우 dependency를 설정해줄 때 설정 파일이 매우 길고, 모든 dependency에 대해 버전 관리도 하나하나 해줘야 한다. 하지만 spring boot는 starter를 통해 dependency가 관리된다(버전관리 또한)
  ### __<span style="color:#ff9933">Configuration</span>__
    - spring에 경유 configuration설정을 할 때도 매우 길고 모든 어노테이션 및 빈 등록 등을 설정해줘야 한다.
    - spring boot는 application.properties파일이나 application.yml 파일에 설정하면 된다.
  ### __<span style="color:#ff9933">autoconfiguration</span>__
    - @SpringBootApplication 덕분에 많은 외부 라이브러리, 내장 톰캣서버등이 실행될 수 있다.
    - @ComponentScan: @component, @controller, @repository, @service라는 어노테이션이 붙어있는 객체들을 스캔해 자동으로 Bean에 등록해준다.
    - @EnableAutoConfiguration: @ComponentScan 이후 사전에 정의한 라이브러리들을 Bean에 등록해준다.
  ### __<span style="color:#ff9933">편리한 배포</span>__
    - sprring의 경울 war파일을 web Application server에 담아 배포있지만
    - spring boot의 경우에는 Tomcat이나 Jetty같은 내장WAS를 가지고 있기 떄문에 JAR 파일로 간편하게 배포할 수 있다.
# 참고
- https://velog.io/@courage331/Spring-%EA%B3%BC-Spring-Boot-%EC%B0%A8%EC%9D%B4
- https://programforlife.tistory.com/68