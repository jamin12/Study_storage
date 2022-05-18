# git 커밋 메시지 규칙
- ``` 
    타입(Type): 제목(Subject)
    // 반드시 한줄 띄우기

    본문(Body) // 생략가능
    /// 반드시 한줄 띄우기

    꼬리말(Footer) // 생략 가능
- __<span style="color:#9999ff">타입</span>__
  - Feat - 새로운 기능 추가
  - Fix - 버그 수정
  - Build - 빌드 관련 파일 수정
  - Ci - CI관련 설정 수정
  - Docs - 문서 (문서 추가, 수정, 삭제)
  - Style - 스타일 (코드 형식, 세미콜론 추가: 비즈니스 로직에 변경 없는 경우)
  - Refactor - 코드 리팩토링
  - Test - 테스트 (테스트 코드 추가, 수정, 삭제: 비즈니스 로직에 변경 없는 경우)
  - Chore - 기타 변경사항 (빌드 스크립트 수정 등)
- __<span style="color:#9999ff">제목</span>__
  - 제목은 50를 넘기지 않고 ,마침표를 붙이지 않습니다.
  - 과거시제를 사용하지 않고 명렬조로 작성합니다.
  - 제목과 본문은 한 줄 띄워 분리합니다.
  - 제목의 첫 그자는 반드시 대문자로 씁니다.
  - 제목이나 본문에 이슈 번호가 있다면 붙여야 합니다.
  - ```
    EX) Feat: 신규 RFID 인식 기능 추가
- __<span style="color:#9999ff">본문</span>__
  - 선택 사항이기에 모든 commit에 본문 내용을 작성할 필요는 없습니다
  - 한줄에 72자를 넘기면 안 됩니다.
  - 어떻게(how)보다 무엇을 왜(What,Why)에 맞춰 작성합니다.
  - 설명뿐만 아니라. commit의 이유를 작설할 때에도 씁니다.
  - ``` 
    EX) 신규 RFID 기능 인식 기능 추가
          - RFIDReader.java: 사용자 요건 사항으로 인한 RFID 인식 기능 추가
- __<span style="color:#9999ff">꼬리말</span>__
  - 선택 사항이므로 모든 comit에 꼬리말을 작성할 필요는 없습니다.
  - issue tracker ID를 작성할 때 사용합니다.
  - 해결 : 이슈 해결시 사용
  - 관련 : 해당 commit에 관련된 이슈번호
  - 참고 : 참고할 이슈가 있는 경우 사용
  - ```
    해결: #123
    관련: #321
    참고: #222
- ```
    EX) Feat: 신규 RFID 인식 기능 추가(#123)

        신규 RFID 기능 인식 기능 추가
        - RFIDReader.java: 사용자 요건 사항으로 인한 RFID 인식 기능 추가

        해결: #123

# 참고
- https://junhyunny.github.io/information/github/git-commit-message-rule/
