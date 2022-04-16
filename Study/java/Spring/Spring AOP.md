# AOP(Aspect Oriented Programming, 관점 지향 프로그래밍)의 이해
## __<span style="color:#9999ff"> AOP의 이해</span>__
1. __<span style="color:#ff9933">[AOP의 등장]</span>__
   - AOP를 이해하기 위해서는 advice, pointcut을 이해해야한다.
     - advice: 타겟 오브젝트에 적용하는 부가 기능을 담은 오브젝트
     - pointcut: 메소드 선정 알고리즘을 담은 오브젝트
   - 부가기능은 핵심기능과 섞이면 설계와 코드가 지저분해지기 때문에 부가기능을 독립적인 모듈로 만들고자 하는 필요성이 대두되었다. 그리고 <span style="color:#ff0f">핵심 기능에 부여되는 부가 기능을 효과적으로 모듈화</span>하는 방법을 찾다가, 어드바이스와 포이트컷을 결합한 어드바이스가 발전하여 AOP가 만들어지게 되었다. 이러한 부가 기능 모듈은 기존의 객체지향 설계와 구분되는 새로운 특성이 있어서 ASpect라고 불리기 시작하였다. Aspect는 애플리케이션의 핵심 기능을 담고 있지는 않지만 애플리케이션을 구성하는 한 가지 요소로써 핵심 기능에 부가되어 의미를 갖는 모듈을 의미한다. 그리고 이렇게 애플리케이션의 핵심적인 기능에서 부가적인 기능을 분리하여 목특한 무듈로 만들고 설계하여 개발하는 방법을AOP라고 부른다.
   - AOP는 OOP를 대체하는 기술이 아니라 OOP를 돕는 보조 기술로써 핵심 기능을 설계하고 구현할 떄 객체지향적인 가치를 지킬 수 있도록 도와준다. 예를 들어 사용자 관리 기능을 개발할 때 핵심 로직 대신 트랜잭션 처리라는 관점에서 바라보고, 그 부분을 집중해서 설계하고 개발할 수 있도록 도와준다.
2. __<span style="color:#ff9933">[Spring의 AOP]</span>__
   - Spring의 AOP는 내부적으로 다음과 같이 동작을 하게된다.
     - 다이내믹 프록시 객체의 생성 요청
     - 포인트컷을 통해 부가기능 대상 여부 확인
     - 어드바이스로 부가 기능 적용
     - 실제 기능 처리
   - Spring AOP는 프록시를 이용하였으며 자바의 기본 JDK와 스프링 컨테이너 외에는 특별한 기술이나 환경이 필요로 하지 않는다. 하지만 이러한 프록시 방법은 반드시 Spring 컨테이너가 피룡하며 부가기능의 처리가 어려운 단점이 있어서 바이트를 조작하는 AOP가 등장하게 되었다.
3. __<span style="color:#ff9933">[AspectJ의 AOP]</span>__
   - Spring AOP외에 또 다른 강력한 AOP 프레임워크 중 하나인 Aspectj는 프록시를 이용하지 않는다 대신 AspectJ는 CGlib라는 바이트 조작 라이브러리를 사용하여 타깃 오브젝트의 바이트를 고쳐서 부가기능을 직접 넣어주는 방법(바이트조작)을 사용한다. 그래서 우리가 만든 코드에서는 부가기능이 분리되어있지만 바이트 코드에서 핵심 기능과 부가기능이 섞여있는 구조이다. AspectJ가 프록시를 사용하지 않고 CGLib를 이용한 복잡한 바이트 조작 방법을 사용하는 이유는 크게 2가지가 있다.
     - 바이트 코드를 조작하면 Spring과 같은 컨테이너의 도움이 필요 없기 떄문이다.
     - 프록시 방식보다 훨씬 강력하고 유연한 AOP를 제공할 수 있다.
   - 프록시를 이용해 프록시를 구현하려면 Spring의 빈으로 등록해야 하기 때문에 Spring에 종속적인 기술이 되어버린다는 단점이 있다. 반면에 바이트코드를 조작하면 스프링과 같은 컨테이너가 없는 환경에서도 AOP를 이용할 수 있다.
   - 또한 바이트 조작으로 AOP를 구현하면 객체의 생성, 필드 값의 조회와 조작 등과 같은 다양한 부가 기능을 추가할 수 있다. 하지만 프록시를 이용하는 방법으로는 이러한 부가 기능을 처리하기가 어렵다.
   - 그래도 대부분의 경우라면 Spring에서 제공하는 AOP로도 처리할 수 있다. 하지만 특별한 요구사항이 생겨 Spring AOP로 처리가 어렵다면 AspectJ를 이용하면 된다.
   - AOP를 정용하는 방법으로는 크게 다음 3가지를 고려하면 된다.
     - 클래스 또는 메소드의 이름 패턴으로 적용
     - 빈의 이름으로 적용
     - 어노테이션으로 적용
