# Stream API의 활요 및 사용법 기초
## __<span style="color:#9999ff">Stream 생성하기</span>__
- 앞서 설명한대로 Stream API를 사용하기 위해서는 먼저 Stream을 생성해주어야 한다. 사용하려는 객체들마다 Collection을 생성하는 방법이 다르다.
1. __<span style="color:#ff9933">[Collection의 Stream생성]</span>__
   - Collection 인터페이스에는 Stream()이 정의되어 있기 때문에 Collection 인터페이스를 구현한 객체들(List,set등)은 모두 이 메소드를 이용해 Stream을 생성할 수 있다. Stream()을 사용하면 해당 Collection의 객체를 소스로 하는 Stream을 반환한다.
   - ``` java 
        // List로부터 스트림을 생성 
        List<String> list = Arrays.asList("a", "b", "c"); 
        Stream<String> listStream = list.stream();
2. __<span style="color:#ff9933">[배열의 Stream생성]</span>__
   - 배열의 원소들을 소스로하는 Stream을 생성하기 위해서는 Steram의 of메소드 또는 Arrays의 Stream 메소드를 사용하면 된다.
   - ``` java 
        // 배열로부터 스트림을 생성 
        Stream<String> stream = Stream.of("a", "b", "c"); 
        //가변인자 Stream<String> 
        stream = Stream.of(new String[] {"a", "b", "c"}); 
        Stream<String> stream = Arrays.stream(new String[] {"a", "b", "c"}); 
        Stream<String> stream = Arrays.stream(new String[] {"a", "b", "c"}, 0, 3); //end범위 포함 x
3. __<span style="color:#ff9933">[원시 Stream 생성]</span>__
   - 위와 같이 객체를 위한 Stream 외에도 int와 long 그리고 double과 같은 원시 자료형들을 사용하기 위한 특수한 종류의 Stream(IntStream,LongStream,DoubleStream)들도 사용할 수 있으며 intstream같은 경우 range()함수를 사용하여 기존의 for문을 대체할 수 있다.
   - ``` java 
        // 4이상 10 이하의 숫자를 갖는 
        IntStream IntStream stream = IntStream.range(4, 10);
## __<span style="color:#9999ff">가공하기</span>__
- 생성한 Stream 객체에서 요소들을 가공하기 위해서는 중간연산이 필요하다. 가공하기 단계의 파라미터로는 앞서 설명하였던 함수형 인터페이스들이 사용되며, 여러 개의 중간연산이 연결되도록 반환값으로 Stream을 반환한다.
1. __<span style="color:#ff9933">[필터링 - Filter]</span>__
   - filter는 Stream에서 조건에 맞는 데이터만을 정제하여 더 작은 컬렉션을 만들어내는 연산이다. java에서는 filter함수의 인자로 함수형 인터페이스 predicate를 받고 있기 때문에 boolean을 반환하는 람다식을 작성하여 filter함수를 구현할 수 있다. 예를 들어 어떤 String의 stream에서 a가 들어간 문자열만을 포함하도록 필터링 하는 예제는 다음과 같이 작성할 수 있다.
    - ``` java
        Stream<String> stream = list.stream() 
                                .filter(name -> name.contains("a"));
2. __<span style="color:#ff9933">[데이터 변환 - Map]</span>__
   - Map은 기존의 Stream 요소들을 변환하여 새로운 Stream을 형성하는 연산이다. 저장된 값을 특정한 형태로 변환하는데 주로 사용되며, java에서는 map함수의 인자로 함수형 인터페이스 funtion을 받고있다. 예를 들어 string을 요소들로 갖는 Stream을 모두 대문자 String의 요소들로 변환하고자 할 때 map을 이용할 수 있다.
   - ``` java 
        Stream<String> stream = names.stream() .map(s -> s.toUpperCase());
   - 위의 map 함수의 람다식은 메소드 참조를 이용해 변경이 가능하다.
   - ``` java 
        Stream<File> fileStream = 
        Stream.of(new File("Test1.java"), new File("Test2.java"), new File("Test3.java")); 
        //Stream<File> --> Stream<String> 변환 
        Stream<String> fileNameStream = fileStream.map(File::getName);
