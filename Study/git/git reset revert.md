# reset revert
## __<span style="color:#9999ff">차이점</span>__
- reset
  - 시간을 아예 과거의 특정 사건(commit)으로 되돌린다.
- revert
  - 현재에 있으면서 과거의 특정 사건(commit)들만 없던 일로 한다.
## __<span style="color:#9999ff">reset</span>__
- ![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FdfdjYN%2Fbtrtt4A9SQ1%2F7CbHAO7D4PBKK6zzCiFPX1%2Fimg.png)
- reset은 위 사진과같이 4번째 커밋이 **완전히 사라지게 된다.**
1. ### __<span style="color:#ff9933">사용법</span>__
   - ``` 
        git reset --soft [commit ID] 
        git reset --mixed [commit ID]
        git reset --hard [commit ID]
   - soft
     - commit 된 파일들을 staging area로 되돌려 놓는 옵션(commit 하기 전 상태로)
   - mixed
     - 옵션을 붙이지 않는다면 기본으로 설정됩니다. commit 된 파일들을 working directory로 돌려놓는 옵션(add 하기 전 상태로)
   - hard
     - commit 된 파일들 중 tracked 파일들을 working directory에서 삭제(untracked 파일은 계속해서 untracked 상태로 남음)
2. ### __<span style="color:#ff9933">특징</span>__
   - 커밋 히스토리를 깔끔하게 유지할 수 있고, 혼자 작업시 편하게 되돌아 갈 수 있다는 장점이 있다.
   - 다른 이들과 같은 브랜치에서 함께 작업 할 때 커밋이 뒤 섞여버릴 수 있다는 단점도 존재한다.

## __<span style="color:#9999ff">revert</span>__
- ![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FbcdPtC%2Fbtrtvrpi9Sv%2FR56nkBED9lKqW2Zz35NkHk%2Fimg.png)
- revert의 경우 4번 째 위치에 2번 째 커밋이 위치하게된다.
- 브랜치의 커밋 히스토리를 수정하지 않으면서 특정 커밋의 변경사항을 되돌리는 작업에 유용하다.
1. ### __<span style="color:#ff9933">사용법</span>__
- ```
    git revert [commit ID]
    git revert HEAD~3
    git revert HEAD^
2. ### __<span style="color:#ff9933">특징</span>__
   - 히스토리에 남게되어 왜 돌아갔는지 등 기록을 남길 수 있고, 다른 사람들과 혐업 시 코드 충돌을 최소화 할 수 있다.

# 참고
- https://velog.io/@njs04210/Git-reset%EA%B3%BC-revert-%EC%95%8C%EA%B3%A0-%EC%82%AC%EC%9A%A9%ED%95%98%EA%B8%B0
- https://hajoung56.tistory.com/31