# AOP의 개념 및 사용 방법
## __<span style="color:#9999ff"> AOP 란?</span>__
1. __<span style="color:#ff9933">[AOP란?]</span>__
   - 프로그래밍을 하다보면 공통적인 기능이 많이 발생한다. 이러한 공통 기능을 모든 모듈에 적용하기 위해 상속을 이용한다. 하지만 JAVA에서는 다중 상속이 불가능하며, 상속을 받아 공통 기능을 부여하기에는 한계가 있다.
   - 예를 들어 우리가 개발한 API의 호출 시간을 측정하고 싶다고 하자. 이를 AOP없이 구현한다면 중복 코드가 발생할 소지가 있고 코드의 변경이 필요하면 여러 코드에 종속적으로 변경이 필요할 것이며,
   - 핵짐적인 비지니스 로직에 호출 시간 측정이라는 부수적인 로직이 추가되어 가독성과 효율성이 떨어지는 등의 문제가 발생할 수 있다. 이러한 문제점을 해결하기 위해 AOP가 등장하게 되었다 AOP는 새로운 프로그래밍 패러다임이 아니라 OOP를 돕는 보조적인 기술로, 핵심적임 관심 사항(Core Concern)과 공통 관심 사항(Cross-cutting Concern)을 분리시키고 각각을 모듈화 하는 것을 의미한다. 예를 들어 우리가 개발한 API중에 회원 가입 API가 있다면 핵심적인 관심 사항은 회원 가입이라는 비지니스 로직이 될 것이고 공통 관심 사항은 호출 시간 측정 로직이 될 것이다.
   - ![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FvbL54%2FbtqVbWXEFnj%2FdFxvFi9WX3JhaPA8qZrta1%2Fimg.png)
2. __<span style="color:#ff9933">[AOP의 장점]</span>__
   - 공통 관심 사항을 핵심 관심사항으로 분리시켜 핵심 로직을 깔끔하게 유지할 수 있다.
   - 그에 따른 코드의 가독성, 유지보수성 등을 높일 수 있다.
   - 각각의 모듈에 수정이 필요하면 다른 모듈의 수정 없이 해달 로직만 변경하면 된다.
   - 공통 로직을 적용할 대상을 선택할 수 있다.
