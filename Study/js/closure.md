# closure
## __<span style="color:#9999ff">정의</span>__
- 함수가 속한 스코프를 기억하여 해당 함수가 스코프 밖에서 실행될 때에도 당시의 스코프에 접근할 수 있게 하는 기능이다.
- 외부 함수가 내부 함수 자체를 실행하지 않고 return 해주고, 다른 스코프에서 해당return된 함수를 실행할 때, return된 함수는 선언 당시 스코프 체인들을 참조하여 해당 스코프에 접근할 수 있다.
## __<span style="color:#9999ff">예시</span>__
- ``` js
    function add(){
        var a = 1; // 변수 a는 add라는 외부 함수의 Scope 내에 존재하게 된다.
        return (function(){ 
        var b = 0; // 변수 b는 익명 내부 함수의 Scope 내에 존재하게 된다.
        console.log(a+b)
        })
    }
    const result = add();
    result(); // 1
- result변수안에 담겨져 있는 함수가 add함수 스코프와 체인이 걸려있는 체로 실행되지 않고 계속 참조상태이기 때문에 add 함수가 실행되어도 해당 스코프는 소멸되지 않고 유지하고 있다.
## __<span style="color:#9999ff">장점</span>__
- __<span style="color:#ff9933">데이터를 보존할 수 있다.</span>__
  - 클로저 함수는 외부 함수의 실행이 끝나더라도 외부 함수 내 변수를 사용할 수 있다.
  - 클로저는 이처럼 특정 데이터를 스코프안에 가두어 둔 채로 계속 사용할 수 있게하는 폐쇄성을 갖는다.
- __<span style="color:#ff9933">정보의 접근 제한</span>__
  - 클로저 모듈 패턴을 사용해 객체에 담아 여러 개의 함수를 리턴하도록 만든다.(캡슐화)
- __<span style="color:#ff9933">모듈화에 유리하다.</span>__
  - 클로저 함수를 각각의 변수에 할당하면각자 독립적으로 값을 사용하고 보존할 수 있다.
  - 이와 같이 함수의 재사용성을 극대화 함수하나를 독립적인 부품의 형태로 분리하는 것을 모듈화라고 한다.
# 참고 
- https://medium.com/@qhdrbs1341/node-js%EB%A5%BC-%EC%9E%98-%EC%93%B0%EA%B3%A0-%EC%9E%88%EB%8A%94-%EA%B1%B4%EA%B0%80-3-ea3f957e6b08
- https://hanamon.kr/javascript-%ED%81%B4%EB%A1%9C%EC%A0%80/