# jS 동작원리
![](https://ingg.dev/213a51586e94904c5075045d2c94273b/work22.svg)
- __<span style="color:#14a492">Memory Heap</span>__
  - 메모리 할당이 일어나는 곳
  - Heap
    - 구조화되지 않은 넓은 메모리 영역
    - 객체(변수, 함수 등)들이 담긴다.
- __<span style="color:#14a492">Call Stack(호출 스택)</span>__
  - 실행될 코드의 한 줄 단위로 할당되어 실행되는 구조
- __<span style="color:#14a492">Web API(노드에서는 백그라운드로 설명된다.)</span>__
  - 비동기 처리를 담당한다.
- __<span style="color:#14a492">Callback Queue(Task Queue, Event Queue등 다양한 형태로 설명된다.)</span>__
  - 비동기 처리가 끝난 후 실행되어야 할 콜백 함수가 차례로 할당된다.
- __<span style="color:#14a492">Event Loop</span>__
  - Queue에 할당된 함수를 순서에 맞춰 Call Stack에 할당해준다.

## __<span style="color:#ff9933">동기적인 코드</span>__
- ``` js 
    function first(){
        second();
        console.log("첫번째");
    }
    function second(){
        third();
        console.log("두번째");
    }
    function third(){
        console.log("세번째");
    }
    first();
    ```
- ![](https://ingg.dev/static/6fa1949bb1f3a44c2d675de396cac1db/fcda8/work33.png)
  - main() 함수는 처음 실행시 전역 컨텍스트이다. 함수의 실행이 완료되면 호출 스택에서 지워진다.

## __<span style="color:#ff9933">비동기적인 코드</span>__
- ``` js
    console.log("시작");

    setTimeout(function(){
        console.log("3초후 실행");
    }, 3000);

    console.log("끝");
    ```
1. 먼저 전역 컨텍스트 main()함수가 Call Stack에 쌓이고 con
   - ![](https://ingg.dev/94937960d2a903fe1a201fd4f62fa5b2/t1-1.svg)
2. console.log("시작");이 리턴되며 Call Stack에서 제거된다.
   - ![](https://ingg.dev/07aa3d7067d011d2a4410a9b44f79c1b/t2.svg)
3. setTimeout함수가 실행되면서 Call Stack에 SetTimeout함수가 들어간다.
   - ![](https://ingg.dev/7964d286f416a654abf4f0cb6050acf3/t3.svg)
4. setTimeout함수는 자바스크립트 엔진을 처리하지않고 Web API가 처리하므로 Callback함수를 전달하고 setTimeout 작업을 요청한다.
   - ![](https://ingg.dev/3f51d2e2c589d249f4fbf1d6f04de942/t4.svg)
5. Call Stack에서는 setTimeout작업이 제거된다.
   - ![](https://ingg.dev/a31a72a921fd00ce3748387d3cc6a874/t5.svg)
6. console.log('끝')이 호출되어 callStack에 쌓인다. "끝"이 콘솔에 찍힌다.
   - ![](https://ingg.dev/30f9ce7590c10216e3a5d90d1b3a2eb0/t6.svg)
7. console.log('끝')이 리턴되며 callStack에서 제거된다.
   - ![](https://ingg.dev/a31a72a921fd00ce3748387d3cc6a874/t5.svg)
8. main() 함수가 리턴되며 Call Stack에서 제거된다.
   - ![](https://ingg.dev/d87e5f246afdbd3b9a7e6479fddfaac6/t7.svg)
9. web API는 setTimeout작업을 실행한다.(Web API로 넘어간 순간부터 초가 실행 되고 있음). 3초를 센 후 Task Queue로 Callback함수를보낸다.
   - ![](https://ingg.dev/5df05bad5c903f4fa6481dfd94089289/t8.svg)
10. Event Loop는 Call Stack이 비어있으면 Task Queue에서 함수를 하나씩 꺼내 Call Stack에 넣고 실행한다.
    - ![](https://ingg.dev/528d7c3c864b1ca812422bc6359b2214/t9.svg)
11. console.log("3초후 실행")이 호출되고 Call Stack에 쌓인다.
    - ![](https://ingg.dev/a16b84bd9d2fb00f89c8dedc9039dbbb/t10.svg)
12. console.log("3초후 실행")이 리턴되고 callStack에서 제거된다. 차례로 Callback함수도 제거된다. Event loop는 Task Queue에 콜백 함수가 들어올 때까지 계속 대키한다.
- 만약 Call Stack에 많은 함수들이 있으면 3초후에 실행되지 않을 수도 있다. Event loop는 Call Stack이 비어있을 때만 Task Queue의 함수를 Call Stack으로 가져오기 때문이다. 
- __<span style="color:#9999ff">심화</span>__
  - 
  - ``` js
        Promise.resolve()
        .then(function(){
            console.log('Promise resolved');
        });
  - 프로미스는 동기이지만 then을 만나면 비동기처럼 작동해 Web API로 넘어가게 된다.
  - ![](https://velog.velcdn.com/images%2Ftitu%2Fpost%2F0eeea3c2-986e-408d-ae56-52dad1143811%2Fimage.png)
  - 이벤트 루프가 비동기 작업을 처리하는 우선순위는 MicroTask Queue > Animation Frames > Task Queue 순이다.
  - 또한 이벤트 루프는 MicroTask Queue나 Animation Frames를 방문할 때는 큐 안에있는 모든 작업들을 수행하지만 Task Queue를 방문할 때는 한번에 하나의 작업만 Call stack으로 전달하고 Queue를 순회한다.
# 참고
- https://ingg.dev/js-work/
- https://www.youtube.com/watch?v=QFHyPInNhbo&ab_channel=%EC%9A%B0%EB%A6%AC%EB%B0%8B_woorimIT
- https://www.youtube.com/watch?v=S1bVARd2OSE&t=105s&ab_channel=%EC%9A%B0%EB%A6%AC%EB%B0%8B_woorimIT
- https://velog.io/@titu/JavaScript-Task-Queue%EB%A7%90%EA%B3%A0-%EB%8B%A4%EB%A5%B8-%ED%81%90%EA%B0%80-%EB%8D%94-%EC%9E%88%EB%8B%A4%EA%B3%A0-MicroTask-Queue-Animation-Frames-Render-Queue