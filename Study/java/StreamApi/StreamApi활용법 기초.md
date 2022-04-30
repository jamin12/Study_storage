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
6. __<span style="color:#ff9933">[원시 Stream <-> Stream]</span>__
   - 작업을 하다 보면 일반적인 Stream객체를 원시 Stream으로 바꾸거나 그 반대로 하는 작업이 필요한 경우가 있다. 이러한 경우를 위해서 일반적인 Stream 객체는 mapToint(), MapToLong(), maptodouble()이라는 특수한 mapping연산을 지원하고 있으며 그반대로 원시객체를 mapToobject를 통해 일반적인 Stream객체로 바꿀 수 있다.
   - ``` java
          // IntStream -> Stream<Integer> 
          IntStream.range(1, 4).mapToObj(i -> "a" + i) 
          // Stream<Double> -> IntStream -> Stream<String> 
          Stream.of(1.0, 2.0, 3.0) 
          .mapToInt(Double::intValue) 
          .mapToObj(i -> "a" + i)
## __<span style="color:#9999ff">Stream 결과 만들기</span>__
- 중간 연산을 통해 생성된 Stream을 바탕으로 이제 결과를 만들 차례이다. 결과를 만들기 위한 최종 연산에는 다음과 같은 것들이 존재한다.
1. __<span style="color:#ff9933">[ 최댓값/최솟값/총합/평균/갯수 - Max/Min/Sum/Average/Count ]</span>__
   - Stream의 요소들을 대상으로 최솟값이나. 최댓값 또는 총합을 구하기 위한 최종 연산들이 존재한다. 최솟값이나 최댓값을 구하기 위해서는 max,min을 이용해야 하며, 총합 또는 평균 또는 개수를 구하기 위해서는 sum, average, count,를 이용해야한다. min이나 max또는 average는 Stream이 비어있는 경우에 값을 특정할 수 없다. 그렇기 때문에 다음과 같이 optional로 값이 반환된다.
   - ``` java 
          OptionalInt min = IntStream.of(1, 3, 5, 7, 9).min(); 
          int max = IntStream.of().max().orElse(0); 
          IntStream.of(1, 3, 5, 7, 9).average().ifPresent(System.out::println);
   - 반면에 총합이나 갯수에 경우에는 값이 비어있는 경우 0으로 값을 특정할 수 있다. 그렇기 때문에 Stream API는 sun메서드와 count메소드에 대해 Optional이 아닌 원시 값은 반환하도록 구현해두었다. 당연히 Stream이 비어있을 겨웅에는 0을 반환하게 된다.
   - ``` java
          long count = IntStream.of(1, 3, 5, 7, 9).count(); 
          long sum = LongStream.of(1, 3, 5, 7, 9).sum();
2. __<span style="color:#ff9933">[데이터 수집 - collect]</span>__
   - Stream의 요소들을 List나 set,Map등 다른 종류의 결과로 수집하고 싶은 경우에는 collect함수를 이용할 수 있다. collect함수는 어떻게 Stream의 요소들을 수집할 것인가를 정의한 Collector타입을 인자로 받아서 처리한다. 일반적으로 List로 Stream의 요소들을 수집하는 경우가 많은데 이렇듯 자주 사용하는 작업은 Collectors객체에서 static메소드로 제공하고 있다. 원하는 것이 없는 경우에는 collector인터페이스를 직접구현하여 사용할 수도 있다.
   - ``` java 
          collect() : 스트림의 최종연산, 매개변수로 Collector를 필요로 한다. 
          Collector : 인터페이스, collect의 파라미터는 이 인터페이스를 구현해야한다. 
          Collectors : 클래스, static메소드로 미리 작성된 컬렉터를 제공한다. 

          // collect의 파라미터로 Collector의 구현체가 와야 한다. 
          Object collect(Collector collector)
   - collect()를 응용한 다양한 예제들을 살펴보기 위해 다음과같은 데이터가 사전에 정의되어 있다고 하자. product 객체는 수량과 이름을 변수로 가지며 주어진 데이터를 다양한 방식으로 수집해볼 것이이다.
   - ``` java
          List<Product> productList = Arrays.asList( 
               new Product(23, "potatoes"), 
               new Product(14, "orange"), 
               new Product(13, "lemon"), 
               new Product(23, "bread"), 
               new Product(13, "sugar"));
     ```
     - __<span style="color:#14a492">Collectors.toList</span>__
       - Stream에서 작업한 결과를 List로 반환받을 수 있다. 아래의 예제에서는 STream의 요소들을 Product의 이름으로 반환하여 그 결과를 List로 반환하고 있다.
       - ``` java
               List<String> nameList = productList.stream() 
               .map(Product::getName) 
               .collect(Collectors.toList());
       - 해당 결과를 set으로 반환받기를 원한다면 Collectors.toSet()를 사용하면 된다.
     - __<span style="color:#14a492">Collectors.joining()</span>__
       - Stream에서 작업한 결과를 1개의 String으로 이어붙이기를 원하는 경우에 Collectors.joining()을 이용할 수 있다. Collectors.joining()은 총 3개의 인자를 받을 수 있는데 이를 이요하면 간단하게 String을 조합할 수 있다.
         - delimiter : 각 요소 중간에 들어가 요소를 구분시켜주는 구분자.
         - prefix: 결과 매앞에 붙는 문자.
         - suffix: 결과 맨 뒤에 붙는 문자
       - ``` java
               String listToString = productList.stream() 
                         .map(Product::getName) 
                         .collect(Collectors.joining()); 
               // potatoesorangelemonbreadsugar 

               String listToString = productList.stream() 
                         .map(Product::getName) 
                         .collect(Collectors.joining(" ")); 
               // potatoes orange lemon bread sugar 

               String listToString = productList.stream() 
                         .map(Product::getName) 
                         .collect(Collectors.joining(", ", "<", ">")); 
               // <potatoes, orange, lemon, bread, sugar>
     - __<span style="color:#14a492">Collectors.averagingInt(), Collectors.summingInt(), Collectors.summarizingInt()</span>__
       - Stream에서 작업한 결과의 평균값이나 총합 등을 구하기 위해는 Collectors.averagingInt()와 Collectors.summingInt()를 이용할 수 있다. 물론 총합의 경우 이를 구현할 수 있는 방법이 그 외에도 많이 있다.
       - ``` java
               Double averageAmount = productList.stream() 
               .collect(Collectors.averagingInt(Product::getAmount)); 

               // 86 
               Integer summingAmount = productList.stream() 
               .collect(Collectors.summingInt(Product::getAmount)); 

               // 86
               Integer summingAmount = productList.stream() 
               .mapToInt(Product::getAmount) 
               .sum();
       - 만약 한개의 Stream으로부터 갯수,합계 평균 최댓값 최솟값을 한번에 얻고싶은 경우엔 Collectors.summarizingInt()를 이용하면 된다. 이를 이용하면 IntSummaryStatistics객체가 반환되며, 필요한 값에 대해 get 메소드를 이용하여 원하는 값을 꺼내면 된다.
       - ``` java 
               IntSummaryStatistics statistics = productList.stream() 
               .collect(Collectors.summarizingInt(Product::getAmount)); 
               
               //IntSummaryStatistics {count=5, sum=86, min=13, average=17.200000, max=23}
     - __<span style="color:#14a492">Collectors.groupingBy()</span>__
       - Stream에서 작업한 결과를 특정 그룹으로 묶기를 원할 수 있다. 이러한 경우에는 Collectors.groupingBy()를 이용할 수 있으며, 결과를 Map으로 반환받게 된다. groupingBy는 매개변수로 함수형 인터페이스 Function을 필요로 한다.
       - 수량을 기준으로 grouping을 원하는 경우 다음과 같이 작성할 수 있으며, 같은 수량일 경우에는 List로 묶어서 값을 반환받게 된다.
       - ``` java
               Map<Integer, List<Product>> collectorMapOfLists = productList.stream() 
               .collect(Collectors.groupingBy(Product::getAmount)); 
               
               /* 
               {
                    23=[Product{amount=23, name='potatoes'}, Product{amount=23, name='bread'}], 
                    13=[Product{amount=13, name='lemon'}, Product{amount=13, name='sugar'}], 
                    14=[Product{amount=14, name='orange'}]
               } 
               */
       - __<span style="color:#14a492">Collectors.partitioningBy()</span>__
         - partitioningBy()는 함수형 인터페이스 predicate를 받아 bollean을 key값으로 partitioning한다. 예를 들어 제품의 갯수가 15보드 큰 경우와 그렇지 않은 경우를 나누고자 한다면 다음과 같이 코드를 작성할 수 있다.
         - ``` java 
               Map<Boolean, List<Product>> mapPartitioned = productList.stream() 
               .collect(Collectors.partitioningBy(p -> p.getAmount() > 15)); 
               
               /* 
               {
                    false=[Product{amount=14, name='orange'}, Product{amount=13, name='lemon'}, Product{amount=13, name='sugar'}], 
                    true=[Product{amount=23, name='potatoes'}, Product{amount=23, name='bread'}]
               } 
               */
