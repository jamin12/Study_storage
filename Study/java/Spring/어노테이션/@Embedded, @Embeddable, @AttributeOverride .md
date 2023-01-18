# @Embedded, @Embeddable, @AttributeOverride
## 임베드 타입(복합 값 타입)
- 엔티티로 사용할 목적이 아닌 값 타입으로 사용하기 위해 만든것이다.
- 임베디드 타입도 in, String과 같은 값 타입이다.
- ``` java
    private String zipCode;
    private String address1;
    private String address2;
- 위의 데이터는 모두 주소를 표현하는 필드이다. 하지만 위와 같은 경우 조소로 인식하기에는 어려움이 있고 필드가 많아지면 더더욱 지저분하게 될 것이다.
## @Embedded & @Embeddable
- 임베디드 타입을 필드로 사용하려면 필드 위에 @Embedded를 붙인다.
- 그리고 값 타입으로 사용되는 클래스에는 @Embeddable을 붙인다.
- **이때 값 타입 클래스에는 기본 생성자가 필수로 존재야하 한다.**
- ``` java
    @Embeddable
    public class Address {
        private String zipCode;
        private String address1;
        private String address2;
    }
    @Entity
    @Table(name = "user")
    public class User {
        @Id
        @GeneratedValue(strategy = GenerationType.AUTO)
        private Long uid;
    ​
        private String name;
    ​
        private String phoneNum;
    ​
        @Embedded
        private Address address;
    }
- 임베디드 타입을 사용하지한ㄴㅎ고 zipCode, address1 ...을 User에서 그냥 사용할 때와 테이블 구조는 똑같다.
1. ### 임베디드 타입과 연관관계
   - 다른 엔티티 혹은 값 타입과 연관관계를 맺을 수 있다.
   - ``` java 
      @Embeddable
      public class Address {

          private String city;
          private String street;

          @Embedded
          private Zipcode zipcode;
      }
      @Embeddable
      public class Zipcode {
          private String zip;
          private String plusFour;
      }
      ///////////////////////////////////////////////////
      @Embeddable
      public class Phone {

          private String areaCode;
          private String number;

          @ManyToOne
          @JoinColumn(name = "PHONE_PROVIDER")
          private PhoneServiceProvider provider;
      }
      @Getter
      @NoArgsConstructor(access = AccessLevel.PROTECTED)
      @Entity
      public class PhoneServiceProvider {

          @Id @GeneratedValue
          private Long id;

          private String name;
      }
## AttributeOverride
- 속성 재정의
- ``` java
    @Embedded
    @AttributeOverrides({
            @AttributeOverride(name = "city", column = @Column(name = "HOME_CITY")),
            @AttributeOverride(name = "street", column = @Column(name = "HOME_STREET")),
            @AttributeOverride(name = "zipcode.zip", column = @Column(name = "HOME_ZIP")),
            @AttributeOverride(name = "zipcode.plusFour", column = @Column(name = "HOME_PLUS_FOUR")),
    })
    private Address homeAddress;


    @Embedded
    @AttributeOverrides({
            @AttributeOverride(name = "city", column = @Column(name = "COMPANY_CITY")),
            @AttributeOverride(name = "street", column = @Column(name = "COMPANY_STREET")),
            @AttributeOverride(name = "zipcode.zip", column = @Column(name = "COMPANY_ZIP")),
            @AttributeOverride(name = "zipcode.plusFour", column = @Column(name = "COMPANY_PLUS_FOUR")),
    })
    private Address companyAddress;

# 참고
- https://galid1.tistory.com/592
- https://ttl-blog.tistory.com/133