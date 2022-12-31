# git cherry pick
- 다른 브랜치에 있는 커밋을 선택하여 내 브랜치에 적용시킬 때 사용하는 명령어 이다.
- cherry-pick은 같은 내용을 갖고 있는 커밋을 여러개 생성하기 때문에 꼭 사용해야 할 때 진행해야한다.
## __<span style="color:#9999ff">사용법</span>__
- ``` git
    # git cherry-pick {커밋 해시}

    git cherry-pick 34b4cab
    git cherry-pick 13f03ab

    # 또는 

    git cherry-pick 34b4cab 13f03ab

    # 또는 연속적인 커밋일 경우
    git cherry-pick 34b4cab..fe834e9
1. ### __<span style="color:#ff9933">머지 커밋 체리픽</span>__
   - ``` git
        # git cherry-pick -m 1 {머지 커밋 해시}
2. __<span style="color:#ff9933">-abort</span>__
   - 만약 cherry-pick을 중단하고 싶으면 -abort 옵션을 이용해서 cherry-pick을 중단할 수 있다. 이 경우 cherry-pick을 실행하기 전 상황으로 코드가 돌아간다
3. __<span style="color:#ff9933">-contiue</span>__
   - 충돌이 발생한 코드를 에디터로 열어서 수정한다. 그 다음 git add <file path> 명령으로 수정된 코드를 추가한다.이후 git cherry-pick -continue 명령을 실행하면 총돌을 수정한 코드가 반영되고, cherry-pick이 재개된다.

# 참고
- https://brownbears.tistory.com/606
- https://hbase.tistory.com/141