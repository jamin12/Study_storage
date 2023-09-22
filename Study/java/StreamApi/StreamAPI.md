# Stream
## __<span style="color:#9999ff">Stream API에 대한 이해</span>__
1. __<span style="color:#ff9933">[Stream API에 대한 소개]</span>__
   - JAVA는 객체지향 언어이기 때문에 기본적으로 함수형 프로그래밍이 불가능하다. <br> 하지만 JDK8부터 Stream API와 람다식, 함수형 인터페이스 등을 지원하면서 java를 이용해 함수형으로 프로그래밍할 수 있는 API 들을 제공해주고 있다. 그 중에서 Stream API는 데이터를 추상화하고 처리하는데 자주 사용되는 함수들을 정의 해두었다. 
   - 예를 들어 주어진 배열이나 리스트의 데이터를 정렬된 상태로 출력하고자 한다, 
   - Stream API를 사용하지 않은 경우:
   - ``` java
      // Stream 사용 전 
      String[] nameArr = {"IronMan", "Captain", "Hulk", "Thor"} 
      List<String> nameList = Arrays.asList(nameArr); 
      // 원본의 데이터가 직접 정렬됨 
      Arrays.sort(nameArr); 
      Collections.sort(nameList);
      for (String str: nameArr) { 
          System.out.println(str); 
      } 
      for (String str : nameList) {
          System.out.println(str); 
      }
   - Stream API를 사용한 경우:
   - ``` java 
      // Stream 사용 후 
      String[] nameArr = {"IronMan", "Captain", "Hulk", "Thor"} 
      List<String> nameList = Arrays.asList(nameArr); 
      // 원본의 데이터가 아닌 별도의 Stream을 생성함 
      Stream<String> nameStream = nameList.stream(); 
      Stream<String> arrayStream = Arrays.stream(nameArr); 
      // 복사된 데이터를 정렬하여 출력함 
      nameStream.sorted().forEach(System.out::println); 
      arrayStream.sorted().forEach(System.out::println);
   - 이처럼 Stream API를 활용하면 코드의 라인수도 줄이고 가독서도 높일 수 있다.
2. __<span style="color:#ff9933">[Stream API의 특징]</span>__
   - 원본의 데이터를 변경하지 않는다.
     - Stream API는 원본의 데이터를 조회하여 원본의 데이터가 아닌 별도의 요소들로 Stream을 생성한다. 그렇기 때문에 원본의 데이터로부터 읽기만 할 뿐이며, 정렬이나 필터링 등의 작업은 별도의 Stream요소들에서 정리 된다.
     - ``` java 
         List<String> sortedList = nameStream.sorted()
							.collect(Collections.toList());
   - Stream은 일회용이다.
     - Stream API는 일회용이기 때문에 한번 사용이 끝나면 재사용이 불가능하다. Stream이 또 필요한 경우에는 Stream을 다시 생성해주어야 한다. 만약 닫힌 Stream을 다시 사용한다면 IlleagalStateException이 발생하게 된다.
     - ``` java
         userStream.sorted().forEach(System.out::print); 
         // 스트림이 이미 사용되어 닫혔으므로 에러 발생 
         int count = userStream.count(); 
         // IllegalStateException 발생 
         java.lang.IllegalStateException: stream has already been operated upon or closed 
            at java.util.stream.AbstractPipeline.evaluate(AbstractPipeline.java:229) 
            at java.util.stream.ReferencePipeline.noneMatch(ReferencePipeline.java:459)
   - 내부 반복으로 작업을 처리한다.
     - Stream을 이용하면 코드가 간결해지는 이유 중 하나는 '내부 반복' 때문이다. 기존에는 반복문을 사용하기 위해서 for이나 while등과 같은 문법을 사용해야 했지만 stream에서는 그러한 반복 문법을 메소드 내부에 숨기고 있기 때문에 간결한 코드 작성이 가능하다.
     - ``` java 
         // 반복문이 forEach라는 함수 내부에 숨겨져 있다. 
         nameStream.forEach(System.out::println);
## __<span style="color:#9999ff">Stream API의 연산 종류</span>__
1. 생성하기
   - Stream 객체를 생성하는 단계
   - Stream은 재사용이 불가능하므로 닫히면 다시 생성해주어야 한다.
   - Stream 연산을 하기 위해서는 먼저 Stream객체를 생성해주어야 한다. 배열, 컬렉션, 임의의 수, 파일 등 거의 모든 것을 가지고 스트림을 생성할 수 있다. 여기서 주의할 점은 연산이 끝나면 Stream이 닫히기 떄문에, Stream이 닫혔을 경우 다시 Stream을 생성해야 한다는 것이다.
2. 가공하기
   - 원본의 데이터를 별도로 데이터로 가공하기 위한 중간 연산
   - 연산 결과를 Stream으로 다시 반환하기 떄문에 연속해서 중간 연산을 이어갈 수 있다.
   - 가공하기 단계는 원본의 데이터를 별도의 데이터로 가공하기 위한 중간 연산의 단계이다. 어떤 객체의 Stream을 원하는 형태로 처리할 수 있으며, 중간 연산의 반환값은 Stream이기 때문에 필요한 만큼 중간 연산을 연결하여 사용할 수 있다.
3. 결과 만들기
   -  가공된 데이터로부터 원하는 결과를 만들기 위한 최종 연산
   -  Stream의 요소들을 소모하면서 연산이 수행되기 때문에 1번만 처리 가능하다.

4. 예시 코드
   - ``` java 
     List<String> myList = Arrays.asList("a1", "a2", "b1", "c2", "c1"); 
     myList .stream() // 생성하기 
     .filter(s -> s.startsWith("c")) // 가공하기 
     .map(String::toUpperCase) // 가공하기 
     .sorted() // 가공하기 
     .count(); // 결과만들기
