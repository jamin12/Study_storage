# hoisting
## __<span style="color:#9999ff">정의</span>__
- 인터프리터가 병수와 함수의 메모리 공간을 선언 전에 미리 할당하는 것을 의미한다.
- var로 선언한 변수의 경우 호이스팅 시 undefined로 변수를 초기화한다. 반면 let과 const로 선언한 변수의 겨웅 호이스팅 시 변수를 초기화하지 않는다.
## __<span style="color:#9999ff">예시</span>__
- ``` js
    catName("클로이");

    function catName(name) {
        console.log("제 고양이의 이름은 " + name + "입니다");
    }

    /*
    결과: "제 고양이의 이름은 클로이입니다"
    */

    function getX() {
        console.log(x); // undefined
        var x = 100;
        console.log(x); // 100
    }
    getX();
- 함수의 코드를 실행하기 전에 함수 선언에 대한 메모리부터 할당한다. 그래서 함수를 호출하는 코드를 함수 선언보다 앞서 배치할 수 있다.
# 참고
- https://developer.mozilla.org/ko/docs/Glossary/Hoisting
- https://github.com/JaeYeopHan/Interview_Question_for_Beginner/tree/master/JavaScript#hoisting