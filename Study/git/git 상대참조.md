# 상대 참조
- git에서 커밋의 해시를 이용해 이동할 수 있다.
- fed2da64c0efc5293610bdd892f82a58e8cbc5d8같은 해시는 앞의 fed2만 입력해도 이동 가능하다.
- 하지만 매번 해시를 확인하기에는 귀찮기 때문에 상대 참조를 사용한다.
## __<span style="color:#9999ff">캐럿(^)</span>__
- 참조 이름에 하나씩 추가할 때 마다 명시한 커밋의 부모를 찾게 된다.
- ```
    git checkout main^
- ![](https://velog.velcdn.com/images%2Fhhsk1500%2Fpost%2F283e2636-a6b3-4608-9bc7-6b2f05dbc89b%2Fimage.png)
## __<span style="color:#9999ff">틸트(~)</span>__
- 캐럿(^)을 계속 입력해서 올라가는 것 말고 틸트(~)연산자는 올라가고 싶은 부모의 갯수가 뒤에 온다.
- ``` 
    git checkout HEAD~4
- ![](https://miro.medium.com/max/640/1*c9U4U3W49QtoAPJZ4SMCeQ.webp)
- 상대 참조를 사용하는 가장 일반적인 방법은 브랜치를 옮길떄 사용된다. 
- -f 옵션을 이용해 브랜치를 특정 커밋에 직접벅으로 재지정 할 수 있다.
- ```
    git branch -f main HEAD~3
- ![](https://miro.medium.com/max/640/1*PGsOmR-Fk0PAecaMFxhADg.webp)

# 참고
- https://learngitbranching.js.org/?locale=ko
- https://violetboralee.medium.com/git-next-level-25433466753a