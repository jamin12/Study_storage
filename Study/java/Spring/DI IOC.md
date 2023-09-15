# DI(Dependency Injection)
## __<span style="color:#9999ff">의존성 주입(Dependency Injection)의 개념과 필요성]</span>__
- __<span style="color:#ff9933">DI 란?</span>__
  - 
  - Spring 프레임워크는 3가지 핵심 프로그래밍 모델을 지원하고 있는데 그중 하나가 DI이다
  - DI란 외부에서 두 객체 간의 관계를 결정해주는 디자인 패턴으로 인터페이스를 사이에 둬서 클래스 레벨에서는 의존관계가 고정되지 않도록 하고 런타임 시에 관계를 다이나믹하게 주입하여 유연성을 확보하고 결합도를 낮출 수 있게 해준다.
- __<span style="color:#ff9933">의존성 주입(Dependency Injection)이 필요한 이유</span>__
  - 
  - 예를 들어 연필이라는 상품과 1개의 연필을 판매하는 Store클래스가 있다고 하자
  - ``` java
      public class Pencil {
      }

      public class Store {
          private Pencil pencil;
          public Store() {
              this.pencil = new Pencil();
          }
      }
  - 위 코드는 2가지 문제점이 있다.
    - __<span style="color:#14a492">두 클래스가 강하게 결합되어 있음</span>__
      - 위와 같은 Store클래스는 현재 Pencil클래스와 **강하게 결합되어 있다는 문제점을**가지고 있다. 두 클래스가 강하게 결합 되어 있어서 만약 Store에서 Pencil이 아닌 Food와 같은 다른 상품을 판매하고자 한다면  Store클래스의 생성자에 변경이 필요하다. 즉, 유연성이 떨어진다. 각각의 다른 상품들을 판매하기 위해 생성자만 다르고 나머지 중복되는 Store 클래스들이 파생되는 것은 좋지 못하다.
    - __<span style="color:#14a492">객체들 간의 관계가 아니라 클래스 간의 관계가 맺어지고 있음<span>__
      - 또한 위의 Store와 Pencil는 객체들 간의 관계가 아니라 클래스들 간의 관계가 맺어져 있다는 문제가 있다. 올바른 객체지향적 설계라면 객체들 간의 관계가 맺어져야 하지만 현채는 Store 클래스와 Pencil클래스가 관계를 맺고 있다. 객체들 간에 관계가 맺어졌다면 다른 객체의 구체 클래스를 전혀 알지 못하더라도 인터페이스 타입으로 사용할 수 있다.
- __<span style="color:#ff9933">의존성 주입(Dependency Injection)을 통한 문제 해결</span>__
  - 
  - 위와 같은 문제를 해결하기 위해서는 우선 다형성이 필요하다. Pencil,Food등 여러가지 제품을 하나로 표현하기 위해서는 Product라는 interface가 필요하다. 그리고 Pencil에서 product인터페이스를 우선 구현하자.
  - ``` java
        public interface Product {

        }

        public class Pencil implements Product {

        }
  - Store와 Pencil이 강하게 결합되어 있는 부분을 제거해주어야 한다. 이를 제거하기 위해 **외부에서 상품을 주입**을 받아야 한다.
  - ``` java
        public class Store {
            private Product product;
            public Store(Product product) {
                this.product = product;
            }
        }
  - 여기서 Spring이 DI컨테이너를 필요로 하는 이유를 알 수 있는데 우선 Stroe에서 Product 객체를 주입하기 위해서는 애플리케이션 실행 시점에 필요한 객체(빈)을 생성 해야하며, 의존성이 있는 두 객체를 연겨라기 위해 한 객체를 다른 객체로 주입 시켜야 하기 때문이다.
  - 예를 들어 다음과 같이 Pencil이라는 객체를 만들고 그 객체를 Store로 주입시켜주는 역할을 위해 DI컨테이너가 필요하게 된 것이다.
  - ``` java
        public class BeanFactory {

            public void store() {
                // Bean의 생성
                Product pencil = new Pencil();
            
                // 의존성 주입
                Store store = new Store(pencil);
            }
            
        }
  - 그리고 이러한 개념은 제어의 역전 (IOC)라고 불리기도 한다. 어떠한 객체를 사용할지에 대한 책임이 BeanFactory와 같은 클래스에게 넘어갔고 자신은 수동적으로 주입받는 객체를 사용하기 때문이다.


