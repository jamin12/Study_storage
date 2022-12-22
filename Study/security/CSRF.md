# CSRF
## __<span style="color:#9999ff">CSRF(Cross-site Request Forgery)</span>__
- 사용자가 자신의 의지와는 무관하게 공격자가 의도한 행위(수정,삭제 등등)을 특정 웹사이트에 요청하게 하는 공격
- 정상적인 사용자가 의도하지 않았지만, 자신도 모르게 서버를 공격하게 되는 경우이다.
1. ### __<span style="color:#ff9933">전제 조건</span>__
   1. 사용자는 보안이 취약한 서버로부터 이미 인증을 받은 상태여야 한다.
   2. 쿠키 기반으로 서버 세션 정보를 획들할 수 있어야 한다.
   3. 공격자는 서버를 공격하기 위한 요청 방법에 대해 미리 파악하고 있어야 하며, 예상치 못한 파라미터가 있으면 불가능 하다.
2. __<span style="color:#ff9933">공격 과정</span>__
   1. 사용자는 보안위 취약한 서버에 로그인 한다.
   2. 로그인 이후 서버에 저장된 세션 정보를 이용할 수 있는 sessionID가 사용자 브라우저 쿠키에 저장된다.
   3. 공격자는 서버에 인증된 브라우저의 사용자가 악성 스크립트 페이지를 누르도록 유도 한다.
   4. 사용자가 악성 스크립트가 작성된 페이지 접근시 쿠키에 저장된 sessionID는 브라우저에 의해 자동적으로 함께 서버로 요청된다.
   5. 서버는 쿠키에 담긴 sessionID를 통해 해당 요청이 인증된 사용자로부터 온 것을 판단하고 처리한다.
   6. ![](https://mblogthumb-phinf.pstatic.net/20121028_125/addong1224_1351415866058A3BhL_PNG/CSRF.png?type=w2)
## __<span style="color:#9999ff">CSRF 방어 방법</span>__
1. ### __<span style="color:#ff9933">Referrer검증</span>__
   - back-end 단에서 request의 referrer를 확인하여 domain이 일치하는지 검증하는 방법이다
   - 일반적으로 referrer검증만으로 대부분의 csrf공격을 방어할 수 있다.
   - 같은 도메인 내의 페이지에 XSS취약점이 있는경우 CSRF공격에 취약해질 수 있다.
   - domain단위 검증에서 좀 더 세밀하게 페이지 단위까지 일치하는지 검증을 하면 도메인 내의 타 페이지에서 XSS 취약점에 의한 CSRF공격을 방어할 수 있다.
2. ### __<span style="color:#ff9933">Security Token 사용(CSRF Token)</span>__
   - 사용자의 세션에 임의의 난수 값을 저장하고 사용자의 요청 마다 해당 난수 값을 포함 시켜 전송시킨다.
   - 이후 back-end단에서 요청을 받을 때 마다 세션에 저장된 토큰 값과 요청 파라미터에 전달되는 토큰 값이 일치하는지 검증하는 방법
   - 같은 도메인 내에 XSS취약점이 있다면 CSRF 공격에 취약해진다.
3. ### __<span style="color:#ff9933">Double Submit Cookie 검증</span>__
   - Double Submit Cookie 검증은 Security Tokenr검증의 한 종류로 세션을 사용할 수 없는 환경에서 사용할 수 있는 방법이다.
   - 웹브라우져 Same Origin 정책으로 인해 자바스크립트에서 타 도메인의 쿠키값을 확인 수정하지 못한다는 것을 이용한 기법
   - 스크립트 단에서 요청 시 난수 값을 생서앟여 쿠키에 저장하고 동일한 난수 값을 요청 파라미터(혹은 헤더)에 저장하여 서버로 정송
   - 서버단에서 쿠키의 토큰 값과 파라미터의 토큰값이 일치하는지만 검사하면 된다.
   - 피싱 사이트에서는 도메인이 달라 쿠키의 값을 저장하지 못해 가능한 방어 기법이다.
## __<span style="color:#9999ff">CSRF 설정</span>__
- MVC 구조는 세션과 쿠키를 통해 사용자 인증을 수행했기 떄문에 CSRF 공격에 취약하다
- 쿠키가 없으면 CSRF공격도 없다 브라우저에 저장되는 쿠키가 CSRF의 공격의 매개체이다.
- REST API 방식은 쿠키나 세션에 의존하지 않는 경향이 크기 때문에 CSRF공격에 대한 방어 설정을 비활성화 시키는 경우가 많다.
- 쿠키 대신에 로컬스토리지와 요청헤더를 사용하거나 세션대신에 JWT를 사용하기 떄문이다.

# 참고
- https://m.blog.naver.com/PostView.naver?isHttpsRedirect=true&blogId=addong1224&logNo=120172012449
- https://itstory.tk/entry/CSRF-%EA%B3%B5%EA%B2%A9%EC%9D%B4%EB%9E%80-%EA%B7%B8%EB%A6%AC%EA%B3%A0-CSRF-%EB%B0%A9%EC%96%B4-%EB%B0%A9%EB%B2%95
- https://junhyunny.github.io/information/security/spring-boot/spring-security/cross-site-reqeust-forgery/