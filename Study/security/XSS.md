# XSS
## __<span style="color:#9999ff">쿠키</span>__
- __<span style="color:#ff9933">쿠키</span>__
  - 
  - 사용자가 인터넷 웹 사이트에 방문할 때 생기는 4KB이하의 파일
  - 쿠키 내용을 이용하여 클라이언트의 신분을 알 수 있음
  - 많은 웹 사이트는 쿠키를 이용하여 사용자 정보를 수집
- __<span style="color:#ff9933">쿠키 생성</span>__
  - 
  - 코드 
  - ``` 
    Set-Cookie: Name = Value; expires=Date;
    domain = DOMAIN_NAME; Path = Path;
    Secure
- __<span style="color:#ff9933">쿠키의 사용</span>__
  - 
  - 사용자 컴퓨터에 쿠키 생성
    - 사용자가 사이트에 방문하면 사이트가 사용자의 컴퓨터에 쿠키를 만듦
    - 쿠키를 만든 사이트의 도메인 이름, 그 사이트를 구분하는 숫자 쿠키 만기일 등의 정보가 공통적으로 들어있음
  - 사용자 컴퓨터의 쿠키를 웹 서버로 전송
    - 방문했던 사이트에 다시 접속하면 이미 저장된 쿠키를 통해 개인 정보를 알 수 있음
- __<span style="color:#ff9933">쿠키의 용도</span>__
  - 
  - 사이트 개인화
    - 쿠키를 이용하면 아이디와 비밀번호 외에도 사용자의 '성향'까지 파악가능
    - 사용자의 성향을 고려한 개인 맞춤 서비스를 마련한다면 다른 사이트와 중요한 차별점이 될 수 있음
  - 장바구니 시스템
    - 사용자가 고른 물건을 쿠키에 저장
  - 웹 사이트 이용 방식 추적
    - 사용자들의 사이트 방문 유형을 파악하여 마케팅 정보로 활용
  - 타깃 마케팅
    - 광고주가 대형 포털 사이트의 공간을 사들여 자회사의 광고를 사용자에게 보여주는 것
    - 광고를 낸 업체들이 광고 효과가 궁금해하기 때문에 광고 대행업체는 제3의 업체의 쿠키를 사용자 컴퓨터에 저장해 사용자의 이용 정보를 수집
---
별로 안중요한듯?
- __<span style="color:#ff9933">쿠키에 관한 오해</span>__
  - 
  - 쿠키가 바이러스를 전파한다?
    - 쿠키는 텍스트 파일이기 떄문에 '실행'되지 않아 바이러스를 전파할 수 없음
  - 쿠키가 사용자 컴퓨터에 피해를 입힌다?
    - 사이트에서 만든 특정 데이터만 있을 뿐 어떠한 정보도 담겨 있지 않음
    - 스스로 디렉터리를 읽거나 파일을 지우는 작업도 절대 수행 불가능
---

## __<span style="color:#9999ff">세션</span>__
- __<span style="color:#ff9933">Session</sapn>__
  - 
  - 일정 시간동안 같은 사용자로부터 들어오는 일련의 요구를 하나의 상태로 보고 그 상태를 일정하게 유지시키는 기술
  - 여기서 일정 시간 방문자가 웹 브라우저를 통해 웹 서버에 접속한 시점부터 웹 브라우저를 종료하여 연결을 끝내는 시점을 말한다.
  - 즉 방문자가 웹 서버에 접속에 있는 상태를 하나의 단위로 보고 그것을 세션이라고 한다.
- __<span style="color:#ff9933">세션 특징</span>__
  - 
  - 웹 서버에 웹 컨테이너의 상태를 유지하기 위한 정보를 저장한다.
  - 웹 서버에 저장되는 쿠키(= 세션 쿠키)
  - 브라우저를 닫거나 서버에서 세션을 삭제했을떄만 삭제가 되므로 쿠키보다 비교전 보안이 좋다.
  - 저장 데이터에 제한이 없다(서버 용량이 허용하는 한)
  - 각 클라이언트 고유 Session ID를 부여한다.
  - Session ID로 클라이언트를 구분하여 각 클라이언트 요구에 맞는 서비스 제공
- __<span style="color:#ff9933">세션의 동작 순서</span>__
  - 클라이언트가 페이지를 요청한다.
  - 서버는 접근한 클라이언트의 Request-Header필드인 Cookie를 확인하여 클라이언트가 해당 Sesison-id를 보냈는지 확인한다.
  - Session-id가 존재하지 않는다면 서버는 Session-id를 생성해 클라이언트에게 돌려준다
  - 서버에서 클라이언트로 돌려준 Session-id를 쿠리를 사용해 서버에 저장한다.
  - 쿠키이름 : JSESSIONID
  - 클라이언트는 제접속 시 이 쿠키를 이용하여 session-id값을 서버에 전달한다.


## __<span style="color:#9999ff">쿠키와 세션 차이</span>__
- ||쿠키|세션|
  |---|---|---|
  |저장위치|클라이언트(=접속자 PC)|웹서버|
  |저장형식|text|Object|
  |만료 시점|쿠키 저장시 설정</br>(브라우저가 종료되도 만료시점이 지나지 않으면 자동삭제되지 않음|브라우저 종료시 삭제</br>(기간 지정 가능)|
  |사용하는 지원(리소스)|클라이언트 리소스|웹 서버 리소스|
  |용량 제한|총 300개</br>하나의 도메인 당 20개</br>하나의 쿠키 당 4KB|서버가 허용하는 한 용량제한 없음|
  |속도|세션보다 빠름|쿠키보다 느림|
  |보안|세션보다 안좋음|쿠키보다 좋음|

## __<span style="color:#9999ff">XSS</span>__
- __<span style="color:#ff9933">XSS</span>__
  - 
  - Corss-Site Scripting약자 - 교차 사이트 스크립팅
  - 사용자가 요청한 페이지에 강제로 악성 스크립트를 삽입해서 브라우저에서 악의적인 동작이 실행되도록 하는 공격 기법
  - 입력을 받아들이는 부분의 스크립트 코드를 필터링하지 않음으로써 공격자가 스크립트 코드를 실행할 수 있음
- __<span style="color:#ff9933">자바스크립트로 실행되는 모든 코드가 실행될 수 있음</span>__
  - 
  - 특히 사용자의 세션을 공격자의 서버로 전송해서 탈취하거나
  - 악성코드가 있는 페이지로 리다이렉트 시키는 방식
  - 자바스크립트를 활용해서 내부 네트워크 포트스캔과 같은 공격도 가능
- __<span style="color:#ff9933">Stored XSS</span>__
  - 
  - 가장 일반적인 XSS 공격 유형
  - 사용자가 글을 저장하는 부분에 정상적인 평문이 아닌 스크립트 코드를 입력
  - 다른 사용자가 게시물을 열람하면 공격자가 입력해둔 악성 스크립트가 시행되어 사용자의 쿠키 정보가 유출되거나 악성 스크립트가 기획한 공격게 속수무책으로 당하게 됨
  - ![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=http%3A%2F%2Fcfile22.uf.tistory.com%2Fimage%2F997CF6485C89B39508BA31)
- __<span style="color:#ff9933">Reflected XSS</span>__
  - 
  - 사용자의 요청에 포함된 스크립트가 서버로부터 그대로 반사되어 응답메시지에 포함돼 브라우저에서 스크립트를 실행시키는 공격기법
  - URL의 변수 부분처럼 스크립트 코드를 입력하는 동시에 결과가 바로 전해지는 공격 기법
  - 스크립트가 웹앱 내에 저장되지 않고 영구적이지 않은 공격
  - 한 용청에 대한 하나의 응답으로 공격이 진행
  - ![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=http%3A%2F%2Fcfile2.uf.tistory.com%2Fimage%2F99CB0F485C89B39406841B)
- __<span style="color:#ff9933">DOM base XSS</span>__
  - 
  - DOM을 이용해서 요소들을 수정하 추가하는 등의 행위를 할 때 발생하는 XSS 취약점
  - 악의적인 스크립트가 포함 된 URL을 사용자가 요청하게 되어 브라우저를 해석하는 단계에서 발생하는 공격
</br>
</br>
</br>
</br>
---
중요한듯?
- **Reflected-XSS나 Stored-XSS** 
  - **서버에서 사용자의 요청을 처리하는 과정에서 취약점이 존재해서 응답으로 해당 스크립트가 전송**
- **Dom-basedXSS**
  - **클라이언트 측에서 DOM을 이용해서 동적으로 페이지를 조작할 때 발생하는 취약점**
---

- __<span style="color:#ff9933">취약점 찾기</span>__
  - 표준 스크립트 문자열을 입력해보기
    - ```
        <script>alert(document.cookie)</script>
  - 스크립트 검증 예시로 사용되는 코드
    - ```
        "><script>alert(document.cookie)</script>
        "><ScRiPt>alert(document.cookie)</ScRiPt>
        "%3e%3cscript%3ealert(document.cookie)%3c/script%3e
        "><scr<script>ipt>alert(document.cookie)</scr</script>ipt>
        %00"><script>alert(document.cookie)</script>

## __<span style="color:#9999ff">CSRF 공격</span>__
  - 피해자가 인지 못하는 상태에서 피해자의 브라우저가 특정 사이트에 강제적으로 리퀘스트를 보내도록 하는 기법
  - ![](https://1.bp.blogspot.com/-7Cdpxzrqmiw/V84o7nkF1QI/AAAAAAAABMA/UrTFpaCLb_wzvncWavPjAKRF8xhzNaE1gCEw/s1600/1006a345-9ef1-40a8-8013-554cd20f0b51.png)

## __<span style="color:#9999ff">XSS 와 CSRF 차이점</span>__
- 사용자의 브라우저 내에서 어떤 행위를 하느냐 아니면 서버로 요청을 보낸 특정 행위를 하게 하느냐의 차이
- XSS : 사용자의 브라우저에서 자바스크립트 코드를 실행시키는 공격
- CSRF : 사용자의 권한으로 사이트에서 원치않은 행위를 하도록 하는 공격

## __<span style="color:#9999ff">javascript</span>__
- 결과물을 HTML 페이지에 출력하는 방법
  - window.alert()메소드
  - HTML DOM 요소를 이용한 innerHTML프로퍼티
  - document.write()메소드
  - console.log() 메소드