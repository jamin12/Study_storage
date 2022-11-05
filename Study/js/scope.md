# Scope
## __<span style="color:#9999ff">정의</span>__
- 어디서 어떻게 변수를 찾는가를 결정하는 규칙의 집합 혹은 **변수 유효 범위**라고 한다.
## __<span style="color:#9999ff">분류</span>__
- __<span style="color:#ff9933">함수 기반 scope</span>__
  - 전통적인 js에는 함수 스코프와 전역 스코프 두 가지만 존재했다. var로 선언한 변수는 함수 내부 또는 외부에서 선언되었는지에 따라 함수 스코프 또는 전역 스코프를 가진다.
  - ``` js
        function add(){
            var a = 1; // 변수 a는 add라는 외부 함수의 Scope 내에 존재하게 된다.
            (function(){
            var b = 0; // 변수 b는 익명 IIFE 내부 함수의 Scope 내에 존재하게 된다.
            console.log(a+b)})(); // 1
        }
## __<span style="color:#ff9933">블록 기반 scope</span>__
- es6에서 js는 블록 스코프 변수를 생성할 수 있도록 let과 const 선언과 함께 시간상 사각지대등을 도입했다.
  - 시간상 사각지대 : let 변수는 초기화하기 전에 읽거나 쓸 수 없습니다. </br> var의 경우 선언 전에 접근할 시 undefined이다.
  - ``` js
      function do_something() {
          console.log(bar); // undefined
          console.log(foo); // ReferenceError
          var bar = 1;
          let foo = 2;
      }
- ``` js
    {
    let innerValue = 3;
    }
    console.log(innerValue) // innerValue is not defined
- let은 블록 기반 스코프 내에 선언되므로 전역 객체 내 해당 블록 스코프에 선언된다.
# 참고
- https://medium.com/@qhdrbs1341/node-js%EB%A5%BC-%EC%9E%98-%EC%93%B0%EA%B3%A0-%EC%9E%88%EB%8A%94-%EA%B1%B4%EA%B0%80-3-ea3f957e6b08
- https://developer.mozilla.org/ko/docs/Web/JavaScript/Closures