# git rebase
## __<span style="color:#9999ff">정의</span>__
- 브랜치끼리의 작업을 접목하는 방법, 리베이스는 기본적으로 커밋들을 모아서 복사한 뒤, 다른 곳에 떨궈 놓는것이다.
## __<span style="color:#9999ff">예시</span>__
- ![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FLIudr%2FbtqCpQU8cPT%2FPM34NEHFCUZndlhXxt55n0%2Fimg.png)
  - 이렇게 master와 experiment 브랜치가 두개가 있을 때 rebase를 하게되면
- ``` txt
    $ git checkout experiment
    $ git rebase master
    First, rewinding head to replay your work on top of it...
    Applying: added staged command
    ```
- ![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FSVKNl%2FbtqCqNcQPaP%2Fijwz6OKTfr2mGke0o6lXM1%2Fimg.png)
  - TODO: diff 정리
  - 두 브랜치가 나뉘기 전인 공통 커밋으로 이동하고 나서 그 커밋부터 지금 checkout한 브랜치가 가르키는 커밋까지 diff를 차례로 만들어 어딘가에 임시로 저장해 놓습니다.
  - rebase할 브랜치(experiment)가 합칠 브랜치(master)가 가리키는 커밋을 가리키게하고 아까 저장해 놓았던 변경사항을 차례대로 적용합니다.
- ``` txt
    $ git checkout master
    $ git merge experiment
    ```
- ![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2F2DnEf%2FbtqCoQ13ABY%2F05Bjbs4rC8DahQc1KQTkkK%2Fimg.png)
  - 그리고 나서 master 브랜치를 "fast=forward"시킵니다.

- C4`로 표시된 커밋에서의 내용은 merge랑 똑같은 결과를 보여줄겁니다. 하지만 rebase가 좀 더 깨끗한 히스토리를 만듭니다. rebase한 브랜치의 Log를 살펴보면 히스토리가 선형입다. 일을 병렬로 동시에 진행해도 Rebase하고 나면 모든 작업이 차례대로 수행된것처럼 보입니다.

## __<span style="color:#9999ff">활용</span>__
- 아래와 같이 여러 갈래로 갈라져 나온 브랜치 같은 히스토리가 있다고 하겠습니다.
- **server**브랜치를 만들어서 서버 기능을 추가하고 그 브랜치에서 다시 **client**브랜치를 만들어 클라이언트 기능을 추가 합니다. 마지막으로 **server**브랜치로 돌아가서 몇 가지 기능을 더 추가합니다.
- ![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FdywY7M%2FbtqCsIog2iO%2F2obzcFFcVeGLwKov6K5fy1%2Fimg.png)
- 이때 테스트가 덜 된 **server**브랜치는 그대로 두고 **client**브랜치만 **master**로 합치려는 상황을 생각해보겠습니다. **server**와는 아무 관련이 없는 **client**커밋은 c8, c9입니ㅏㄷ. 이 두 커밋을 **master**브랜치에 적용하기 위해서 --onto 옵션을 사용하여 아래와 같은 명령을 실행합니다.
- ``` txt
    $ git rebase --onto master server client
    ```
- 이 명령은 **master**브랜치로부터 **server** 브랜치와  **client**브랜치의 공통 조상까지의 커밋을 **client**브랜치에서 없애고 싶을 때 사용합니다.
- ![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FEOFw5%2FbtqCoR04EX1%2Fk0ooNvrvLRveXpt41IUz80%2Fimg.png)
- 이제 **master**브랜치로 돌아가서 "fast-forward"시킬 수 있습니다.
- ``` txt
    $ git checkout master
    $ git merge client
    ```
- ![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FtO4qe%2FbtqCuYjQJFg%2F3yvBzm5FKCWbhxiS5PHW00%2Fimg.png)
- **server** 브랜치의 일이 다 끝나면 **git rebase <base_branch> <topic_branch>** 라는 명령으로 Checkout 하지 않고 바로 **server** 브랜치를 **master** 브랜치로 Rebase 할 수 있습니다. 이 명령을 토픽 브랜치(**server**)를 Checkout 하고 베이스 브랜치(**master**) 브랜치에 Rebase 합니다.
- ``` txt
    $ git rebase master server
    ```
- ![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2Fd4v8TU%2FbtqCl9uq4qe%2FNrx9SdgBasXVvXjXCx4vZK%2Fimg.png)
- 이제 마지막으로 **master** 브랜치를 "Fast-forward" 시키겠습니다.
그리고서 더이상 필요하지 않게된 **clien** 나 **server** 브랜치는 삭제해도 됩니다. 브랜치를 삭제해도 커밋 히스토리는 여전히 남아있습니다.
- ![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2Fmb5om%2FbtqCrOPNyeK%2FpUIg2ibXfE3PSdn8572jbK%2Fimg.png)
# 참고
- https://seosh817.tistory.com/240
- https://learngitbranching.js.org/?locale=ko
- https://readystory.tistory.com/151
- https://git-scm.com/book/ko/v2/Git-%EB%B8%8C%EB%9E%9C%EC%B9%98-Rebase-%ED%95%98%EA%B8%B0