## __<span style="color:#9999ff">패키지 기반, 어노테이션 기반으로 AOP 사용해보기</span>__
1. __<span style="color:#ff9933">[AOP 사용해보기]</span>__
   - 다른 요소들고 마찬가지로 AOP를 적용하기 위해서는 먼저 의존성을 추가해주어야 한다.
   - AOP 의존성을 추가하고 빌드를 하였으면 AOP를 활성화하겠다는 어노테이션을 추가해주어야 한다. SpringBoot의 애플리케이션 클래스에 @EnableAspectJAutoProxy 어노테이션을 추가해주도록 하자
   - ``` java 
      @EnableAspectJAutoProxy 
      @SpringBootApplication 
      public class DemoApplication { 
         public static void main(String[] args) { 
            SpringApplication.run(DemoApplication.class, args); 
         } 
      }
   - 위 두과정을 거치면 AOP를 적용하기 위한 준비가 끝난 것이다. 어디에 AOP를 적용하지 말고, 실행 시간을 측정하기 위한 Aspect클래스부터 개발해보도록 하자.
   - ``` java 
      @Aspect 
      @Component 
      @Log4j2 
      public class ExecutionTimeAop { 
         // 모든 패키지 내의 controller package에 존재하는 클래스 
         @Around("execution(* *..controller.*.*(..))") 
         public Object calculateExecutionTime(ProceedingJoinPoint pjp) throws Throwable { 
            // 해당 클래스 처리 전의 시간 
            StopWatch sw = new StopWatch(); 
            sw.start(); 
            // 해당 클래스의 메소드 실행 
            Object result = pjp.proceed(); 
            // 해당 클래스 처리 후의 시간 
            sw.stop(); 
            long executionTime = sw.getTotalTimeMillis(); 
            String className = pjp.getTarget().getClass().getName(); 
            String methodName = pjp.getSignature().getName(); 
            String task = className + "." + methodName; 
            log.debug("[ExecutionTime] " + task + "-->" + executionTime + "(ms)"); 
            return result; 
         } 
      }
   - 우선 AOP 클래스로 설정하기 위해 @Aspect 어노테이션을 추가해주었으며, Spring의 빈으로 등록하기 위해 @Component 어노테이션을 추가해줘었다. 그리고 우리가 하고자 하는 것은 모든 API의 실행 시간을 측정해주는 것이므로 StopWatch를 생성하여 측정을 시작하였다. 그리고 pjp의 proceed를 통해 실제 핵심 로식을 실행하여 Object클래스로 메소드의 결과를 받았다. 이후에 StopWatch를 중단하여 실행 시간을 밀리세컨드로 계산하여 로그를 출력하고 함수를 종료시키고 있다.
   - 이제 위와 같은 기능을 적용할 지점을 선정해야 하는데, 크게 패키지 기반으로 하는 방법과 어노테이션 기반으로 하는 방법이 있다.
2. __<span style="color:#ff9933">[패키지 기반으로 AOP 적용하기]</span>__
   - 먼저 Controller 패키지에 해당 기능을 적용하기 위해서는 다음과 같은 @Around를 적용해줄 수 있다.
   - @Around("execution(* *..controller.*.*(..))") 