3. __<span style="color:#ff9933">[정렬 - Sorted]</span>__
   - Stream의 요소들을 정렬하기 위해서는 storted를 사용해야 하며, 파라미터로 Comparator를 넘길 수도 있다. Comparator인자 없이 호출할 경우에는 오름차순으로 정렬이 되며, 내림차순으로 정렬하기 위해서는 Comparator 의 reverseOrder를 이용하면된다.
   - ``` java
        List<String> list = Arrays.asList("Java", "Scala", "Groovy", "Python", "Go", "Swift"); 
        Stream<String> stream = list.stream() .sorted() 
        // [Go, Groovy, Java, Python, Scala, Swift] 
        Stream<String> stream = list.stream() .sorted(Comparator.reverseOrder()) 
        // [Swift, Scala, Python, Java, Groovy, Go]
4. __<span style="color:#ff9933">[중복 제거 - Distinct]</span>__
   - Stream의 요소들에 중복된 데이터가 존재하는 경우, 중복을 제거하기 위해 Distinct을 사용할 수 있다. Distinct은 중복된 데이터를 검사하기 위해 Object의 equals() 메소드를 사용한다. 예를 들어 중복된 Stream의 요소들을 제거하기 위해서는 아래와 같이 distinct()를 사용할 수 있다.
   - ``` java
          List<String> list = Arrays.asList("Java", "Scala", "Groovy", "Python", "Go", "Swift", "Java"); 
          Stream<String> stream = list.stream() .distinct() 
          // [Java, Scala, Groovy, Python, Go, Swift]
   - 만약 우리가 생성한 클래스를 Stream으로 사용한다고 하면 equals와 hashcode를 오버라이드 해야한 distinct()를 제대로 적용할 수 있다.
   - 만약 다음과 같은 Employee클래스가 있다고 하자
   - ``` java
          public class Employee { 
               private String name; 
               public Employee(String name) { 
                    this.name = name; 
               }
               public String getName() { 
                    return name; 
               } 
          }
   - 위의 Employee클래스는 equals와 hashCode를 오버라이드 하지 않았기 때문에, 아리의 코드를 싱행해도 중복된 데이터가 제거되지 않고, size값으로 2를 출력하게 된다.
   - ``` java
          import java.util.*; 
          public class Main { 
               public static void main(String[] args) { 
                    Employee e1 = new Employee("MangKyu"); 
                    Employee e2 = new Employee("MangKyu");
                    List<Employee> employees = new ArrayList<>();
                    employees.add(e1); employees.add(e2); 
                    int size = employees.stream()
                    .distinct()
                    .collect(Collectors.toList())
                    .size(); 
                    System.out.println(size); 
               } 
          }
   - 그렇기 떄문에 우리는 아래와 같이 equals와 hashCode를 오버라이드하여 이러한 문제를 해결해야 한다.
   - ``` java 
          import java.util.Objects;

          public class Employee {

          private String name;

          public Employee(String name) {
               this.name = name;
          }

          public String getName() {
               return name;
          }

          @Override
          public boolean equals(Object o) {
               if (this == o) return true;
               if (o == null || getClass() != o.getClass()) return false;
               Employee employee = (Employee) o;
               return Objects.equals(name, employee.name);
          }

          @Override
               public int hashCode() {
                    return Objects.hash(name);
               }
          }
   - 위와 같은 코드를 추가하고 main함수를 다시 실행시키면 size는 1이 된다.
5. __<span style="color:#ff9933">[특정 연산 수행 - Peek]</span>__
   - Stream의 요소들을 대상으로 Stream에 영향을 주지 않고 특정 연산을 수행하기 위한 peek함수가 존재한다. '확인해본다'라는 뜻을 지닌 peek단어처럼 peek 함수는 Stream의 각각의 요소들에 대해 특정 작업을 수행할 뿐 결과에 영향을 주지 않는다. 또한 peek 함수는 파라미터로 함수형 인터페이스 Consumer를 인자로 받는다. 예를 들어 어떤 Stream의 요소들을 중간에 출력하기를 원할 때 다음과 같이 활용할 수 있다.
   - ``` java 
          int sum = IntStream.of(1, 3, 5, 7, 9) 
          .peek(System.out::println) .sum();
   - 