# 기본개념
## ORM이란?
- objectRelational mapping(객체와 관계형 데이터베이스 매핑, 객체와 DB의 테이블이 매핑을 이루는 것)
- 객체가 테이블이 되도록 매핑 시켜주는 프레임워크이다.
- 프로그램의 복잡도를 줄이고 자바 객체와 쿼리를 분리할 수 있으며 트랜잭션 처리나 기타 데이터베이스 관련 작업들을 좀 더 편리하게 처리할 수 있는 방법
- SQL Query가 아닌 직관적인 코드로서 데이터를 조작할 수 있다.

## JPA란?
- java persistence API(자바 ORM 기술에 대한 API 표준명세)
- 한마디로 ORM을 사용하기 위한 인터페이스를 모아둔 것 이라고 볼 수 있다.
- 자바 어플리케이션에서 관계형 데이터베이스를 사용하는 방식을 정의한 인터페이스이다.
- ORM에 대한 잡 API 규격이면 hibernate, openJPA등이 JPA를 구현한 구현체이다.(ORM을 사용하기 위한 인터페이스를 모아둔 것)
- hibernate이외에도 openJPA, TOPLINK등이 있다.

## Hibernate
- JPA를 사용하기 위해서 JPA를 구현한 ORM프레임워크중 하나.
- hibernate는 JPA명세의 구현체이다.javax.persistence.EntityManager와 같은 JPA의 인터페이스를 직접 구현한 라이브러리이다
- ![구현체..](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FbUHeS3%2FbtqCaRN7gVh%2F0ZQRYe0CF6tQeD3d9d8ITk%2Fimg.png)

## Spring Data JPA
- Spring Data JPA의 핵심은 Repository이다.
- spring에서 제공해주는 모듈 중 하나로, 개발자가 JPA를 더 쉽고 편하게 사용할 수 있도록 도와준다.
- JPA를 한 단께 추상화 시킨 Repository라는 인터페이스를 제공함으로써 이루어진다.
- 사용자가 Repository 인터페이스에 정해진 규칙대로 메소드를 입력하면 Spring이 알아서 메소드 이름에 적합한 쿼리를 날리는 구현체를 만들어서 Bean으로 등록해준다.
  
- ![Alt text](https://suhwan.dev/images/jpa_hibernate_repository/overall_design.png)
# 참고
- https://suhwan.dev/2019/02/24/jpa-vs-hibernate-vs-spring-data-jpa/