## __<span style="color:#9999ff">람다란?</span>__
- Stream 연산들은 매개변수로 함수현 인터페이스를 받도록 되어있다. 그리고 람다식은 반환값으로 함수형 인터페이스를 반환하고 있다. 그렇기 때문에 우리는 Stream API를 정확히 이해하기 위해 람다식과 함수형 인터페이스에 대해 알고 있어야 한다.
1. __<span style="color:#ff9933">람다식 이란?</span>__
   - 람다식이란 **함수를 하나의 식으로 표현한 것이다.** 함수를 람다식으로 표현하면 메소드의 이름이 팰요없기 때문에 람다식은 익명 함수의 한 종류라고 볼 수 있다. 
   - 익명 함수란 이름이 없는 함수로 익명함수들은 모두 일급 객체이다, 일급 객체인 함수는 변수처럼 사용가능하며 매개변수로 전달이 가능하는 등의 특징을 가지고 있다. 
   - 기존 방식:
   - ``` java
      // 기존 방식
      반환타입 메소드명(매개변수,....){
         실행문
      }
      // 예시
      public String hello(){
         return "Hello world";
      }
   - 람다식
   - ``` java
      // 람다 방식 (매개변수, ... ) -> { 실행문 ... } 
      // 예시 
      () -> "Hello World!";
   - 이렇게 람다식이 등장하게 된 이유는 불필요한 코드를 줄이고, 가독성을 높이기 위함이다. 그렇기 떄문에 함수형 인터페이스의 인스턴스를 생성하여 함수를 변수처럼 선언하는 람다식에서는 메소드의 이름이 불필요하다고 여겨져서 이를 사용하지 않는다. 대신 컴파일러가 문맥을 살펴 타입을 타입을 추론한다. 또한 람다식으로 선언된 함수를 1급 객체이기 때문에Stream API매개변수로 전달이 가능해진다.
2. __<span style="color:#ff9933">람다식의 특징</span>__
   - 람다식의 특징
     - 람다식 내에서 사용되는 지역변수는 final이 붙지 않아도 상수로 간주된다.
     - 람다식으로 선언된 변수명은 다른 변수명과 중복될 수 없다.
   - 람다식의 장점
     - 코드를 간결하게 만들 수 있다.
     - 식에 개발자의 의도가 명확히 드러나 가독성이 높아진다.
     - 함수를 만드는 과정없이 한번에 처리할 수 있어 생산성이 높아진다.
     - 병렬프로그래밍이 용이하다.
   - 람다식의 단점
     - 람다를 사용하면서 만든 무명함수는 재사용이 불가능하다.
     - 디버깅이 어렵다
     - 람다를 남발하면 비슷한 함수가 중복 생성되어 코드가 지저분해질 수 있다.
     - 재귀로 만들경우에 부적합하다.
