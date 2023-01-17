## __영속성 컨텍스트__
1. __엔티티 매니저 팩토리와 엔티티 매니저__
   - **EntitiyManagerFactory**:
     - 만드는 비용이 상당히 큼.
     - 한 개만 만들어서 어플리케이션 전체에서 공유하도록 설계
     - 여러 스레드가 동시에 접근해도 안전, 서로 다른 스레드 간 공유가능
   - **EntitiyManager**
     - 여러 스레드가 동시에 접근하면 동시성 문제 발생
     - 스레드간 절대 공유하면 안된다.
     - 데이터베이스 연결이 필요한 시점까지 커넥션을 얻지 않는다.
   - JPA는 스레드가 하나 생성될 떄마다(매 요청마다) EntitiyManagerFactory에서 EntityManager를 생성한다.
   - EntityManager는 내부적으로 DB커넥션 풀을 사용해서 DB에 붙는다.
- ![엔티티 팩토리 엔티티 매니저 사진](https://github.com/namjunemy/TIL/blob/master/Jpa/tacademy/img/10_jpa_em.PNG?raw=true)

2. __영속성 컨텍스트__
   - 영속성 컨텍스트는 JPA를 이해하는데 가장 중요한 용어이다.
   - "엔티티를 영구 저장하는 환경"이라는 뜻
   - EntityManager.persist(entity);
      - 앞의 예제에서 persist()로 db에 객체를 저장하는 것이라고 배웠지만,
      - 실제로는 DB에 저장하는것이 아니라, 영속성 컨텍스트를 통해서 엔티티를 영속화 한다는 뜻이다.
      - 정확히 말하면 persist() 시점에는 영속성 컨텍스트에 저장한다. DB 저장은 이후이다.
    - 엔티티 매니저? 영속성 컨텍스트?
       - __영속성 컨텍스트는 논리적인 개념이다.__
       - 눈에 보이지 않는다.
       - 엔티티 매니저를 통해서 영속성 컨텍스트에 접근한다.
       - ![](https://github.com/namjunemy/TIL/blob/master/Jpa/tacademy/img/11_jpa_em.PNG?raw=true)
       - 스프링에서 EntityManager를 주입 받아서 쓰면, 같은 트랜잭션의 범위에 있는 ENtitiyManager는 동일 영속성 컨텍스트에 접근한다.


3. __엔티티의 생명주기__
- ![](https://github.com/namjunemy/TIL/blob/master/Jpa/tacademy/img/12_entity_lifecycle.PNG?raw=true)
   - 비영속(new/transient)
     - 영속성 컨텍스트와 전혀 관계가 없는 상태
     - ``` java
          // 객체를 생성만 한 상태(비영속)
          Member member = new Member();
          member.setId("member1");
          member.setUsername("회원1");
        ```
    - 영속(managed)
      - 영속성 컨텍스트에 저장된 상태
      - 엔티티가 영속성 컨텍스트에 의해 관리된다.
      - 이떄 DB에 저장되지 않는다. 영속 상태가 된다고 DB에 쿼리가 날라가지 않는다.
      - 트랜잭션의 커밋 시점에 영속성 컨텍스트에 있는 정보들이 DB에 쿼리로 날라간다.
      - ``` java
        // 객체를 생성한 상태(비영속)
        Member member = new Member();
        member.setId("member1");
        member.setUsername("회원1");
        ​
        EntityManager em = emf.createEntityManager();
        em.getTransaction().begin();
        ​
        // 객체를 저장한 상태(영속)
        em.persist(member);
        ```
    - 준영속(deatched)
      - 영속성 컨텍스트에 저장되었다가 분리된 상태
      - ``` java
        // 회원 엔티티를 영속성 컨텍스트에서 분리, 준영속 상태
        em.detach(member);
        ```
    - 삭제(removed)
      - 삭제된 상태 DB에서도 날린다.
      - ``` java
        // 객체를 삭제한 상태
        em.remove(member);
        ```
1. __영속성 컨텍스트의 이점__
   - **1차 캐시**
     - ![](https://github.com/namjunemy/TIL/blob/master/Jpa/inflearn/img/03_persistence_context_cache.PNG?raw=true)
      - 영속성 컨텍스트(엔티티 매니저)에는 내부 1차 캐시가 존재한다.
      - 엔티티를 영속성 컨텍스트에 저장하는 순간 1차캐시에 저장된다.
      - key: @id로 선언한 필트값 value: 해당 엔티티 자체로 캐시에 저장된다.
      - 1차 캐시가 있으면 어떤 이점이있을까? 조회할 떄 이점이 생긴다.
      - find()가 일어나는 순간, 엔티티 매니저 내부의 1차 캐시를 먼저 찾는다.
      - __주의!__ 1차캐시는 글로벌하지 않다. 해당 스레드 하나가 시작할 때 부터 끝날떄 까지 잠깐 쓰는거다. 공유하지 않는 캐시다.
        - 100명 한테 요청 100개 오면 엔티티 매니저가 100개 생기고 1차캐시도 100개 생긴다. 스레드가 종료되면, 그때 다 사라진다.
        - 트랜잭션의 범위 안에서만 사용하는 굉장히 짧은 캐시 레이어이다.
        - 전체에서 쓰는 캐시는 2차 캐시라고 한다.
        - ``` java
          Member member = new Member();
          member.setId("member1");
          member.setUsername("회원1");
          ​
          ...
          ​
          // 1차 캐시에 저장됨
          em.persist(member);
          ​
          // 1차 캐시에서 조회
          Member findMember = em.find(Member.class, "Member1");
          ```
      - 1차 캐시에 데이터가 없다면? 데이터베이스에서 조회한다.
        - member2를 조회하는데 1차 캐시에 해당 엔티티가 없다.
        - 그러면 DB에서 꺼내온다.
        - 그리고 1차 캐시에 저장한다.
        - 그후 member2를 반환한다.
        - 다시 member2를 조회하면 1차캐시에 있는 member2가 반환 된다.
          - select 쿼리가 안나간다. 한 트랜잭션 내에서
    - **동일성(identity) 보장**
      - 영속 엔티티의 동일성을 보장한다.
      - 1차 캐시덕분에 member1을 두번 조회해도 다른 객체가 아니다. 같은 레퍼런스가 된다.
      - 1차 캐시로 반복 가능한 읽기 등급의 트랜잭션 격리 수준을 데이터베이스가 아닌 애플리케이션 차원에서제공한다.
      - ``` java
        Member a = em.find(Member.class, "Member1");
        Member b = em.find(Member.class, "Member2");
        ​
        Systen.out.println(a == b); // 동일성 비교 true
        ```
    - **트랜잭션을 지원하는 쓰기 지연(transactional write-behind)-엔티티 등록**
      -![](https://github.com/namjunemy/TIL/blob/master/Jpa/inflearn/img/04_transactional_write_behind.PNG?raw=true)
      - 트랜잭션 내부에서 persist()가 일어날때,
      - 엔티티들을 1차 캐시에 저장하고, 논리적으로 쓰기지연 SQL저장소 라는 곳에 INSERT쿼리들을 생성해서쌓아 놓는다.
      - DB에 바로 넣지 않고 기다린다.
      - commit()하는 시점에 DB에 동시에 쿼리들을 쫙 보낸다.(쿼리를 보내는 방식은 동시 or 하나씩 옵션에따라.)
      - 이렇게 쌓여있는 쿼리들을 DB에 보내는 동작이 flush()이다.
      - flush()는 1차캐시를 지우지 않는다 쿼리들을 DB에 날려서 DB와 싱크를 맞추는 역할을 한다.
      - 실제로 퀘리를 보내고 나서, commit()한다.
      - 트랜잭션 커밋하게 되면 flush()와 commit() 두가지 일을 하게 되는 것이다.
      - ``` java
        EntityManager em = emf.createEntityManager();
        EntityTransaction transaction = em.getTransaction();
        // 엔티티 매니저는 데이터 변경시 트랜잭션을 시작해야 한다.
        transaction.begin(); // 트랜잭션 시작
        ​
        em.persist(memberA);
        em.persist(memberB);
        // 이때까지 INSERT SQL을 데이터베이스에 보내지 않는다.
        ​
        // 커밋하는 순간 데이터베이스에 INSERT SQL을 보낸다.
        transaction.commit(); // 트랜잭션 커밋
        ```
      - persistence.xml에 아래와 같은 옵션을 줄 수 있다.
        - JDBC일괄 처리 옵션으로 커밋 직전까지 insert쿼리를 해당 사이즈 만큼 모아서 한번에 처리한다.
      - ``` xml
          <property name="hibernate.jdbc.batch_size" value=10/>
        ```
    - **변경감지(Dirty Checking) - 엔티티 수정**
      - 엔티티 수정이 일어나면 update나 persist로 영속성 컨텍스트에 알려줘야 하지 않을까?
      - ``` java
        EntityManager em = emf.createEntityManager();
        EntityTransaction transaction = em.getTransaction();
        transaction.begin(); // 트랜잭션 시작
        ​
        // 영속 엔티티 조회
        member memberA = em.find(Member.class, "memberA");
        ​
        // 영속 엔티티 수정
        memberA.setUsername("nj");
        memberA.setAge(27);
        ​
        //mem.update(member) 또는 em.persist(member)로 다시 저장해야 하지 않을까?
        ​
        transaction.commit(); // 트랜잭션 커밋
        ```
      - 엔티티 데이터만 수정하면 끝이다. 데이터만 set하고 트랜잭션을 커밋하면 자동으로 업데이트 쿼리가나간다.
      - 1차 캐시에 저장할 때 동시에 스냅샷 필드도 저장한다.
      ![](https://github.com/namjunemy/TIL/blob/master/Jpa/tacademy/img/13_dirty_checking.PNG?raw=true)
      - 그러고 나서 commit()또는 flush()가 일어날 떄 엔티티와 스냅샷을 비교해서, 변경사항이 있으면 updateSQL을알아서 만들어서 DB에 저장한다.
    - **엔티티 삭제**
      - ``` java
        Member memberA = em.find(Member.class, "memberA");
        ​
        em.remove(memberA); // 엔티티 삭제
        ```
      - 삭제는 위의 매커니즘이랑 같고 트랜잭션의 commit시점에 DELETE쿼리가 나간다.
2. __플러시__
    - 플러시는 영속성 컨텍스트의 변경 내용을 테이터베이스에 반영한다.
    - 트랜잭션 커밋이 일어날 때 플러시가 동작하는데, 쓰기 지연 저장소에 쌓아놨던 INSERT,UPDATE,DELETE SQL들이 데이터베이스에 날라간다.
    - 쉽게 얘기해서 영속성 컨텍스트의 변경 사항들과 데이터베이스를 싱크하는 작업이다
    - **플러시 발생**
      - 변경을 감지한다 Dirty Checking
      - 수정된 엔티티를 쓰기 지연 SQL 저장소에 등록한다.
      - 쓰기 지연 저장소의 쿼리를 데이터베이스에 전송한다. 만일플러시가 발생한다고 커밋이 이루지는게 아니고, 플러시 다음에 커밋이 일어난다.
    - **영속성 컨텍스트를 flush 하는 방법**
      - em.flush()로 직접 호출
      - ``` java
        Member member = new Member(200L, "A");
        em.persist(member);
        ​
        em.flush();
        ​
        System.out.println("플러시 직접 호출하면 쿼리가 커밋 전 플러시 호출 시점에 나감");
        ​
        transaction.commit();
        ```
 
      - 트랜잭션 커밋시 플러시 자동 호출
      - JPQL 쿼리 실행하면 플러시 자동 호출
        - 아래와 같이 member1,2,3을 영속화한 상태에서 쿼리는 안날라간 상태
        - JPQL로 SELECT 쿼리를 날리려고 하면 저장되어 있는 값이 없어서 문제가 생길 수 잇다.
        - JPA는 이런 상황을 방지하고자 JPQL실행 전에 무조건 flush()로  DB와 싱크를 맞춘다음에 JPQL쿼리를 날리도록 설정 되어있다.
        - 그래서 아래 상황에서는 JPQL로 맴버들을 조회할 수 있다.
        - ``` java
          em.persist(memberA);
          em.persist(memberB);
          em.persist(memberC);
          ​
          // 중간에 JPQL 실행
          query = em.createQuery("select m from Member m", Member.class);
          List<Member> members = query.getResultList();
          ```
        - 플러시가 일어나면 1차 캐시가 삭제될까?
          - 삭제되지 않는다 쓰기 지연 SQL저장소에 있는 쿼리들만 DB에 전송되고 1차 캐시는 남아있다.
      - **플러시 모드 옵션**
        - em.setFlushMode(FlushModeType.COMMIT);
        - FlushModeType.AUTO
          - 커밋이나 쿼리를 실행할 때 플러시(기본값)
        - FlushModeType.COMMIT
          - 커밋 할때만 플러시
      - **플러시 정리**
        - 플러시는 영속성 컨텍스트를 비우지 않는다.
        - 플러시는 영속성 컨텍스트의 변경 내용을 데이터베이스에 동기화 한다.
        - 플러시가 동작할 수 있는 이유는 데이터베이스 트랜잭션이라는 작업 단위가 있기 떄문이다.
          - 트랜잭션이 시작되고 커밋되는 시점에만 동기화 해주면 되기 떄문에 그 사이에서 플러시 매커니즘의 동작이 가능한 것이다.
        - JPA는 기본적으로 데이터를 맞추거나 동시성에 관련된 것들은 데이터베이스 트랜잭션에 위임한다.
3. __준영속 상태__
    - 영속 상태
      - 영속성 컨텍스트의 1차 캐시가 올라간 상태가 영속 상태이다. 엔티티 매니저가 관리하는 상태
      - em.persist()로 영속성 컨텍스트에 저장한 상태도 영속 상태이지만,
      - em.find()로 조회를 할 때 영속성 컨텍스트 1차캐시에 없어서 db에서 조회해와서 1차캐시에 저장한 샅애도 영속 상태다
    - 준 영속 상태 - 영속 상태의 엔티티가 영속성 컨텍스트에서 분리된 상태(detached)
      - em.deatch()로 맴버를 영속성 컨텍스트에서 분리하고
      - 트랜잭션을 커밋하면 아무 일도 일어나지 않는다.JPA가 관리 하지 않는 객체가 된다.
      - 실제로 아래에선 update쿼리가 나가지 않는다.
      - ``` java
        Member member = em.find(Member.class, 150L);
        member.setName("AAAAA");
        ​
        em.detach(member);
        ​
        transaction.commit();
      - 영속성 컨텍스트가 제공하는 기능을 사용하지 못함 쿼리 안나감.
      - 준영속성 상태로 만드는 방법
        - em.detach() - 특정 엔티티만 준영속 상태로 전환
        - em.clear() - 영속성 컨텍스트를 완전히 초기화
          - 아래 코드의 경우 첫번째 find에서 멤버를 select해서 영속성 컨텍스트에 저장하고
          - 영속성 컨텍스트를 초기화 했다.
          - 그러고 나서 같은 아이디를 가지는 멤버를 다시 조회했을떄 select쿼리가 다시 나간다.
          - 총 2번의 select쿼리가 발생한다.
          - clear는 테스트 케이스 작성시 도움이 된다.
          - ``` java 
            Member member = em.find(Member.class, 150L);
            member.setName("AAAAA");
            ​
            em.clear();
            ​
            Member member1 = em.find(Member.class, 150L);
            ​
            transaction.commit();
          - em.close() - 영속성 컨텍스트를 
4. __2차 캐시__
   - ![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2Fx4lJI%2FbtqX827lJ2f%2FriUkurtYNEfj2C9YedbHHK%2Fimg.png)
   - 애플리케이션 범위의 캐시로, 공유 캐시라고도 한다.
   - 따라서 애플리케이션을 종료할 때 까지 캐시가 유지된다.
   - 2차 캐시는 동시성을 극대화하기 위해 __캐시 한 객체를 직접 반환하지 않고 복사본을 만들어서 반환한다.__ 캐시한 객체를 그대로 반환하면 여러 곳에서 같은 객체를 동시에 수정하는 동시성 문제가 발생할 수 있음 이를 해경하기 위해서 객체에 락을 걸어야 하는데 이러면 동시성이 떨어질 수 있다. 그래서 2차 캐시는 원본 대신에 복사본을 반환 한다.
   - __영속성 컨텍스트가 다르면 객체 동일성을 보장하지 않는다__
# 참고
- https://ict-nroo.tistory.com/130
- https://willseungh0.tistory.com/77