# XSS

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