## __<span style="color:#9999ff">함수형 인터페이스 란?</span>__
1. __<span style="color:#ff9933">함수형 인터페이스란?</span>__
   - 이제 우리는 람다식으로 순수 함수를 선언할 수 있게 되었다. 하지만 java는 기본적으로 객체지향 언어이기 때문에 순수 함수화 일반함수를 다르게 취급하고 있으며 java에서는 이를 구분하기 위해 함수형 인터페이스가 등장하게 되었다.
   - 함수형 인터페이스란 함수를 1급 객체처럼 다룰 수 있게 해주는 어노테이션으로, 인터페이스에 선언하여 단 하나의 추상 메소드만 을 갖도록 제한하는 역할을 한다. 함수형 인터페이스를 사용하는 이유는 Java의 람다식이 함수형 인터페이스를 반환하기 떄문이다.
   - 예를 들어 우리가 두 값 중 큰 값을 구하는 익명 함수를 개발하였다고 하자. 그러면 우리는 지금까지 다음과 같이 개발을 하였을 것이다.
   - ``` java
      public class Lambda { public static void main(String[] args) { 
         // 기존의 익명함수 
         System.out.println(new MyLambdaFunction() { 
            public int max(int a, int b) { 
               return a > b ? a : b; 
               } 
            }.max(3, 5)); 
         } 
      }
   - 하지만 함수형 인터페이스의 등장으로 우리는 함수를 변수처럼 선언할 수 있게 되었고, 코드역시 간결하게 작성할 수 있게 되었다. 함수형 인터페이스를 구현하기 위해서는 인터페이스를 개발하여 그 내부에는 1개 뿐인 abstract함수를 선언하고 위에는 @Functionalinterface 어노테이션을 붙여주면 된다. 위의 코드를 람다식으로 변경하면 다음과 같다.
   - ``` java
      @FunctionalInterface 
      interface MyLambdaFunction { 
         int max(int a, int b); 
      } 
      public class Lambda { 
         public static void main(String[] args) {
             // 람다식을 이용한 익명함수
              MyLambdaFunction lambdaFunction = (int a, int b) -> a > b ? a : b; 
              System.out.println(lambdaFunction.max(3, 5)); 
         } 
      }
   - 이제 우리는 java8 이전에 사용했던 익명함수들을 람다식으로 변경해 코드를 줄일 수 있게 되었고 여기서 놓치지 말아야 하는것은 람다식으로 생성된 순수 함수는 함수형 인터페이스로만 선언 가능하다는 점이다. @Functionalinterface는 해당 인터페이스가 1개의 함수만을 갖도록 제한하기 떄문에 여러개의 함수를 선언하면 컴파일 에러가 발생할 것이라는 점이다.
2. __<span style="color:#ff9933">java에서 제공하는 함수형 인터페이스</span>__
   - Supplier \<T>
     - Supplier는 매개변수 없이 반환값만을 갖는 함수형 인터페이스이다.
     - Supplier는 T get()을 추상 메소드로 갖고 있다.
     - ``` java 
         // 정의 
         @FunctionalInterface
          public interface Supplier<T> { 
             T get(); 
         }
         // 사용 예시 
         Supplier<String> supplier = () -> "Hello World!"; 
         System.out.println(supplier.get());
   - Consumer \<T>
     - Consumer는 객체 T를 매개변수로 받아서 사용하며 반환값은 없는 함수형 인터페이스다
     - Consumer는 void accept(T t)를 추상메소드로 갖는다.
     - 또한 Consumer는 andThen이라는 함수를 제공하고 있는데, 이를 통해 하나의 함수가 끝난 후 다음 Consumer를 연쇄적으로 이용할 수 있다. 아래의 예제에서는 먼저 accept로 받아들인 Consumer를 먼저 처리하고 andThen으로 받은 두번째 Consumer를 처리하고 있다. 함수형에서 함수는 값의 대입 또는 변경 등이 없기 때문에 첫 번째 Consumer가 split으로 데이터를 변경하였다 하더라도 원본의 데이터는 유지된다.
     - ``` java 
         // 정의 
         @FunctionalInterface 
         public interface Consumer<T> { 
            void accept(T t); 
            default Consumer<T> andThen(Consumer<? super T> after) { 
               Objects.requireNonNull(after); 
               return (T t) -> { accept(t); after.accept(t); }; 
               } 
         } 
         // 예시
         Consumer<String> consumer = (str) -> System.out.println(str.split(" ")[0]); 
         consumer.andThen(System.out::println).accept("Hello World"); 
         // 출력 
         Hello 
         Hello World
   - Funtion<T,R>
     - Funtion은 객체 T를 매개변수로 받아서 처리한 후 R로 반환하는 함수형 인터페이스다
     - Funtion은 R apply(T t)를 추상메소드로 갖는다.
     - 또한 Funtion은 Cunsumer와 마찬가지로 andThen을 제공하고 있으며, 추가적으로 compose를 제공하고 있다. 앞에서 andThen은 첫 번째 함수가 실행된 이후에 다음 함수를 연쇄적으로 실행하도록 연결해준다고 하였다. 하지만. compose는 첫 번째 함수 실행 이전에 먼저 함수를 싱행하여 연쇄적으로 연결해준다는 점에서 차이가 있다. 또한 identity함수가 존재하는데 이는 자기 자신을 반환하는 static함수 이다.
      - ``` java
            // 정의 
            @FunctionalInterface 
            public interface Function<T, R> { 
               R apply(T t); 
               default <V> Function<V, R> compose(Function<? super V, ? extends T> before) { 
                  Objects.requireNonNull(before); 
                  return (V v) -> apply(before.apply(v)); 
               } 
               default <V> Function<T, V> andThen(Function<? super R, ? extends V> after) { 
                  Objects.requireNonNull(after); 
                  return (T t) -> after.apply(apply(t)); 
               } 
               static <T> Function<T, T> identity() { 
                  return t -> t; 
               } 
            } 
            // 예시, 메소드 참조로 간소화 가능(String::length;) 
            Function<String, Integer> function = str -> str.length(); 
            function.apply("Hello World");
   - Predicate \<T>
     - Predicate는 객체 T를 매개변수로 받아 처리한 후 Boolean을 반환한다.
     - Predicate는 Bollean test(T t)을 추상 메소드로 갖고 있다.
     - ``` java 
         // 정의 
         @FunctionalInterface 
         public interface Predicate<T> { 
            boolean test(T t); 
            default Predicate<T> and(Predicate<? super T> other) { 
               Objects.requireNonNull(other); 
               return (t) -> test(t) && other.test(t); 
            } 

            default Predicate<T> negate() { 
               return (t) -> !test(t); 
            } 

            default Predicate<T> or(Predicate<? super T> other) { 
               Objects.requireNonNull(other); 
               return (t) -> test(t) || other.test(t); 
            } 

            static <T> Predicate<T> isEqual(Object targetRef) { 
               return (null == targetRef) 
                      ? Objects::isNull 
                      : object -> targetRef.equals(object); 
            } 
         } 
         // 예시 
         Predicate<String> predicate = (str) -> str.equals("Hello World"); 
         predicate.test("Hello World");
