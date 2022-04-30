# 즉시로딩과 지연로딩
- member를 조회할때 team도 함께 조회해야할까?
  - 비즈니스 로직에서 단순히 멤버 로직만 사용하는데 함께 조회하면 아무리 연관관계가 걸려있다고 해도 손해이다.
- JPA는 이 문제를 지연로딩 LAZY를 사용해서 프록시로 조회하는 방법으로 해결한다.
## __코드로 이해하기__
- member와 team사이가 다대일 @manytoone관계로 매핑되어 있는 상황에서,
- @manytoone 어노테이션에 fetch 타입을 줄 수 있다.
- ``` java
    @Entity
    @Getter
    @Setter
    public class Member extends BaseEntity {
    ​
        @Id
        @GeneratedValue(strategy = GenerationType.IDENTITY)
        private Long id;
    ​
        @Column(name = "name")
        private String username;
    ​
        private Integer age;
    ​
        @Enumerated(EnumType.STRING)
        private RoleType roleType;
    ​
        @Lob
        private String description;
    ​
        // 패치 타입 LAZY 설정
        @ManyToOne(fetch = FetchType.LAZY)
        @JoinColumn(name = "team_id", insertable = false, updatable = false)
        private Team team;
    ​
        @OneToOne
        @JoinColumn(name = "locker_id")
        private Locker locker;
    ​
        @OneToMany(mappedBy = "member")
        private List<MemberProduct> memberProducts = new ArrayList<>();
    ​
        public void changeTeam(Team team) {
            this.team = team;
            this.team.getMembers().add(this);
        }
    }
- member를 조회하고 team객체의 클래스를 확인해보면 proxy객체가 조회된다.
- ``` java
    Team team = new Team();
    team.setName("teamA");
    em.persist(team);
    ​
    Member member = new Member();
    member.setUsername("memberA");
    em.persist(member);
    ​
    member.changeTeam(team);
    ​
    em.flush();
    em.clear();
    ​
    Member findMember = em.find(Member.class, member.getId());
    ​
    System.out.println(findMember.getTeam().getClass());
- ``` java
    Hibernate: 
        select
            member0_.id as id1_4_0_,
            member0_.createdBy as createdB2_4_0_,
            member0_.createdDate as createdD3_4_0_,
            member0_.lastModifiedBy as lastModi4_4_0_,
            member0_.lastModifiedDate as lastModi5_4_0_,
            member0_.age as age6_4_0_,
            member0_.description as descript7_4_0_,
            member0_.locker_id as locker_10_4_0_,
            member0_.roleType as roleType8_4_0_,
            member0_.team_id as team_id11_4_0_,
            member0_.name as name9_4_0_,
            locker1_.id as id1_3_1_,
            locker1_.name as name2_3_1_ 
        from
            Member member0_ 
        left outer join
            Locker locker1_ 
                on member0_.locker_id=locker1_.id 
        where
            member0_.id=?

    class hello.jpa.Team$HibernateProxy$e97rdqZR  // 프록시 객체
- 이때 팀이름을 출력하면 팀 객체의 조회가 필요한 시점에 쿼리가 나간다.
- ``` java
   System.out.println("TEAM NAME : " + findMember.getTeam().getName());
- ``` java
  Hibernate: 
    select
        team0_.id as id1_8_0_,
        team0_.createdBy as createdB2_8_0_,
        team0_.createdDate as createdD3_8_0_,
        team0_.lastModifiedBy as lastModi4_8_0_,
        team0_.lastModifiedDate as lastModi5_8_0_,
        team0_.name as name6_8_0_ 
    from
        Team team0_ 
    where
        team0_.id=?
        
    TEAM NAME : teamA
## 지연로딩
![](https://github.com/namjunemy/TIL/blob/master/Jpa/inflearn/img/32_lazy.PNG?raw=true)
- 내부 매커니즘은 위의 그림과 같다.
- 로딩되는 시점에LAZY로딩 설정이 되어있는 Team 엔티티는 프록시 객체로 가져온다.
- 후에 실제 객체를 사용하는 시점에(Team을 사용하는 시점에)초기화가 된다. DB에 쿼리가 나간다.
  - getTeam()으로 Team을 조회하면 프록시 객체가 조회된다.
  - getTeam().getXXX()로 팀의 필드에 접근 할 때 쿼리가 나간다.
- __대부분 비즈니스 로직에서 member와Team을 같이 사용한다면?__
  - 이런경우 LAZY로딩을 사용한다면 select쿼리가 따로따로 2번 나간다.
  - 네트워크를 2번 타서 조회가 이루어진다는 소리다. 손해!
  - 이떄는 즉시로딩(EAGER) 전략을 사용해서 함께 조회하면 된다.
  

## 즉시로딩(EAGER)
- fetch 타입을 EAGER로 설정하면 된다.
- 대부분 JPA구현체는 가능하면 조인을 사용해서 SQL을 한번에 함꼐 조회하려 한다.
- 이렇게 하면, 실제 조회할 때 한방 쿼리로 다 조회해온다.(실제 TEAM을 사용할떄 쿼리가 안나가도 된다.)
- 실행 결과를 보면 TEAM객체도 프록시 객체가 아니라 실제 객체이다.
- ``` java
    System.out.println(findMember.getTeam().getClass());