3. __<span style="color:#ff9933">[조건 검사 - Match]</span>__
   - Stream의 요소들이 특정한 조건을 충족하는지 검사하고 싶은 경우에는 match함수를 이용할 수 있다. match 함수는 함수형 인터페이스 predicate를 받아서 해당 조건을 만족하는지 검사를 하게 되고, 검사 결과를 boolean으로 반환한다. match함수에는 크게 다음의 3가지가 있다.
     - anyMatch: 1개의 요소라도 해당조건을 만족하는가
     - allMatch: 모든 요소가 해당 조건을 만족하는가
     - nonMatch: 모든 요소가 해당 조건을 만족하지 않는가
   - 예를 들어 다음과 같은 예시 코드가 있다고 할 때, 아래의 경우 모두 true를 반환하게 된다.
   - ``` java
          List<String> names = Arrays.asList("Eric", "Elena", "Java"); 
          boolean anyMatch = names.stream() 
               .anyMatch(name -> name.contains("a")); 
          boolean allMatch = names.stream() 
               .allMatch(name -> name.length() > 3); 
          boolean noneMatch = names.stream() 
               .noneMatch(name -> name.endsWith("s"));
4. __<span style="color:#ff9933">[특정 연산 수행 - forEach]</span>__
   - Stream의 요소들을 대상으로 어떤 특정한 연산을 수행하고 싶은 경우에는 forEach함수를 이용할 수 있다. 앞에서 살펴본 비슷한 함수로 peek()이 있다. peek()은 중간 연산으로써 실제 요소들에 영향을 주지 않은채로 작업을 진행하고  Stream을 반환하는 합수였다 하지만 forEach()는 최종 연산으로써 실제 요소들에 영향을 줄 수 있으며, 반환 값이 존재하지 않는다. 
# 참고
- https://mangkyu.tistory.com/114