## __<span style="color:#9999ff">메소드 참조</span>__
- 메소드 참조란 함수형 인터페이스를 람다식이 아닌 일반 메소드를 참조시켜 선언하는 방법이다. 일반 메소드를 참조하기 위해서는 다음의 3가지 조건을 만족해야 한다.
  - 함수형 인터페이스의 매개변수 타입 = 메소드의 매개변수 타입
  - 함수형 인터페이스의 매개변수 개수 = 메소드의 매개변수 개수
  - 함수형 인터페이스의 반환형 = 메소드의 반환형
- 참조가능한 메소드는 일반 메소드 ,Static메소드, 생성자가 있으며 클래스이름::메소드이름 으로 참조할 수 있다. 이렇게 참조를 하면 함수형 인터페이스로 반환이 된다.
1. <span style="color:#ff9933">일반 메소드 참조</span>
   - 예를 들어 위에서 보여준 Funtion에 메소드 참조를 적용한다고 하자. 우선 해당 메소드가 위의 3가지 조건 만족하는지 살펴보아야 한다.
     - 매개변수 없음
     - 매개변수 개수 = 0개
     - 반환형 = int
   - String의 length함수는 매개변수가 없으면 반환형이 int로 동일하기 때문에 String::length로 다음과 같이 메소 참조를 적용할 수 있다.
   - ``` java
         // 기존의 람다식 
         Function<String, Integer> function = (str) -> str.length(); 
         function.apply("Hello World");
         // 메소드 참조로 변경 
         Function<String, Integer> function = String::length; 
         function.apply("Hello World");
2. <span style="color:#ff9933">Static 메소드 참조</span>
   - Static 메소드 역시 참조가 가능하다 에를들어 Objects의 isNull은 반환값이 Boolean이며 매개변수 값은 1개이고 매개 변수가 Object이므로 predicate로 다음과 같은 메소드 참조가 가능하다.
   - ``` java
         Predicate<Boolean> predicate = Objects::isNull;
         // isNull 함수 
         public static boolean isNull(Object obj) { 
            return obj == null; 
         }
3. <span style="color:#ff9933">생성자 참조</span>
   - 생성자도 메소드 참조를 할 수 있다. 생성자는 new로 생성해주므로 클래스이름::new로 참조할 수 있다. Supplier는 매개변수가 없이 반환값만을 갖는 인터페이스이기 떄문에 매개변수 없이 String객체를 새롭게 생성하는 String의 생성자를 참조하여 Supplier로 선언할 수 있다.
   - ``` java
         Supplier<String> supplier = String::new;

# 참고
- https://mangkyu.tistory.com/113 
#java #stream