- ``` java
  class hello.jpa.Team
- __프록시와 즉시로딩 주의할 점__
  - 실무에서는 가극적 지연로딩만 사용한다. 즉시로딩 쓰지말자.
    - JPA구현체도 한번에 가져오려고 하고 한번에 가져와서 쓰면 좋지 않나?
  - 즉시 로딩을 적용하면 예상하지 못한SQL이 발생한다.
    - @ManyToOne이 5개 있는데 전부 EAGER로 설정되어 있다고 생각해보자.
    - 조인이 5개 일어난다. 실무에선 테이블이 더 많다.
  - 즉시로딩은 JPQL에서 N+1 문제를 일으킨다.
    - 실무에서 복잡한 쿼리를 많이 풀어내기 위해서 JPQL을 많이 사용한다.
    - em.find는 PK를 정해놓고 DB에서 가져오기 떄문에 JPA내부에서 최적화 할 수 있다.
    - 하지만 JPQL에선 입력받는 query string이 그대로 SQL로 변환된다.
      - "select m from Member m"이 문장은 당연히 Member만 select하게 뇌다.
      - MEmber를 쭉 다 가져와서 보니까 EAGER네? LAZY면 프록시를 넣으면 되겠지만 EAGER는 반환하느 시점에 다 조회가 되어있어야 한다.
      - 따라서  Member를 다 가져오고나서 그 Member와 연관된 TEAM을 다시 다 가져온다.
- __코드로 이해하기__
- ``` java
    Team team1 = new Team();
    team1.setName("teamA");
    em.persist(team1);
    ​
    Team team2 = new Team();
    team2.setName("teamB");
    em.persist(team2);
    ​
    Member member1 = new Member();
    member1.setUsername("memberA");
    em.persist(member1);
    member1.changeTeam(team1);
    ​
    Member member2 = new Member();
    member2.setUsername("memberB");
    em.persist(member2);
    member2.changeTeam(team2);
    ​
    em.flush();
    em.clear();
    ​
    List<Member> members = em
                    .createQuery("select m from Member m", Member.class)
      .getResultList();
    ​
    tx.commit();
    ```
    - 실행결과를 보면 일단 멤버를 조회해서 가져온다. 그리고 나서 Member들의 Team이 비어있으니까 채워서 반환시키기 위해서 Team을 각각 쿼리를 날려서 가져온다.
    - N + 1의 문제의 의미는 아래 처럼 쿼리를 1개 날렸는데, 그것 때문에 추가 쿼리가 N개 나간다는 의미이다.
- ``` java
  Hibernate: 
    /* select
        m 
    from
        Member m */ select
            member0_.id as id1_4_,
            member0_.createdBy as createdB2_4_,
            member0_.createdDate as createdD3_4_,
            member0_.lastModifiedBy as lastModi4_4_,
            member0_.lastModifiedDate as lastModi5_4_,
            member0_.age as age6_4_,
            member0_.description as descript7_4_,
            member0_.locker_id as locker_10_4_,
            member0_.roleType as roleType8_4_,
            member0_.team_id as team_id11_4_,
            member0_.name as name9_4_ 
        from
            Member member0_
    Hibernate: 
        select
            team0_.id as id1_8_0_,
            team0_.createdBy as createdB2_8_0_,
            team0_.createdDate as createdD3_8_0_,
            team0_.lastModifiedBy as lastModi4_8_0_,
            team0_.lastModifiedDate as lastModi5_8_0_,
            team0_.name as name6_8_0_ 
        from
            Team team0_ 
        where
            team0_.id=?
    Hibernate: 
        select
            team0_.id as id1_8_0_,
            team0_.createdBy as createdB2_8_0_,
            team0_.createdDate as createdD3_8_0_,
            team0_.lastModifiedBy as lastModi4_8_0_,
            team0_.lastModifiedDate as lastModi5_8_0_,
            team0_.name as name6_8_0_ 
        from
            Team team0_ 
        where
            team0_.id=?
    ```
- 결론: 실무에서는 LAZY로딩 전략을 가져가자
  - 근데 실무에서 대부분 멤버 팀을 함꼐 사용하는데 LAZY로 해놓고 계속 쿼리 두방을 날려야하나?
  - 이런 경우는 JPQL의 fetch Join을 통해서 해당 시점에 한방 쿼리로 가져와서 쓸 수 있다.
  - 추가적으로 엔티티그래프와 어노테이션을 푸는방법 배치 사이즈 설정으로 해경하는 방법이 있다.
  - 대부분 fetch join으로 해결한다.
- @ManyToOne @OneToOne @XXXToOne 어노테이션들은 기본이 EAGER이다.
- @OneToMany @ManyToMany는 기본이 지연(LAZY)로딩이다



# 참고
- [https:y.com/130//ict-nroo.tistor](https://ict-nroo.tistory.com/132?category=826875)