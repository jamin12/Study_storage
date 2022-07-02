# Promise
- 프로미스는 자바스크립트 비동기 처리에 사용되는 객체이다.
## __<span style="color:#9999ff">기본 사용법</span>__
- 우선 promise로 관리할 비동기 작업을 만들 때에는 promise에서 요구하는 방법대로 만들어야 한다.
- 여러가지 방법이 있지만 제일 정석적인 방법은 new Promise(...)하는 것이다.
- ``` js
    const promise1 = new Promise((resolve, reject) => {
    // 비동기 작업
    }); 
- promise의 생성자는 특별한 함수하나(**excutor**)를 인자로 받는다.
- excutor
  - excutor의 첫번째 인수로 resolve이며, 두번째 인수로 reject를 받는다
  - resolve는 excutor내에서 호출할 수 있는 또 다른 함수이다. resolve를 호출하게 된다면 "이 비동기 작업이 성공했어! 라는 뜻이다.
  - reject또한 excutor내에서 호출할 수 있는 또 다른 함수이다. reject를 호출하게 된다면 "이 비동기 작업이 실패했어" 라는 뜻이다.
- Promise의 특징은 new Promise(...)하는 순간 여기에 할당된 비동기 작업은 바로 시작된다.
- 이 이후 작업이 성공하거나 실패하고 난 다음의 then메소드와 catch메소드로 동작을 설정할 수 있다.
  - **then** 메소드는 해당 promise가 성공했을 때 동작을 지정한다. 인자로 함수를 받는다
  - **catch** 메소드는 해당 promise가 실패했을 때 동작을 지정한다, 인자로 함수를 받는다.
  - 위 함수들은 체인 형태로 활용할 수 있다.
- excutor로 새로운 promise를 만든 다음 then과 catch를 이용하여 후속 동작까지 지정해줘야 어느정도 제대로 돌아가는 promise가 된다.
- ``` js
    const promise1 = new Promise((resolve, reject) => {
        resolve();
    });
    promise1
    .then(() => {
        console.log("then!");
    })
    .catch(() => {
        console.log("catch!");
    });
    // 출력 : then!

    const promise1 = new Promise((resolve, reject) => {
        reject();
    });
    promise1
    .then(() => {
        console.log("then!");
    })
    .catch(() => {
        console.log("catch!");
    });
    // 출력 : catch!
## __<span style="color:#9999ff">재사용하기</span>__
- 비슷한 비동기 작업을 할때 마다 new Promise를 하는 것은 비 효율적이기 때문에 new Promise(...)한 것을 그대로 리턴하는 함수를 만들면 된다.
- ``` js
    function startAsync(age) {
    return new Promise((resolve, reject) => {
        if (age > 20) resolve();
        else reject();
    });
    }

    setTimeout(() => {
    const promise1 = startAsync(25);
    promise1
        .then(() => {
        console.log("1 then!");
        })
        .catch(() => {
        console.log("1 catch!");
        });
    const promise2 = startAsync(15);
    promise2
        .then(() => {
        console.log("2 then!");
        })
        .catch(() => {
        console.log("2 catch!");
        });
    }, 1000);

    // 출력 : 1 then!
    //     :  2 catch!
## __<span style="color:#9999ff">작업 결과 전달하기</span>__
- resolve, reject 함수에 인자를 전달함으로써 then및 catch함수에서 비동기 작업으로부터 정보를 얻을 수 있다.
- ``` js
    function startAsync(age) {
    return new Promise((resolve, reject) => {
        if (age > 20) resolve(`${age} success`);    
        else reject(new Error(`${age} is not over 20`));
    });
    }

    setTimeout(() => {
    const promise1 = startAsync(25);
    promise1
        .then((value) => {
        console.log(value);
        })
        .catch((error) => {
        console.error(error);
        });
    const promise2 = startAsync(15);
    promise2
        .then((value) => {
        console.log(value);
        })
        .catch((error) => {
        console.error(error);
        });
    }, 1000);

    // 출력 : 
    // 25 success
    // Error: 15 is not over 20
    //     at /home/taehoon/Desktop/playground-nodejs/index.js:4:17
    //     at new Promise (<anonymous>)
    //     at startAsync (/home/taehoon/Desktop/playground-nodejs/index.js:2:10)
    //     at Timeout._onTimeout (/home/taehoon/Desktop/playground-nodejs/index.js:17:20)
    //     at listOnTimeout (internal/timers.js:554:17)
    //     at processTimers (internal/timers.js:497:7)
## __<span style="color:#9999ff">이 외 추가적인 것들</span>__
1. promise 체이닝
   - 
   - ![](https://joshua1988.github.io/images/posts/web/javascript/promise.svg)
   - promise의 또 다른 특징은 여러개의 프로미스를 연결하여 사용할 수 있다는 점입니다.
   - then(), catch() 메소드들은 새로운 프로미스 객체를 반환한다.
   - ``` js
       function getData() {
       return new Promise({
           // ...
       });
       }

       // then() 으로 여러 개의 프로미스를 연결한 형식
       getData()
       .then(function(data) {
           // ...
       })
       .then(function() {
           // ...
       })
       .then(function() {
           // ...
       });
2. then의 두 번째 인자로 onRejected를 받을 수 있다.
   - 
   - then만 쓰더라도 reject된 promise에 대한 처리를 할 수 있다.
   - ``` javascript
        const throwError = new Promise((resolve, reject) => {
        throw new Error("error");
        });
        throwError.then(
        () => console.log("throwError success"),
        () => console.log("throwError catched")
        );
        // 출력 : throwError catched
    - 하지만 가급적으로 catch를 쓰는 것이 좋고 효율적이다.
3. finally로 이행/거부 상관 없는 동작을 지정해줄 수 있다.
   - 
   - promise는 finally라는 메소드도 있다. 이 함수는 이행 거부 상관없이 가장 마지막으로 실행 된다.
## __<span style="color:#9999ff">간단한 동작 원리와 의의</span>__
- ![](https://i1.wp.com/elvanov.com/elvanov/wp-content/uploads/2021/07/IMG_0369.jpeg?resize=768%2C396&ssl=1)
- 왼쪽이 단순한 비동기 작업을 나태내었고 오른쪽은 promise가 어떤 구조를 가지고 있는지를 나타내고 있다.
- promise는 세가지 상태를 지니고 있다.
  - 대기상태(pending)
  - 이행상태(fulfilled)일때 then로 등록한 동작 실행
  - 거부상태(rejected) catch로 등록한 동작 실행

# 참고
- https://elvanov.com/2597
- https://joshua1988.github.io/web-development/javascript/promise-for-beginners/