# Thread-Safe
## __<span style="color:#9999ff">Thread-Safe란?</span>__
- Multi thread 프로그래밍에서 여러 Thread로부터 어떤 method나, variable, object에 동시에 접근이 이루어져도 프로그램의 실행에 문제가 없는 것
- 하나의 funtion이 한 Thread로부터 호출되어 실행 중일 때, 다른 Thread가 동일한 함수를 호출하여 동시에 실행되더라도 각 Thread에서 함수의 수행 결과가 바르게 나오는 것!

## __<span style="color:#9999ff">Thread-Safe보장을 위한 방법</span>__
- __<span style="color:#ff9933">Mutual exclusion</span>__
  - 
  - 상호배제란, 한 Thread가 자원에 접근할 때 다른 Thread의 접근을 막는 것을 의미한다.</br>공유 자원을 사용해야할 경우 해당 자원의 접근을 semaphore등의 락으로 통제한다. </br> 즉 Critical section을 정의해서 Critical section은 한번에 하나의 스레드만 접근할 수 있도록 해야한다.</br> 자바에서는 synchronized 을 사용한다.
- __<span style="color:#ff9933">Re-entrancy</span>__
  - 
  - 어떤 함수가 한 스레드에 의해 호출되어 실행 중일때 다른 스레드가 그 함수를 호출하더라도 결과가 각각에게 올바로 주어져야 한다.
- __<span style="color:#ff9933">Thread-local storage</span>__
  - 
  - 공유 자원의 사용을 최대한 줄여 각각 스레드에서만 접근 가능한 저장소들을 사용함으로써 동시접근을 막습니다.
- __<span style="color:#ff9933">Atomic operations</span>__
  - 
  - 공유 자원에 접근할 때 원자 연산을 이용하거나 '원자적'으로 정의된 접근 방법을 사용함으로써 상호배제를 구현할 수 있다.
## __<span style="color:#9999ff">Thread-Safe하게 프로그래밍 하기</span>__
- __<span style="color:#ff9933">Lock 사용하기</span>__
  - 
  - 자바에서 가장 쉽고 간편하게 스레드 안전성을 보장하는 방법이 synchronized 키워드를 사용하는 방법이다.
  - ``` java
    /**
    * Sequence
    *
    * @author Brian Goetz and Tim Peierls
    */

    @ThreadSafe
    public class Sequence {
        @GuardedBy("this") private int nextValue;

        public synchronized int getNext() {
            return nextValue++;
        }
    }
  - 위 코드는 Sequence 인스턴스의 getNext() 메서드의 동시성을 보장한다. 즉 getNext()에 접근할 수 있는 스레드는 단 한개만 보장한다는 뜻이다. 따라서 수많은 스레드들이 동시에 getNext()에 접근한다고 하더라도 nextValue 값을 보장받을 수 있다.
  - 메서드에 synchronized 키워드를 붙여서 동기화하는 방법도 있지만 틎정 코드 블록에 다음과 같이 synchronized 블록을 사용하는 방법도 있다.
  - ``` java
    /**
    * PrivateLock
    * <p/>
    * Guarding state with a private lock
    *
    * @author Brian Goetz and Tim Peierls
    */
    public class PrivateLock {
        private final Object myLock = new Object();
        @GuardedBy("myLock") Widget widget;

        void someMethod() {
            synchronized (myLock) {
                // Access or modify the state of widget
            }
        }
    }
  - 위 코드는 자바모니터 패턴을 활용해서 synchronized를 사용했다.
