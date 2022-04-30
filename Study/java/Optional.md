## Optional
1. NPE
   - 개발을 할 때 가장 많이 발생하는 예외 중 하나가 바로 NPE(nullPointerExecption)이다. NPE를 피하기 위해서는 null 검사 로직을 추가해야 하는데 nill 검사를 해야하는 변수가 많은 경우 코드가 복잡해지고 로직이 상당히 번거롭다. 그렇게 때문에 null대신 초기값을 사용하길 권장하기도 한다.
2. Optional이란?
   - Java8에서는 Optional<T> 클래스를 사용해 NPE를 방지할 수 있도록 도와준다. Optional<T>는 null이 올 수 잇는 값을 감싸는 wrapper 클래스로 참조하더라도 NPE가 발생하지 않도록 도와준다. Optional클래스는 아래와 같은 value에 값을 저장하기 떄문에 값이 null이라도 NPE가 발생하지 않으며 클래스이기 때문에 각종 메소드를 제공해준다.
   - ``` java
        public final class Optional<T> { 
            // If non-null, the value; if null, indicates no value is present 
            private final T value; 
            ...
        }
    - Optional은 null또는 실제 값을 value로 갖는 wrapper로 감싸서 NPE로부터 자유로워지기 위해 나온 wrapper클래스이다. 또한 Optional은 파라미터로 넘어가는 등이 아니라 반환 타입으로써 제한적으로 사용도록 설계 되었다. Optional은 값을 wrapping하고 다시 풀고 null경우 대체하는 함수를 호출하는 등의 오버헤드가 잇으므로 잘못 사용하면 성능을 저하시킬 수 있다. 글렇기 떄문에 메소드의 반환 값이 절대 null이 아니라면 Optional을 사용하지 않는 것이 좋다. 즉 optional은 메소드의 <span style=color:#ff5050> __결과가 null이 될 수 있으며 null에 의해 오류가 발생할 가능성이 매우 높을 때 반환값으로만 사용되어야 한다.__</span>
3. 언제 Optional을 사용해야 하는가?
   - Optional은 null을 반환하면 오류가 발생할 가능성이 매우 높은 경우에 '결과 없음'을 명확하게 드러내기 위해 메소드의 반환 타입으로 사용되도록 매우 제한적인 경우로 설계되었다 <br/> 이러한 설계 목적에 부합하게 실제로 Optional을 잘못 사용하면 많은 <span style=color:#ff5050>__부작용이__</span> 발생하게 된다.
     1. NPE대신 NoSuchElementException이 발생함
        - 만약 Optional로 받은 변수를 값이 있는지 판단하지 않고 접근하려고 한다면 NoSuchElementException이 발생하게 된다.
        - Null-Saft하기 위해 Optional을 사용하엿는데, 값의 존재 여부를 판단하지 않고 접근한다면 NPE는 피해도 NSEE가 발생할 수 있다.   
     2. 이전에 없었던 새로운 문제들이 발생함
        - Optional을 사용하면 문제가 되는 대표적인 경우가 직렬화를 할 떄이다, 기본적으로 Optional은 직렬화를 지원하지 않기 때문에 캐시나 메세지큐 등과 연동할 때 문제가 발생할 수 있다. 이러한 상황은 Optional을 사용할 때 오히려 새로문 문제가 발생하는 경우이다.
     3. 시간적, 공간적 비용(또는 오버헤드)이 증가함
        - 공간적 비용: Optional은 객체를 감싸는 컨테이너 이므로 기존의 객체를 저장하기 위한 메모리에 더해 optional객체를 저장하기 위한 메모리가 할당된다. 이는 추가적인 메모리를 사용하는 것이다.
        - 시간적 비용: Optional 안에 있는 객체를 얻기 위해서는 Optional 객체를 통해 접근해야 하므로 접근 비용이 증가한다.
    - <span style=color:#ff5050>__올바른 Opitonal사용법__</span>
      1. optional 변수에 Null을 할당하지 말아라
         - Optional은 컨테이너/박싱 클래스일 뿐이며 Optional 변수에 null을 할당하는 것은 Optional 변수 자체가 null인지 또 검사해야 하는 문제를 야기한다. 그러므로 값이 없는 경우라면 Optional.empty()로 초기화 하도록 하자.
      2. 값이 없을 때 Optional.orElseX로 기본 값을 반환하라.
         - Option의 장접 중 하나는 함수형 인터페이스를 통해 가독성좋고 유연한 코드를 작성할 수 있다는 것이다. 가급적이면 isPersent()로 검사하고 get으로 값을 꺼내기 보다는 orElseGet 등을 활용해 처리하도록 하자.
      3. 단순히 값을 얻으려는 목적만으로만 Optional을 사용하지 마라
         - 단순히 값을 얻으려고 Optional을 사용하는것은 Optional을 남용하는 대표적인 경우다. 이러한 경우에는 굳이 Optional을 사용해 비용을 낭비하는 것 보다 직접 값을 다루는 것이 좋다.
      4. 생성자, 수정자, 메소드 파라미터,접근자 등으로 Optional을 넘기지 마라
         - Optional을 파라미터로 넘기는 것은 상당히 의미없는 행동이다. 왜냐하면 넘겨온 파라미터를 위해 자체 null체크도 추가로 해주어야하고, 코드도 복잡해지는 등 상ㅎ당히 번거로워지기떄문이다. Optional은 반환 타입으로 대체 동작을 사용하기 위해 고안된 것임을 명심해야 하며, 앞서 성명한대로 Serializable을 구현하지 않음으로 필드 값으로 사용하지 않아야한다.
      5. Collection의 경우 Optional을 사용하지 말고 빈 Collection으로 처리하라
         - Collection의 경우 굳이 Optional로 감쌀 필요가 없다. 오히려 빈 Collection을 사용하는 것이 깔끔하고 처리가 가볍다.
         - ``` java
            // AVOID 
            public Optional<List<User>> getUserList() { 
               List<User> userList = ...; // null이 올 수 있음 
               return Optional.ofNullable(items); 
               } 
               // PREFER 
               public List<User> getUserList() { 
                  List<User> userList = ...; // null이 올 수 있음 
                  return items == null ? Collections.emptyList() : 
                  userList; 
               }
      6. 반환 타입으로만 사용하라
          - Optional을 반환 타입으로써 에러가 발생할 수 있는 경우에 결과 없음을 명확히 드러내기 위해 만들어졌으며, StramAPi와 결합되어 유연한 체이닝 API를 만들기위해 탄생한 것이다.
# 참고
- https://mangkyu.tistory.com/203