3. __<span style="color:#ff9933">[어노테이션 기반으로 AOP 적용하기]</span>__
   - 반면에 어노테이션 기반으로 적용하기 위해서는 AOP를 처리하기 위한 어노테이션을 만들어주어야 한다.
   - ``` java
      @Retention(RetentionPolicy.RUNTIME) 
      @Target({ElementType.TYPE, ElementType.METHOD}) 
      public @interface ExecutionTimeChecker { 

      }
   - 위에서 @Target은 어노테이션이 적용될 레벨을 의미한다. 이번에는 클래스 또는 메소드에 적용하고자 TYPE, METHOD를 추가해주었다.
   - 그 다음에는 해당 어노테이션이 적용되도록 @Around를 수정해야한다.
   - @Around("@within(com.mang.atdd.membership.aop.ExecutionTimeChecker)")
   - 그리고 해당 메소드를 호출해보면 실행 시간을 측정하는 AOP가 클래스 레벨에는 물론 메소드 레벨까지 정상적으로 실행됨을 확인할 수 있다.
# Spring의 AOP 프록시 구현 방법(JDK동적 프록시,CGLIB프록시)과 @EnableAspectJAutoProxy의 proxyTargetClass
## __<span style="color:#9999ff">Spring의 AOP프록시 구현 방법(JDK Dynamic Proxy, CGLib proxy)</span>__
1. __<span style="color:#ff9933">[AOP에 대한 이해]</span>__
   - AOP는 부가 기능을 핵심 기능으로 부터 분리하기 위해 등장한 기술이다. 부가 기능을 분리함으로써 우리는 해당 로직을 재사용할 수 있고 핵심 기능은 핵심 열할에만 집중할 수 있도록 도와준다.
   - spring의 AOP는 기본적으로 프록시 방식으로 동작하도록 되어있는데,Spring에서 AOP를 활용하기 위해서는 @EnableAspectJAutoProxy 어노테이션을 붙여주어야 하면 이에대한 옵션으로 proxyTargetClass가 있다.</br>그리고 이 옵션을 주지 않으면 스프링 빈을 찾지 못하는 등의 에러가 발생할 수 있는데, 이를 이해하기 위해서 우리가 수동으로 구현하는 프록시 방식과 Spring이 자동으로 구현하는 프록시 방식에 대해 알아보고 왜 이 옵션이 생기게 되었는지 이해해보도록 하자
2. __<span style="color:#ff9933">[수동으로 직접 Proxy 구현]</span>__
   - 앞서 설명하였듯이 AOP를 구현하는 기본적인 방법은 프록시 패턴을 이용하는 것인데, 우리가 직접 AOP를 구현하고자 한다면 다음과 같이 구현할 수 있다.</br>예를 들어 다음과 같은 DiscountController와 DiscountService및 구현체가 있다고 하자
   - ``` java 
      @RestController 
      @RequiredArgsConstructor 
      public class DiscountController { 
         private final DiscountService discountService; 
      } 

      public interface DiscountService { 
         int discount(); 
      } 

      @Service 
      public class RateDiscountService implements DiscountService { 
         @Override 
         public int discount() { ... } 
      }
   - 우리는 RateDiscountService의 discount메소드가 호출되기 이전에 부가기능을 적용하기 위해 다음과 같이 인터페이를 구현하여 proxy객체를 직접 생성할 수 있다.
   - ``` java
      @Service 
      public class RateDiscountServiceProxy implements DiscountService { 
         // 여기서는 RateDiscountService에 해당한다. 
         private DiscountService discountService; 
         public DiscountServiceProxy(DiscountService discountService) { 
            this.discountService = discountService; 
         } 
      @Override 
      public int discount() { 
         // 1. 메소드 호출 전의 부가 기능 처리 

         // 2. 실제메소드 호출 
         this.discountService.discount() 
         // 3. 메소드 호출 후의 부가 기능 처리 
         }
      }
   - 위와 같이 실제 객체의 메소드가 호출 전/후에 처리해야 하는 부가기능을 추가함으로 써 AOP를 구현할 수 있다. 하지만 이러한 방식은 다음과 같은 문제점을 가지고 있다.
     - 불필요하게 DiscountService타입의 빈이 2개 등록됨
     - DI시 문제가 발생할 수 있음
   - Spring이 1개의 타입에 대해 불필요하게 여러 개의 빈을 관리해야 할 뿐만 아니라 해당 타입의 빈이 여러개이므로 의존성 주입시에도 문제가 발생할 여지가 있는 것이다. 물론 변수 이름이나 지시자 등으로 피할 수 있지만 이는 번거롭다.
3. __<span style="color:#ff9933">[Spring의 JDK dynamic proxy구현]</span>__
   - 위와 같은 문제를 해결하기 위해 Spring은 java 언어 차원에서 제공하는 자동 프록시 생성기를 통해 직접 프록시 객체를 생성한 후에 특별한 처리를 해주는데 이를 JDK동적 프록시 또는 JDK 다이나믹 프록시라고 부른다. Spring은 프록시를 구현할 때 프록시를 구현한 객체를 마치 실제 빈인 것처럼 포장하고, 2개의 빈을 모두 등록하는 것이 아니라 실제 빈을 프록시가 적용된 빈으로 바꿔치기 한다.</br>이러한 방식이 가능한 이유는 프록시 구현 대상이 인터페이스를 구현하고 있으며, Spring이 프록시 구현체를 만들때 프록시 대상과 동일한 인터페이스를 구현하도록 했기 때문이다. 즉, 프록시 대상과 프록시 구현체 모두 동일한 인터페이스 타입 및 구현체이기 때문에 기존 RateDiscountSAervice빈을 RateDiscountServiceProxy로 바꿔치기하고, 빈 후처리기를 통해 이미 정의된 의존 관계 역시 바꿀 수 있는 것이다.
   - ![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2F8Hwlj%2Fbtq93m1xAoj%2Fano2xaOrSnrV90EdrgG1pk%2Fimg.png)
   - 기존에는 왼쪽과 같이 실제 빈과 proxy빈 2개가 스프링 컨테이너에 등록되었다면, Spring이 JDK 동적 프록시를 구현할 때에는 Proxy빈을 실제 빈처럼 구현하고 기존의 빈을 대체하도록 하는 것이다.
   - 이러한 방식이 괜찮아 보이지만 만약 실제 빈을 직접 참조하고 있는 경우라면 문제가 발생 한다.
   - 위의 예제에서 문제가 발생하니 않은 이유는 실제 빈(RateDiscountService)이 DiscountService라는 인터페이스에 의존하고 있고, DiscountController에서도 DiscountService에 의존하고 있기 떄문이다.
   - 하지만 만약 다음과 같이 결제를 담당하는 PaymentService에서 구체 클래스(RateDiscountService)를 주입받고 있다면 어떻게 될까?
   - ``` java
      @Service 
      @RequiredArgsConstructor 
      public class PaymentService { 
         private final RateDiscountService rateDiscountService; 
      }
   - Spring이 새롭게 추가한 RateDiscountServiceProxy는 DiscountService인터페이스를 구현한 클래스이지 RateDiscounterService를 상속받는 클래스가 아니다. 그래서 RateDiscountService타입의 빈을 찾을 수 없어 에러가 발생 하게 된다.
   - ![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FRFrCc%2Fbtq93oSyfXa%2FkraCOVIpwbuHSeutdYUJJk%2Fimg.png)
   - 또한 JDK dynamix proxy는 인터페이스를 기반으로 프록시를 생성하는데, 인터페이스를 구현한 클래스가 아니면 프록시 객체를 생성할 수 없다. 즉, JDK 동적 프록시는 다음과 같은 두 가지 한계점을 가지게 된다.
     - 프록시를 적용하기 위해서는 반드식 인터페이스를 생성해야 함
     - 구체 클래스로는 빈을 주입받을 수 없고, 반드시 인터페이스로만 주입받아야함
   - 하지만 실제 개발을 하다보면 인터페이스 없이 구체 클래스에 의존하는 경우도 많은데 AOP를 적용하기 위해 모든 빈들을에게 인터페이스를 만들어주는 것은 상당히 번거롭다. 또한 구체 클래스에 의존해야 하는 경우에는 빈을 찾을 수 없어서 에러가 발생하므로 대처가 어렵다. 이러한 이유로 Spring은 JDK 동적 프록시 방식이 아닌 또 다른 프록시 구현 방식을 구혀하였다.
