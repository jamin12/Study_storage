# Graphql
- gql은 sql과 마찬가지로 쿼리 언어이다. 하지만 이 둘의 언어적 구조 차이는 매우 크고 실전에서 쓰이는 방식의 차이도 매우 크다.
## SQL
- 데이터베이스 시스템에 저장된 데이터를 효율적으로 가져오는 것이 목적이다.
## GQL
- gql: 웹 클라이언트가 데이터를 서버로부터 효율적으로 가져오는것이 목적이다.
- 서버사이드 gql 어플리케이션은 gql로 작성된 쿼리를 입력으로 받아 쿼리를 처리한 결과를 다시 클아이언트로 돌려준다.
- 어떤 특정 데이터베이스타 플렛폼에 종속적이지 않다. 심지어네트워크 방식에도 종속적이지 않다. 일반적으로 gql의 인터페이스간 송수신으 네트워크 레이어 L7의 Http POST메서드와 웹소켓 프로토콜을 활용한다. 필요에 따라서는 L4의 TCP/UDP를 활용하거나 L2형식의 이더넷 프레임을 활용할 수도 있다.
- ![](http://tech.kakao.com/files/graphql-pipeline.png)
## REST API와 비교
- REST API는 URL, METHOD등을 조합하기 떄문에 다양한 EndPoint가 존재한다. 반면, gql은 단 하나의 endpoint가 존재한다.
- gql API에서는 불러오는 데이터의 종류를 쿼리 조합을 통해서 결정한다.
- REST API에서는 각 EndPoint마다 데이터베이스 SQL쿼리가 달라지는 반면, gql API는 gql 스키마의 타입마다 데이터베이스 SQL쿼리가 달라진다.
- ![Alt text](http://tech.kakao.com/files/graphql-stack.png)
- ![Alt text](http://tech.kakao.com/files/graphql-mobile-api.png)
## 쿼리 뮤테이션
- 요청하는 쿼리문의 구조와 응답 내요의 구조는 거의 일치한다.(gql의 핵심 항상 기대 한 결과를 얻을 수 있다.)
- ![Alt text](http://tech.kakao.com/files/graphql-example.png)
- gql에서는 굳이 쿼리랑 뮤테이션을 나누는데 내부적으로 들어가면 사실항 이 둘은 별 차이가 없다.
- 쿼리는(R)읽는데 사용하고, 뮤테이션은 데이터를 변조(CUD)하는데 사용한다는 개졈적인 규약이다.
## 스키마/타입
- c, c++의 헤더파일 작성에 비유가 된다.
- ``` gql
    type Character {
        name: String!
        appearsIn: [Episode!]!
    }
    ```
  - 오브젝트 타입 : Character
  - 필드 : name, appearsIn
  - 스칼라 타입 : String, ID, Int 등
  - 느낌표(!) : 필수 값을 의미(non-nullable)
  - 대괄호([, ]) : 배열을 의미(array)
## 리졸버(resolver)
- gql에서는 데이터를 가져오는 구체적인 과정을 직접 구현해야 한다. 이 과정은 리졸버가 담당한다.
- 이를 통해 source의 종류에 상관없이 구현이 가능하다.
- 데이터베이스, 일반파일, http, SOAP 등에서 원격 데이터를 가져올 수 있다.
- gql쿼리에서는 각각 필드마다 함수가 하나씩 존재한다고 생각하면 된다. 만약 필드가 스칼라 값(문자열이나 숫자와 같은)인 경우에는 샐힝이 종료 된다.
- ``` ts
  type Query {
    users: [User]
    user(id: ID): User
    limits: [Limit]
    limit(UserId: ID): Limit
    paymentsByUser(userId: ID): [Payment]
  }

  type User {
    id: ID!
    name: String!
    sex: SEX!
    birthDay: String!
    phoneNumber: String!
  }

  type Limit {
    id: ID!
    UserId: ID
    max: Int!
    amount: Int
    user: User
  }

  type Payment {
    id: ID!
    limit: Limit!
    user: User!
    pg: PaymentGateway!
    productName: String!
    amount: Int!
    ref: String
    createdAt: String!
    updatedAt: String!
  }
# 참고
- https://tech.kakao.com/2019/08/01/graphql-basic/
- https://graphql.org/learn/thinking-in-graphs/
- https://graphql-kr.github.io/learn/queries/