## __<span style="color:#9999ff">DI 정리</span>__
- 한 객체가 어떤 객체(구체 클래스)에 의존할 것인지는 별도의 관심사다 Spring에서는 DI컨테이너를 통해 서로 강하게 결합되어 있는 두 클래스를 분리하고 두 객체 간의 관계를 결정해 줌으로써 결합도를 낮추고 유연성을 확보하고자 하였다. 의존성 주입으로 애플리케이션 실행 시점에 객체를 생성하고 관계를 결정해 줌으로써 다른 구체 클래스에 의존하는 코드를 제거하며 서로 다른 두 객체의 결합을 약하게 만들어 주었다. 또한 이러한 방법은 상속보다 훨씬 유연하다.

- 두 객체간의 관계라는 관심사의 분리
- 두 객체 간의 결합도를 낮춤
- 객체의 유연성을 높임
- 테스트 작성을 용이하게 함

## __<span style="color:#9999ff">의존성을 주입하는 방법</span>__
- __<span style="color:#ff9933">setter injection</span>__
  - 
  - setter를 이용하여 의존 객체를 주입해 주면 된다.
  - ``` java
        @Service
        @Transactional(readOnly = true)
        public class MemberService {

            private Member member;

            @Transactional
            public Long join() {
                save(member);
            }
            
            public void setMember(Member member) {
                this.member = member;
            }
        }
- __<span style="color:#ff9933">Constructor injection</span>__
  - 
  - 생성자를 이용한 injection방법
  - ``` java
        @Service
        @Transactional(readOnly = true)
        public class MemberService {
            private Member member;
            public MemberService(Member member) {
                this.member = member
            }
            
            @Transactional
            public void join() {
                save(member);
            }
        }
- __<span style="color:#ff9933">스프링의 @Autowired를 이용한 자동 의존 주입</span>__
  - 
  - 우선 스프링에서는 ApplicationContext가 관리하는 Bean으로 등록된 객체만 의존 주입이 가능하다.</br> applicationcontext가 관리하는 bean은 @Autowired가 선언된 type을 비교하고 bean을 자동으로 주입한다.
  - ``` java
        @Service
        @Transactional(readOnly = true)
        public class MemberService {

            private Member member;

            @Autowired
            public MemberService(Member member) {
                this.member = member
            }
            
            @Transactional
            public void join() {
                save(member);
            }

        }
# IOC(Inversion of Control)
- 간단히 말하면 객체에 대한 제어권이 개발자에서 컨테이너로 넘어간 것
- 객체의 생성부터 생명주기 관리까지 전부 컨테이너가 맡아서 하기 때문에 제어를 컨테이너가 갖고 있다.
- 스프링에서 제공하는 컨테이너를 loc컨테이너라고 하기도 한다.
- 컨테이너가 직접 빈을 생성/관리 하기 떄문에 개발자는 코드에 new등으로 선언하지 않아도 되면 이는 각 클래스들의 의존도를 줄여준다.
## __<span style="color:#9999ff">IOC 용어 정리</span>__
- bean : 스프링에서 제어권을 가지고 직접 만들어 관계를 부여하는 오브젝트
  - 스프링을 사용하는 애플리케이션에서 만들어지는 모든 오브젝튼는 빈이 아니다. 스프링의 빈은 스프링 컨테이너가 생성하거 관계설정, 사용을 제어해주는 오브젝트를 말한다.
- bean factory : 스프링 Ioc를 담당하는 핵심 컨테이너
  - Bean을 등록/생성/조회/반환/관리 한다. 보통 bean factory를 바로 사용하지 않고 이를 확장한 applicationcontext를 이용한다. BeanFactory는 bean factory가 구현하는 interface이다.
- application context : bean factory를 확장한 Ioc컨테이너
  - Bean의 등록/생성/조회/반환/관리 기능은 bean factory와 같지만 추가적으로 spring의 각종부가 서비스를 제공한다. ApplicationContext는 application context가 구현해야 하는 interface이며 BeanFactory 상속한다.
- configuration metadata : application context 혹은 bean factory가 Ioc를 적용하기 위해 사용하는 메타정보
  - 스프링의 설정정보는 컨테이너에 어떤 기능을 세팅하거나 조정하는 경우에도 사용하지만 주로 bean을 생성/구성하는 용도로 사용한다.
- container(Ioc container): Ioc방식으로 bean을 관리한다는 의미에서 bean factory나 application context를 가르킨다.
  - application context는 그 자체로 ApplicationContext 인터페이스를 구현한 오브젝트를 말하기도하는데, 하나의 애플리케이션에 보통 여러개의 ApplicationContext객체가 만들어 진다. 이를 통칭해서 spring container라고 부를 수 있다.
# 참고
- https://mangkyu.tistory.com/150
- https://steady-hello.tistory.com/120
- https://bcp0109.tistory.com/226
#java #spring