4. __<span style="color:#ff9933">[Spring의 CGLib Proxy 구현]</span>__
   - 위와 같은 문제를 해결하기 위해서는 Spring이 구현해주는 Proxy 객체가 인터페이스(DiscountSerivee)를 기반으로 하지 않고 클래스(RateDiscountService)를 구현한 객체여야 한다. 즉, **클래스 상속을 기반으로 프록시를 구**하도록 강제해야 하는 것이다.
   - ``` java
      public class RateDiscountServiceProxy extends RateDiscountService { ... }
   - 그러면 Spring은 RateDiscountServiceProxy를 구현할 때 위와같이 RateDiscountService를 상속받아 구현하는데, 이러한 클래스 기반의 프록시를 구현하기 위해서는 바이트 코드를 조작해야한다. 그래서 Spring은 CGLib이라는 바이트 조작 라이브러리를 통해 클래스 상속으로 프록시를 구현함으로써 JDK 동적 프록시에 의한 문제를 완전히 해결하고 있따. CGLib을 이용한 프록시 방식은 클래스 기반의 프록시이므로 인터페이스가 없어도 적용가능하며, 인터페이스가 구현된 빈의 경우에도 인터페이스 주입과 구체 클래스 주입이 모두 가능하다. 대신 CGLib 프록시는 상속을 이용하므로 **기본 생성자를 필요로 하며, 생성자가 2번 호출되고 final클래스나 final 메소드면 안된다는 제약**이 있다.
## __<span style="color:#9999ff">@EnableAspectJAutoProxy의 proxyTargetClass</span>__
1. __<span style="color:#ff9933">[@EnableAspectJAutoProxy의 proxyTargetClass]</span>__
   - 스프링 프레임워크는 프록시를 구현할 떄 기존의 방식처럼 **인터페이스를 구현하도록 할것인지(JDK동적 프록시) 또는 해당 클래스를 바이트 조작하여 직접 구현하도록 할 것인지(CGLib)에 대한 옵션을 제공하고 있는데, 이것이 바로 proxyTargetClass이다.
   - ``` java 
      @SpringBootApplication 
      @EnableAspectJAutoProxy(proxyTargetClass = true) 
      public class AtddMembershipApplication { 
         public static void main(String[] args) { 
            SpringApplication.run(AtddMembershipApplication.class, args); 
         } 
      }
   - 일반적으로는 인터페이스 주입에 의한 문제를 예방하고자 proxyTargetClass를 True로 주는 경우가 많은데, SpringBoot를 사용하고 있다면 더이상 이러한 옵션을 부여하지 않아도 된다. 왜냐면 SpringBoot에서는 CGLib라이브러리가 안정화되었다고 판단하여 proxyTargetClass 옵션의 기본값을 true로 사용하고 있다.
   - 여기서 하나 주의할 점은 @EnableAspectJAutoProxy 어노테이션의 기본값이 true로 바뀐것이 아니라는 점이다. @EnableAspectJAutoProxy는 SpringBoot가 아닌 Spring에서 만들어진 어노테이션이므로 기본값은 false가 맞다.
   - ``` java
      @Target(ElementType.TYPE) 
      @Retention(RetentionPolicy.RUNTIME) 
      @Documented
      @Import(AspectJAutoProxyRegistrar.class) 
      public @interface EnableAspectJAutoProxy { 
         boolean proxyTargetClass() default false; 
         boolean exposeProxy() default false; 
      }
   - 위에서 proxyTargetClass 옵션의 기본값을 true로 바꾼것은 SpringBoot에만 해당된다. SpringBoot는 어플리케이션을 실행할 때 AutoConfigure를 위한 정보들을 spring-boot-autoconfigure의 spring-configuration-metadata.json에서 관리하고 있다.
   - 그리고 AutoConfigure를 진행할 때 해당 값을 참조해서 설정을 진행한다. 그러므로 proxyTargetClass의 기본 값이 true라는 것은 Spring MVC가 아닌 Spring Boot에서만 해당하는 내용이다. 만약 SpringBoot에서 proxyTargetClass의 값을 false로 설정하려면 프로퍼티에서 spring.aop.proxy-target-class를 false로 주면 된다.
   - 만약 spring-boot-starter-aop의존성이 추가되어 있다면 AopAutoConfiguration을 통한 설정에 의해 @EnableAspectJAutoProxy를 추가하지 않아도 된다.
1. __<span style="color:#ff9933">[정리 및 요약]</span>__
   - 원래 Spring은 프록시 타깃 객체에 인터페이스가 있다면 그 인터페이스를 구현한 JDK 다이내믹 프록시 방식으로 객체를 생성하고 인터페이스가 없다면 CGLib를 이용하한 클래스 프록시를 만든다.
     - 인터페이스를 구현하고 있는지 확인함
     - 인터페이스를 구형하고 있으면 JDK 다이내믹 프록시 방식으로 객체를 생성
     - 인터페이스를 구현하지 않으면 GCLib 방식으로 객체를 생성
   - 하지만 JDK 동적 프록시 방식은 다음과 같은 2가지 한계점을 가지고 있다.
     - 프록시를 적요하기 위해서 반드시 인터페이스를 생성해야함
     - 구체 클래스로는 빈을 주입받을 수 없고, 반드시 인터페이스로만 주입받아야함
   - 그래서 스프링은 CGLib 방식의 프록시를 강제하는 옵션을 제공하고 있는데, 이것이 바록 proxyTargetClass이며, 이값을 true로 지정해주면 Spring은 인터페이스가 있더라도 무시하고 클래스 프록시를 만들게 된다.
   - SpringBoot에서는 CGLib라이브러리가 갖는 단점들을 모두 해결하였고 proxyTargetClass 옵션의 기본값을 true로 사용하고 있다.