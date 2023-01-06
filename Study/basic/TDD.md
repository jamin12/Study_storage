# TDD
## TDD란
- TDD란 Test Driven Development의 약자로 테스트 주도 개발이라고 한다. 
- 반복 테스트를 이용한 소프트웨어 방법론으로 작은 단위의 테스트 케이스를 작성하고 이를 통과하는 코드를 추가하는 단계를 반복하여 구현하낟.
- 짧은 개발주기의 의존하는 개발 프로세스이며 애자일 방법론중 하나인 "eXtream Programming(XP)"dml Test-First개념에 기반을 둔 단순한 설계를 중요시한다.
- **TDD는 ProductionCode를 구현하기 전에 단위 테스트 코드를 먼저 추가하는 것이다.**
- **TDD는 테스트 코드를 무조건 ProdctionCode보다 먼저 구현해야 하는 원칙이 있다.**
1. ### 단위 테스트
   - 말 그대로 한 단위(일반적으로 class)만을 테스트 하는 것이다.
## TDD 개발 주기
- ![](https://i0.wp.com/hanamon.kr/wp-content/uploads/2021/04/TDD-%E1%84%80%E1%85%A2%E1%84%87%E1%85%A1%E1%86%AF%E1%84%8C%E1%85%AE%E1%84%80%E1%85%B5.png?w=1024&ssl=1)
- `Red`: 실패하는 테스트 코드를 먼저 작성한다.
- `Greem`: 테스트 코드를 성공시키기 위한 실제 코드를 작성한다.
- `Blue`: 단계에서는 중복 코드를 제거, 일반화 등의 리팩토링을 수행하다.
## TDD 개발 방식
- 디자인 단계에서 프로그래밍 목적을 반드시 미리 정의해야만 하고 무엇보다 테스트 해야 할지 미리 정의해야만 한다.
- TDD를 잘하기 위해서는 욕사항 분석을 잘해야 하고 정리가 잘되있어야 한다.
- 테스트 코드를 작성하는 도중 발생하는 버그는 테스트 케이스에 추가하고 설계를 개선한다.
- 이후 테스트가 통과된 코드만을 개발 단계에서 실제 코드로 작성한다.
- ![](https://i0.wp.com/hanamon.kr/wp-content/uploads/2021/04/TDD%E1%84%91%E1%85%B3%E1%84%85%E1%85%A9%E1%84%89%E1%85%A6%E1%84%89%E1%85%B3.png?w=528&ssl=1)
    > 이러한 반복적인 단계가 진행되면서 자연스럽게 코드의 버그가 줄어들고 소스코드는 간결해진다.
## TDD장점
1. ### 보다 튼튼한 객체 지향적인 코드 생산
   - TDD는 코드의 재사용 보장을 명시하므로 TDD를 통한 소프트웨어 개발 시 기능 별 철저한 모듈화가 이뤄진다.
   - 이는 종속성과 의존성이 낮은 모듈로 조합된 소프트웨어 개발을 가능하게 하며 필요에 따라 모듈을 추가하거나 제거해도 소프트웨어 전체 구조에 영향을 미치치 않게 된다.
2. ### 재설계 시간의 단축
   - 테스트 코드를 먼저 작성하기 때문에 개발자가 지금 무엇을 해야하는지 분명히 정의하고 개발을 시작하게된다. 또한 테스트 시나리오를 작성하면서 다양한 예외사항에 대해 생각해 볼 수 있다. 이는 개발 진행 중 소프트웨어의 전반적인 설계가 변경되는 일을 방지할 수 있다.
3. ### 디버깅 시간의 단축
   - 이는 유닛 테스팅을 하는 이점이기도하다. 예를 들면 사용자의 데이터가 잘못 나온다면 DB의 문제인지, 비즈니스 레이어의 문제인지 UI의 문제인지 실제 모든 레이어들을 전부 디버깅 해야하지만, TDD의 경우 자동화 된 유닛 테스팅을 전제하므로 특정 버그를 손 쉰게 찾아낼 수 있다.
4. ### 테스트 문서의 대체 가능
   - 주로 SI 프로젝트 진행 과정에서 어떤 요소들이 테스트 되었는지 테스트 정의서를 만든다. 이것은 단순 통합 테스트문서에 지나지 않는다. 하지만 TDD를 하게 될 경우 테스팅을 자동화 시킴과 동시에 보다 정확한 테스트 근거를 산출 할 수 있다.
5. ### 추가 구현의 용의함
   - 개발이 완료된 소프트웨어에 어떤 기능을 추가할 때 가장 우려되는 점은 해당 기능이 기존 코드에 어떤 영향을 미칠지 알지 못한다는 것이다. 하지만 TDD의 경우 자동화된 유닛 테스팅을 전제하므로 테스트 기간을 획기적으로 단축시킬 수 있다.

## TDD단점
1. ### 생산성 저하이다.
   - 2개의 코드를 짜야하고 중간중간 테스트를 하면서 고쳐나가야하기 때문이다.
   - TDD 방식의 개발 시간은 일반적인 개발 방식에 비해 대략 10~30% 정도로 늘어난다.

## 정리
- 실패하는 단위 테스트를 작성할 때까지 프로덕션 코드(production code)를 작성하지 않는다.
- 컴파일은 실패하지 않으면서 실행이 실패하는 정도로만 단위 테스트를 작성한다.
- 현재 실패하는 테스트를 통과할 정도로만 실제 코드를 작성한다.

# 참고
- https://velog.io/@kyu/TDDTest-Driven-Development
- https://hanamon.kr/tdd%EB%9E%80-%ED%85%8C%EC%8A%A4%ED%8A%B8-%EC%A3%BC%EB%8F%84-%EA%B0%9C%EB%B0%9C/