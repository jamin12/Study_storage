# java
## __<span style="color:#9999ff">JAVA의 특징</span>__
- java는 객체지향 프로그래밍 언어이다.
- JVM위에서 동작하기 때문에 운영체제에 독립적이지만 실행 속도가 상대적으로 느리다.
- GC를 통한 자동적인 메모리 관리가 가능하다.
- 다중상속이나 타입에 업격하며, 제약이 많다.
## __<span style="color:#9999ff">OOP(객체지향 프로그래밍)</span>__
- `여러개의 독립된 단위, '객체'들의 상호작용으로 프로그램 로직을 구성하는 프로그래밍 패러다임이다.`
1. ### __<span style="color:#ff9933">특징</span>__
   1. #### __<span style="color:#14a492">캡슐화</span>__
      - 한 객체가 특정한 하나의 목적을 위해 필요한 데이터나 메소드를 하나로 묶는 것을 의미한다.
        - 데이터는 외부에서 직접 접근을 하면 안되고 함수를 통해서만 접근을 해야한다.
   2. #### __<span style="color:#14a492">은닉화</span>__
      - 캡슐화의 목표. 내부 구조는 private하게 감춰두고 외부에서 조작할 수 있는 정보만 public으로 공개한다.
      - 바깥 간섭으로 인해 발생하는 오류 방지</br>(내부의 구현은 감추고 모듈 내에서 응집도를 높이며 외부로의 노출을 최소화하여 모듈간의 결합도를 떨어뜨려 유연함과 유지보수성을 높인다.)
      - 다른 사람의 클래스 객체를 다 뜯어볼 필요가 없어진다. 제공되는 기능 그대로 사용하면 된다.
   3. #### __<span style="color:#14a492">상속</span>__
      - 기존 메소드와 변수를 물려받되, 필요한 기능을 더 추가하거나 나(자식클래스)에게 맞게 재정의하는 방법
      - ![](https://computasha.github.io/static/7abbb433459f6103db01f9f9a8937ad5/37523/inheritance-interface.png)
      - 보통 상속에서는 다중 상속이 안되거나 제한이 있는데 인터페이스는 이에 대해 자유롭다.
      - 날기 인터페이스를 implements 하는 경우 반드시 날기 인터페이스에 있는 메소드를 정의해야 한다.
      - 인터페이스의 경우 implements된 모든 클래스 객체를 하나로 묶어서 한번에 명령할 수 있다.

   4. #### __<span style="color:#14a492">추상화 Abstraction</span>__
      - 목적과 관련이 없는 부분을 제외해서 필요한 부분을 포착하는 기법이다.
      - 객체의 공통된 속성들 중 필요한 부분을 포착해서 클래스로 정의하는 설계 기법이다.
      - 구체적인 사물들의 공통적인 특징을 파악해서 이를 하나의 개념(집합)으로 다루는것
        
   5. #### __<span style="color:#14a492">다형화</span>__
      - 같은 형태이지만 다른 기능을 하는 것이다.
      - 서로 다른 클래스의 객체가 같은 메시지를 받았을 때 각자의 방식으로 동작하는 능력
      - 오버라이딩(overriding)은 상위 클래스가 가지고 있는 메서드를 하위 클래스가 재정의해서 사용
      - 오버로딩(overloading)은 같은 이름의 메서드 여러개를 가지면서 매개변수의 유형과 개수가 다르도록 하는 기술이다.
## __<span style="color:#9999ff">자바 컴파일 과정<span>__
1. 개발자가 자바 소스코드(.java)를 작성합니다.</br>

2. 자바 컴파일러(Java Compiler)가 자바 소스파일을 컴파일 합니다. 이때 나오는 파일은 자바 바이트코드(.class)파일로 아직 컴퓨터가 읽을 수 없는 자바 가상 머신이 이해할 수 있는 코드입니다. 바이트 코드의 각 명령어는 1바이트 크기의 Opcode와 추가 피 연산자로 이루어져 있습니다.

3. 컴파일된 바이트 코드를 JVM의 클래스로더(class Loader)에게 전달합니다.

4. 클래스 로더는 동적로딩(Dynamic Loading)을 통해 필요한 클래스들을 로딩 및 링크하여 런타임 데이터 영역(Runtime Data area)즉 JVM 메모리에 올립니다.

5. 실행엔진(Execution Engine)은 JVM 메모리에 올라온 바이트 코드들을 명령어 단위로 하나씩 가져와서 실행합니다. 이때, 실행 엔진은 두가지 방식으로 변경합니다.
   1. 인터프리터 : 바이트 코드 명령어를 하나씩 읽어서 해석하고 실행합니다. 하나하나의 실행은 빠르나 전체적인 실행 속도가 느리다는 단점을 가집니다.
   2. JIT컴파일러(Just-In-Time Compiler) : 인터프리터의 단점을 보완하기 위해 도입된 방식으로 바이트 코드 전체를 컴파일하여 바이너리 코드로 변경하고 이후에 해당 메서드를 더 이상 이터프리팅 하지 않고, 바이너리 코드로 직접 실행하는 방식입니다. 내부적으로(application)을 모니터링 하여 가장 컴파일이 필요한 부분(가장 자주 실행되는 부분)즉 '핫스팟'을 찾아낸 다음, 이 핫스팟을 네이티브 코드로 컴파일한다. 오라클 핫스팟 JVM은 한번 컴파일된 바이트코드라도 해당 메서드가 더 이상 자주 불리지 않는다면, 즉 핫스팟이 아니게 되면 캐시에서 네이티브 코드를 덜어내고 다시 인터프리터 모드로 동작한다. 핫스팟 VM은 서버 VM과 클라이언트 VM으로 나뉘어 있고, 각가 다른 JIT 컴파일러를 사용한다.</br>JIT는 인터프리터 방식으로 실행하다가 적절한 시점에 바이트 코드전체를 컴파일하여 네이티브 코드로 변경하고,이후에는 해당 메서드를 더이상 이터프리팅하지 않고 네이티브 코드로 직접 실행하는 방식이다. 네이티브 코드는 캐시에 보관하기 때문에 한 번 컴파일된 코드는 계속 빠르게 수행되게 된다.
   - ![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FcbmDfm%2FbtqYQw7KusY%2FDiAB9gtgkoDHnYRKkFw0P1%2Fimg.png)

## __<span style="color:#9999ff">자바 메모리 영역</span>__
1. ### __<span style="color:#ff9933">Method(static)영역</span>__
   - 전역 변수나 static으로 선언되는 것들을 메모리에 상주 시킨다.
   - 클래스 정보, static변수, 변수 정보, 메소드 정보 등을 저장
   - JVM이 동작해서 클래스가 로딩될 때 생성
   - JVM이 종료 될때까지 유지
2. ### __<span style="color:#ff9933">힙 영역</span>__
   - 인스턴스를 생성할 때 생성되는 메모리 형식(new 연산을 통해 생성되는 객체들)
   - 힘의 참조 주소는 "스택"이 갖고 있는 해당 객체를 통해서만 힙 영역에 있는 인스턴스를 핸들링 할 수 있다.
   - GC가 정리하기 전까지 남아있다.
3. ### __<span style="color:#ff9933">스택 영역</span>__
   - int, long등 기본 자료형을 생성할 때 저장하는 공간으로 임시적으로 사용되는 변수나 정보들이 저장되는 공간
   - 쓰레드 별로 한개만 생성된다.
   - 지역변수 메서드 등이 할당되는 LIFO방식의 메모리
## __<span style="color:#9999ff">오버라이딩(Overriding) 오버로딩(Overloading)</span>__
1. ### __<span style="color:#ff9933">오버로딩(Overloading)</span>__
   - 메서드의 이름은 같고 매개변수의 갯수나 타밍이 다른 함수를 정의하는 것을 의미한다.
   - 리턴값만을 다르게 갖는 오버로딩은 작성 할 수 없다.
2. ### __<span style="color:#ff9933">오버라이딩(Overriding)</span>__
- 상위 클래스의 메서드를 하위 클래스가 재정의하는 것
- 메서드의 이름은 물론 파라미터의 갯수나 타입도 동일해야하며, 주로 사우이 클래스의 동작을 상속받은 하위 클래스에서 변경하기 위해 사용된다.
## __<span style="color:#9999ff">자바 인터페이스<span>__
- 추상 메서드만을 가질 수 있고 구현메서드는 가질 수 없는 추상클래스이다.
- 부모 클래스가 인터페이스면 자식 클래스가 무조건 구현해야 한다.
- 부모 인터페이스의 모든 메서드들을 자식 클래스에서 반드시 재정의되어야 하기 때문에 다형성이 보장된다.
- 인터페이스 멤버 변수는 public static final로만 지정가능하며 생략 가능하다
- 멤버 함수는 public abstract final로만 선언 가능하고 이또한 생략 가능하다.
1. ### __<span style="color:#ff9933">특징</span>__
   1. 다중 상속 가능
   2. 추상 메서드만 보유
   3. 생성자 생성 불가
   4. 메소드 오버라이딩 필수
## __<span style="color:#9999ff">자바 GC(Garbage Collection)<span>__
- 가비지 컬렉션은 JVM의 메모리 관리 기법 중 하나로 시스템에서 동적으로 할당됐던 메모리 영역 중에서 필요없어진 메모리 영역을 회수하여 메모리를 관리해주는 기법
1. ### __<span style="color:#ff9933">가비지 컬렉션 과정에 대해 설명해주세요</span>__
   1. GC의 작업 수행을 위해 JVM이 어플리케이션의 실행을 잠시 멈추고 GC를 실행하는 쓰레드를 제외한 쓰레드들의 작업을 중단 후 Stop The World과정 사용하지 않는 메모리를 제거 하고 작업이 재개된다.
## __<span style="color:#9999ff">자바 박싱 언박싱<span>__
1. ### __<span style="color:#ff9933">Wrapper class</span>__
   - 자바는 int, long 같은 변수의 기본 자료형을 제공한다. 하지만 이런 기본 자료형은 컬렉션이나 제네릭에서 제한적으로 사용하능하다.
   - 자바는 각 기본 자료형에 대응되는 래퍼 클래스를 제공한다.
   - |기본 자료형|래퍼 클래스|
       |---|---|
       |boolean|Boolean|
       |short|Short|
       |int|Integer|
       |long|Long|
       |float|Float|
       |double|Double|
       |char|Character|
       |byte|Byte|
1. ### __<span style="color:#ff9933">박싱 언박싱</span>__
- 박싱은 기본 자료형의 데이터를 대응되는 래퍼 클래스로 만드는 동작을 의미한다.
- ``` java
        public class Boxing {
        public static void main(String[] args) {
            
            int i = 10;
            Integer wI = new Integer(a); // int Boxing 동작
            
            double d = 3.14;
            Double wD = new Double(d); // double Boxing 동작
            
            boolean b = true;
            Boolean wB = new Boolean(b); // boolean Boxing 동작
        }
    }
- 언박싱은 동작은 래퍼 클래스에서 기본 자료형으로의 변환을 의미한다.
- ``` java
        public class Boxing {
        public static void main(String[] args) {
            
            Integer wI = new Integer(10);
            int i = wI.intValue();     // Integer Unboxing 동작
            
            Double wD = new Double(3.14);
            double d = wD.doubleValue(); // Double Unboxing 동작
            
            Boolean wB = new Boolean(true);
            boolean b = wB.booleanValue(); // Boolean Unboxing 동작
        }
    }
## __<span style="color:#9999ff">자바 제네릭<span>__
- 제네릭은 클래스 내부에서 지정하는 것이 아닌 외부에서 사용자에 의해 지정되는 것을 의미한다. 한마디로 특정 타입을 미리 지정해주는 것이 아닌 필요에의해 지정할 수 있도록 하는 일만 타입이라는 것이다.
1. __제네릭의 장점__
   - 제네릭을 사용하면 잘못된 타입이 들어올 수 있는 것을 컴파일 단계에서 방지할 수 있다
   - 클래스 외부에서 타입을 지정해주기 때문에 따로 타입을 체크하고 변환해줄 필요가 없다. 즉, 관리하기가 편하다.
   - 비슷한 기능을 지원하는 경우 코드의 재사용성이 높아진다.
## __<span style="color:#9999ff">자바 Reflection<span>__
- 힙 영역에 로드된 class 타입의 객체를 통해 원하는 클래스의 인스턴스를 생성할 수 있도록 지원하고, 인스턴스의 필드와 메소드를 접근 제어자와 상관없이 사용할 수 있도록 지원하는 API이다.
- 접근 제어자에 상관없이 조작이 가능하니 캡슐화 규칙이 깨지게 된다.
- 예를 들어 spring의 bean factory를 보면 @controller, @service 등 어노테이션만 붙이면 알아서 해당 어노테이션이 붙은 클래스를 생성하고 관리해주는데 런타임에서 해당 어노테이션이 붙은 클래스를 탐색하고 발견한다면 리플렉션을 통해 해당 클래스의 인스턴스를 생성하고 필요한 필드를 주입하여 Bean Factory에 저장하는 식으로 사용 된다.
## __<span style="color:#9999ff">자바 직렬화 역직렬화<span>__
1. ### __<span style="color:#ff9933">직렬화</span>__
   - 자바 시스템 내부에서 사용되는 Object 또는 Data를 외부 자바 시스템에서도 사용할 수 있도록 Byte형태로 데이터를 변환하는 기술
   - JVM의 메모리에 상주 되어 있는 객체 데이터를 바이트 형태로 변환하는 기술
2. ### __<span style="color:#ff9933">역직렬화</span>__
   - byte로 변환된 data를 원래 Object나 Data로 변환하는 기술
   - 직렬화된 바이트 형태의 테이더를 객체로 변환해서 JVM으로 상주시키는 형태
3. ### __<span style="color:#ff9933">serialVersionUID를 선언해야 하는 이유</span>__
   - JVM은 직렬화나 역직렬화를 하는 시접의 클래스에 대해 version번호를 부여한다. 그런데 만약 이 시점에서 클래스의 정의가 바꾸게되면, version번호도 새롭게 할당 해주는데 직렬화와 역직렬화의 version번호가 서로 다르면 안되기 때문에 serialVersionUID를 선언해서 문제를 해결할 수 있다.
   - 즉 클래스 버전이 맞는지 확인하기 위한 용도로 사용된다.
## __<span style="color:#9999ff">메모리 상수 풀<span>__
- 힙 영역의 고정영역에 생성되어 java 프로세스의 종료까지 계속 유지되는 메모리 영역
- 기본적으로 JVM에서 관리하며 프로그래머가 작성한 상수에 대해서 최우전적으로 찾아보고 없으면 상수풀에 추가한 이후 그 주소값을 리턴한다.
## __<span style="color:#9999ff">JDBC란<span>__
- java에서 DB에 접근하여 데이터 조회 삽입 수정 삭제를 가능케하는것을 말한다.
- java가 `database를 사용할 수 있도록 연결해주는 응용프로그램 인터페이스인 JAVA API이다`
## __<span style="color:#9999ff">Servlet JSP 차이<span>__
- 기능 차이는 없고 역할의 차이만 있다.
1. ### __<span style="color:#ff9933">Servlet 이란</span>__
   - 웹 기만의 요청에 대한 동적인 처리가 가능한 server Side에서 돌아가는 Java program
   - java 코드안에 HTML코드 (하나의 클래스)
   - 웹개발을 위해 만든 표준
2. ### __<span style="color:#ff9933">JSP 란</span>__
   - JAVA 언어를 기반으로 하는 server Side 스크립트 언어
   - HTML 코드 안에 JAVA코드
   - Servlet를 보완하고 기술을 확장한 스크립트 방식 표준Servlet의 모든 기능 + 추가적인 기능   
## __<span style="color:#9999ff">쓰레드 교착상태(데드락)<span>__
- 멀티 쓰레드 프로그래밍에서 동기화를 통해 락을 획득하여 동일한 자원을 여러곳에서 함부로 사용하지 못하도록 하였다.
- 두 개의 쓰레드에서 서로가 가지고 있는 락이 해제되기를 기다리는 상태가 생길 수 있으며 이러한 상태를 교착상태라고 한다.
1. ### __<span style="color:#ff9933">발생 조건</span>__
   - 상호배제: 한 자원에 대해 여러 쓰레드 동시 접근 불가.
   - 점유와 대기: 자원을 가지고 있는 상태에서 다른 쓰레드가 사용하고 있는 자원 반납을 기다리는 것
   - 비선점: 다른 쓰레드의 자원을 실행 중간에 강제로 가져올 수 없음
   - 환형대기: 각 쓰레드가 순환적으로 다음 쓰레드가 요구하는 자원을 가지고 있는것
   - 위 네가지 조건을 모두 충족할 경우 데드락이 발생하게 된다.
## __<span style="color:#9999ff">Inner Class(내부 클래스)의 장점<span>__
1. 내부 클래스에서 외부 클래스의 맴버에 손쉽게 접근할 수 있다.
2. 서로 관련있는 클래스를 논리적으로 묶어서 표현함으로써 코드의 캡슐화를 증가시킨다.
3. 외부에서 내부 클래스에 접글할 수 없으므로 코드의 복잡성을 줄일 수 있다.
## __<span style="color:#9999ff"> Error와 Exception의 차이<span>__
- Error는 실행 중ㅇ ㅔ일어날 수 있는 치명적인 오류를 말한다, 컴파일 시점에 체크할 수 없고, 오류가 발생하면 프로그램은 비정상 종료되며 예측 불가능한 UncheckedException에 ㅅ속한다.
- Exception은 Error 보다 비교적 경미한 오류이며 try-catch를 이용해 프로그램의 비정상 종료를 막을 수 있다.
## __<span style="color:#9999ff">스프링 필터랑 인터셉터의 차이<span>__
- 필터와 인터셉터는 실행되는 시점에서 차이가 있따.
- 필터는 웹 애플리케이션에 등록하고 인터셉터는 스프링 context에 등록한다.
## __<span style="color:#9999ff"> Synchronized에 대해<span>__
- 여러 개의 쓰레드가 한 개의 자원을 사용하고자 할 때, 현재 데이터를 사용하고 있는 쓰레드를 제외하고 나머지 쓰레드들은 데이터에 접근 할 수 없게 막는 개념
- 데이터의 thread-safe를 하기 위해 자바에서 Synchronized키워드를 제공해 멀티 쓰레드 환경에서 쓰레드간 동기화를 시켜 데이터의 thread-safe를 보장한다.
## __<span style="color:#9999ff">Vector와 List의 차이<span>__
- 벡터는 데이터 삽입시 원소를 밀어내지만 리스트는 노드를 연결만 하기 때문에, 삽입 삭제 부분에서 리스트가 시간복잡도의 우위를 가집니다.
- 벡터는 랜덤부분접근이 가능하지만 리스트는 더블링크드리스트(노드가 양쪽으로 연결)로 되어있기 때문에 랜덤 접근이 되지 않습니다. 검색적인 측면에서는 벡터가 우위에 있습니다.
- 벡터는 리스트와 달리 항상 동기화되는 장점이자 단점을 가지고 있습니다. 멀티 쓰레드 환경에서 안전하게 객체를 추가하고 삭제할 수 있지만, 단일쓰레드 환경 일때도 동기화를 하기 때문에 List보다 성능이 떨어집니다.
# 참고
## __<span style="color:lightblue">질문</span>__
- https://gem1n1.tistory.com/27
- https://dev-coco.tistory.com/153
- https://byul91oh.tistory.com/228
## __<span style="color:lightblue">OOP특징</span>__
- https://computasha.github.io/CS-OOP/
- https://theheydaze.tistory.com/603
## __<span style="color:lightblue">자바 컴파일 과정<span>__
- https://developer-youn.tistory.com/38
- https://gyoogle.dev/blog/computer-language/Java/%EC%BB%B4%ED%8C%8C%EC%9D%BC%20%EA%B3%BC%EC%A0%95.html
## __<span style="color:lightblue">자바 메모리 영역</span>__
- https://tape22.tistory.com/28
## __<span style="color:lightblue">오버라이딩(Overriding) 오버로딩(Overloading)</span>__
- https://88240.tistory.com/450
## __<span style="color:lightblue">자바 인터페이스<span>__
- https://life-with-coding.tistory.com/488
- http://www.tcpschool.com/java/java_polymorphism_interface
## __<span style="color:lightblue">자바 박싱 언박싱<span>__
- https://soft.plusblog.co.kr/56
## __<span style="color:lightblue">자바 제네릭<span>__
- https://st-lab.tistory.com/153
## __<span style="color:lightblue">자바 Reflection<span>__
- https://steady-coding.tistory.com/609
## __<span style="color:lightblue">메모리 상수 풀<span>__
- https://djlee118.tistory.com/459
## __<span style="color:lightblue">JDBC란<span>__
- https://inpa.tistory.com/entry/DB-%F0%9F%93%9A-JDBC-ODBC-%EC%B0%A8%EC%9D%B4-%EC%A0%95%EB%A6%AC
## __<span style="color:lightblue">Servlet JSP 차이<span>__
- https://bangu4.tistory.com/35
## __<span style="color:lightblue">쓰레드 교착상태(데드락)<span>__
- https://math-coding.tistory.com/175
## __<span style="color:lightblue"> Error와 Exception의 차이<span>__
- https://dev-coco.tistory.com/18