- __<span style="color:#ff9933">자료구조 사용하기</span>__
  - 
  - 불가피하게 synchronized를 사용해야한다면 어쩔수 없지만 선택이 가능하다면 자바에서 제공하는 자료구조를 사용하는게 더 좋은 방법이다. java.concurrent패키지 하위에 좀재하는 자료구조들인데 대표적으로 Hashtable, ConcurrentHashmap,Atomicinteger,blockingQueue 등등이 있다.
  - 동시성 관련된 자료구조를 사용하는 방법은 좋은 방법이지만 어떤 자료구조를 사용하느냐에 따라서 성능이 족므 떨어질 수도 있다.
  - 위에서 소개한 synchronized을 사용하는 방법의 가장 큰 단점은 좋은 성능을 보장하기가 어렵다는 것이다. 예를 들어 자바의 Hashtable의 주요 메서드들을 확인해보자 
  - ``` java
      public synchronized V get(Object key) {
          Entry<?,?> tab[] = table;
          int hash = key.hashCode();
          int index = (hash & 0x7FFFFFFF) % tab.length;
          for (Entry<?,?> e = tab[index] ; e != null ; e = e.next) {
              if ((e.hash == hash) && e.key.equals(key)) {
                  return (V)e.value;
              }
          }
          return null;
      }
      
      ...
      
      
      public synchronized V put(K key, V value) {
          // Make sure the value is not null
          if (value == null) {
              throw new NullPointerException();
          }

          // Makes sure the key is not already in the hashtable.
          Entry<?,?> tab[] = table;
          int hash = key.hashCode();
          int index = (hash & 0x7FFFFFFF) % tab.length;
          @SuppressWarnings("unchecked")
          Entry<K,V> entry = (Entry<K,V>)tab[index];
          for(; entry != null ; entry = entry.next) {
              if ((entry.hash == hash) && entry.key.equals(key)) {
                  V old = entry.value;
                  entry.value = value;
                  return old;
              }
          }

          addEntry(hash, key, value, index);
          return null;
      }
      ...
  - get, put 등등 주요 메서드들이 전부 synchronized키워드에 감싸진 상태로 동시성을 보장하고 있다. 그말인 즉슨 hashtable의 동기화된 인스턴스 메서드를 호출할 수 있는 스레드는 반드시 1개라는 뜻이다. 동시에 호출이 불가능하다.
  - 자바는 hashtable의 성능을 조금 더 증진시키기 위해 1.5버전에서 ConcurrentHashmap을 도입했다. ConcurrentHashmap은 좀더 세밀하게 동시성을 보장하기 때문에 더 좋은 성능을 보장한다.
  - ![](https://user-images.githubusercontent.com/30790184/116840588-5aa33900-ac11-11eb-9daf-2922a66c57a7.png)
  - 다음은 ConcurrentHashmap의 주요 메서드이다. Hashtable과는 달리 get() put()메서드에 synchronized 키워드가 없다.
  - ``` java
      public V get(Object key) {
          Node<K,V>[] tab; Node<K,V> e, p; int n, eh; K ek;
          int h = spread(key.hashCode());
          if ((tab = table) != null && (n = tab.length) > 0 &&
              (e = tabAt(tab, (n - 1) & h)) != null) {
              if ((eh = e.hash) == h) {
                  if ((ek = e.key) == key || (ek != null && key.equals(ek)))
                      return e.val;
              }
              else if (eh < 0)
                  return (p = e.find(h, key)) != null ? p.val : null;
              while ((e = e.next) != null) {
                  if (e.hash == h &&
                      ((ek = e.key) == key || (ek != null && key.equals(ek))))
                      return e.val;
              }
          }
          return null;
      }

      ...


      public V put(K key, V value) {
          return putVal(key, value, false);

      /** Implementation for put and putIfAbsent */
      final V putVal(K key, V value, boolean onlyIfAbsent) {
          if (key == null || value == null) throw new NullPointerException();
          int hash = spread(key.hashCode());
          int binCount = 0;
          for (Node<K,V>[] tab = table;;) {
              Node<K,V> f; int n, i, fh;
              if (tab == null || (n = tab.length) == 0)
                  tab = initTable();
              else if ((f = tabAt(tab, i = (n - 1) & hash)) == null) {
                  if (casTabAt(tab, i, null,
                               new Node<K,V>(hash, key, value, null)))
                      break;                   // no lock when adding to empty bin
              }
              else if ((fh = f.hash) == MOVED)
                  tab = helpTransfer(tab, f);
              else {
                  V oldVal = null;
                  synchronized (f) {
                      if (tabAt(tab, i) == f) {
                          if (fh >= 0) {
                              binCount = 1;
                              for (Node<K,V> e = f;; ++binCount) {
                                  K ek;
                                  if (e.hash == hash &&
                                      ((ek = e.key) == key ||
                                       (ek != null && key.equals(ek)))) {
                                      oldVal = e.val;
                                      if (!onlyIfAbsent)
                                          e.val = value;
                                      break;
                                  }
                                  Node<K,V> pred = e;
                                  if ((e = e.next) == null) {
                                      pred.next = new Node<K,V>(hash, key,
                                                                value, null);
                                      break;
                                  }
                              }
                          }
                          else if (f instanceof TreeBin) {
                              Node<K,V> p;
                              binCount = 2;
                              if ((p = ((TreeBin<K,V>)f).putTreeVal(hash, key,
                                                             value)) != null) {
                                  oldVal = p.val;
                                  if (!onlyIfAbsent)
                                      p.val = value;
                              }
                          }
                      }
                  }
                  if (binCount != 0) {
                      if (binCount >= TREEIFY_THRESHOLD)
                          treeifyBin(tab, i);
                      if (oldVal != null)
                          return oldVal;
                      break;
                  }
              }
          }
          addCount(1L, binCount);
          return null;
        }
      }
