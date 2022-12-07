# merge
## __<span style="color:#9999ff">정의</span>__
- 두 브랜치를 합치는 방법, `git merge`는 다른 브랜치를 현재 Checkout된 브랜치에 Merge 하는 명령이다. Merge 하고 나서 현재 브랜치가 Merge 된 결과를 가리키도록 옮긴다.
## __<span style="color:#9999ff">예시</span>__
- ![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FLIudr%2FbtqCpQU8cPT%2FPM34NEHFCUZndlhXxt55n0%2Fimg.png)
- 두 브랜치의 마지막 커밋 두개(c3,c4)와 공통조상(c2)를 사용하는 3-way merge로 새로운 커밋을 만들어 냅니다.
- ![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FduaerI%2FbtqCsIhwu4d%2FxsbJjGzaE9a83aCkLYz2x1%2Fimg.png)
## __<span style="color:#9999ff">되돌리기</span>__
- Refs 수정
  - 실수로 생긴 merge 커밋이 로컬 저장소에만 있을 때는 브랜치를 원하는 커밋을 가리키도록 옮기는 것이 쉽고 빠릅니다. 이에 대해서는 git reset --hard HEAD~명령으로 브랜치를 되돌리면 됩니다.
- 커밋 되돌리기 브랜치를 올믹는 것을 할 수 없는 경우는 모든 변경사항을 취소하고 새로운 커밋을 만들 수도 있습니다. git에서는 이 기능을 `revert`라 부릅니다. git revert 명령은 git cherry-pick명령의 반대로 볼 수 있습니다.
# 참고
- https://git-scm.com/book/ko/v2/%EB%B6%80%EB%A1%9D-C%3A-Git-%EB%AA%85%EB%A0%B9%EC%96%B4-Branch%EC%99%80-Merge
- https://learngitbranching.js.org/?locale=ko
- https://readystory.tistory.com/151