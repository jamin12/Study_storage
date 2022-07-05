# 콜백 함수
## __<span style=color:#9999ff>콜백 함수가 탄생한 배경</span>__
1. 자바스크립트의 비동기 처리방식
   - 자바스크립트는 기본적으로 이벤트가 중심이 되는 언어이다.</br>이벤트는 즉각적이고 동적이 때문에 일일히 다른코드를 기다려줄 수 없다.</br>그래서 자바스크립트에서는 비동기 처리방식이 채택하여 사용하고 있다.
   - **동기처리**는 코드가 1,2,3이 있을때 1이 종료되면 2실행 2가 종료되면 3 이런식으로 진행되는 반면 **비동기처리**는 1이 종료되지 않아도 2실행 2가 종료되지 않아도 3이 실행되는 방식이다.
2. 비동기 처리방식의 문제
   - 여러코드가 예측하기 어렵게 실행된다.
   - 예시로 
   - ``` js
        // 1번 
        function a() {   
            setTimeout(function() {   
                console.log('a');  
                }, 5000)
            }  
        // 2번 
        function b() {  
            console.log('b'); 
        }  
        a();
        b();
   - 여기서 내가 원하는 코드는 5초뒤 a,b를 출력하는것이다. 하지만 이 코드는 b,a이다.
   - js는 1번에서 5초를 기다리지 않고 2번부터 실행한뒤 5초가 지난후 1번을 실행한다.
3. 콜백 함수의 등장
   - 비동기 방식의 문제로 등장했다고 하니, 당연히 비동기의 반대가 될 것이다.
   - 즉, 콜백함수는 코드를 통해 명시적으로 호출하는 함수가 아니라, 개발자는 단지 함수를 등록하기만 하고, **어떤 이벤트가 발생했거나 특정 시점에 도달했을 때 시스템에서 호출하는 함수를 말한다.**
   - 그리고 콜백함수의 사용법은 "**다른 함수의 매개변수로 함수를 전달하는 방식**"이라는 것이다.
## __<span style="color:#9999ff">콜백함수 흐름</span>__
- ```js
    // 1번 
    function a(callback) {   
        setTimeout(function() {   
            console.log('a');    
            callback();  
            }, 5000) 
        }  
        // 2번 
    function b() {  
        console.log('b'); 
    }  
    a( function() {     
        b(); 
    });
- 우선 평범하게 a(), b() 함수가 선언된다.
- a( callback ) 부분에서 callback안에 함수 b를 실행하라는 함수가 매개변수로 들어간다.
- 1번이 실행된다. a(callback)안에는 (2)의 함수인 매개변수가 들어간다.
- setTime~에 들어가면 우선 5초를 기다린 뒤 a가 콘솔에 찍힌다.
- 그 뒤 callback()을 만나면 넘겨받은 함수인 b()를 실행하여 b가 콘솔에 찍힌다. 


# 참고
- https://yulfsong.tistory.com/89
- https://www.hanumoka.net/2018/10/24/javascript-20181024-javascript-callback/