- __<span style="color:#ff9933">Stack 한정 프로그래밍</span>__
  - 
  - 스레드는 각기 별도의 스택과 지역변수를 갖도록 되어있다. 따라서 모든 스레드는 각자 고유한 스택과 지역변수를 가진다는 특성을 잘 이해하면 동시성을 보장하도록 할 수 있다.
  - ``` java
      /**
      * Animals
      * <p/>
      * Thread confinement of local primitive and reference variables
      *
      * @author Brian Goetz and Tim Peierls
      */
      public class Animals {
          Ark ark;
          Species species;
          Gender gender;

          public int loadTheArk(Collection<Animal> candidates) {
              SortedSet<Animal> animals;
              int numPairs = 0;
              Animal candidate = null;

              // animals confined to method, don't let them escape!
              animals = new TreeSet<Animal>(new SpeciesGenderComparator());
              animals.addAll(candidates);
              for (Animal a : animals) {
                  if (candidate == null || !candidate.isPotentialMate(a))
                      candidate = a;
                  else {
                      ark.load(new AnimalPair(candidate, a));
                      ++numPairs;
                      candidate = null;
                  }
              }
              return numPairs;
          }
      }
  - animals라는 sortedset인스턴스는 지역변수로 선언되었다. 넘어오는 candidates파라미터를 복사 한 뒤에 추가적인 작업을 실행하는데 지역변수 즉 stack내부에서만 사용했기 때문에 동시성을 보장할 수 있다.
- __<span style="color:#ff9933">ThreadLocal 사용하기</span>__
  - 
  - 위에서 지역변수를 사용해서 동시성을 보장하는 방법은 간결하고 이해하기 쉽지만 저 메서드 스택을 벗어나는 순간 animals라는 변수의 참조가 없어지기 때문에 다른곳에서 animals를 사용할 수 없다는 단점이 있다.
  - 위 같은 단점을 해결하기 위해 자바는 ThreadLocal이라는 클래스를 제공하고 있다.
  - ThreadLocal을 이용하면 쓰레드 영역에 변수를 설정할 수 있기 때문에 특정 쓰레드가 실행하는 모든 코드에서 그 쓰레드에 설정된 변수 값을 사용할 수 있게 된다.
  - 대표적인 사용 예로 SpringSecurity에서 제공하는 SecurityContextHolder가 바로 ThreadLocal 적절한 예가 된다.
- __<span style="color:#ff9933">불변객체 사용하기 feat. final keyword</span>__
  - 
  - 불변객체란 객체가 선언된 이후로는 변경할 수 없는 객체임을 뜻하는데 자바에서 대표적으로 String이 있다. 그렇다면 String은 기본적으로 스레드에 안전하다.
  - ``` java
      import java.util.Date;
      
      /**
      * Always remember that your instance variables will be either mutable or immutable.
      * Identify them and return new objects with copied content for all mutable objects.
      * Immutable variables can be returned safely without extra effort.
      * */
      public final class ImmutableClass
      {
      
          /**
          * Integer class is immutable as it does not provide any setter to change its content
          * */
          private final Integer immutableField1;
      
          /**
          * String class is immutable as it also does not provide setter to change its content
          * */
          private final String immutableField2;
      
          /**
          * Date class is mutable as it provide setters to change various date/time parts
          * */
          private final Date mutableField;
      
          //Default private constructor will ensure no unplanned construction of class
          private ImmutableClass(Integer fld1, String fld2, Date date)
          {
              this.immutableField1 = fld1;
              this.immutableField2 = fld2;
              this.mutableField = new Date(date.getTime());
          }
      
          //Factory method to store object creation logic in single place
          public static ImmutableClass createNewInstance(Integer fld1, String fld2, Date date)
          {
              return new ImmutableClass(fld1, fld2, date);
          }
      
          //Provide no setter methods
      
          /**
          * Integer class is immutable so we can return the instance variable as it is
          * */
          public Integer getImmutableField1() {
              return immutableField1;
          }
      
          /**
          * String class is also immutable so we can return the instance variable as it is
          * */
          public String getImmutableField2() {
              return immutableField2;
          }
      
          /**
          * Date class is mutable so we need a little care here.
          * We should not return the reference of original instance variable.
          * Instead a new Date object, with content copied to it, should be returned.
          * */
          public Date getMutableField() {
              return new Date(mutableField.getTime());
          }
      
          @Override
          public String toString() {
              return immutableField1 +" - "+ immutableField2 +" - "+ mutableField;
          }
      }
  - 기본적으로 setter메서드가 없고 생성자가 private으로 선언되어있으며 실제 인스턴스는 팩토리 메서드를 통해서 생성하도록 외부에 공개되었다. 그리고 모든 멤버변수들은 final로 선언되어있는데 integer나 String은 기본적으로 불변이기 때문에 반환할때도 그대로 리턴해도 되지만 Date의 경우 가변객체이기 때문에 반드시 방어적 복사본을 생성하는 방법으로 프로그래밍을 해야 동시성을 보장받을 수 있다.
  - 적절한 final키워드도 별다른 동기화 작업이 없이도 동시성환경에서 자유롭게 사용할 수 있다. 불변객체와 비슷한 관점으로 초기화된 이후에 변경될 수 없기 때문에 여러스레드가 동시에 접근해도 동일한 값을 보장받을 수 있기 때문이다.
# 참고
- https://velog.io/@cateto/Java-Thread-Safe%EB%9E%80
- https://sup2is.github.io/2021/05/03/thread-safe-in-java.html
- https://zerodev.tistory.com/82
#javabasic #java