# CSP
- 콘텐츠 보안정책(Content-security policy)
- 신뢰할 수있는 웹 페이지 컨텍스트에서 악의적인 콘텐츠 실행으로 인한 크로스 사이트 스크립팅, 클릭 재킹 및 기타 코드 삽입 공격을 방지하기 위해 도입된 컴퓨터 보안 표준이다.
## __<span style="color:#9999ff">CSP 사용 헤더</span>__
- Content-Secuirty-Policy : W3C가 지정한 표준 헤더 - 주로 사용
- X-Content-Secuirty-Policy : FireFox/IE 구형 브라우저에서 사용되는 헤더
- X-WebKit-CSP : 크롬 기반 구형 브라우저에서 사용되는 헤더
## __<span style="color:#9999ff">CSP 지시문 옵션</span>__
### __<span style="color:#ff9933">지시문</span>__
- default-src : 모든 리소스에 대한 정책
- script-src : Javascript 등 웹에서 실행 가능한 스크립트에 대한 정책
- style-src : css에 대한 정책
- connect-src : script src로 불러올 수 있는 url에 대한 정책
- img-src : 이미지 ( data: URL에서 이미지가 로드되는 것을 허용하려면 data:를 지정 )
- script-nonce : script-src에 nonce가 포함되는 정책
- form-action : form 태그 내 action 부분에 대한 정책
- object-src : 플러그인, 오브젝트에 대한 정책
- media-src : video, audio
- font-src : font
- sandbox : HTML 샌드박스
- reflected-xss : X-XSS-Protection header와 동일한 효과
- report-uri : 정책 위반 케이스가 나타났을 때 내용을 전달할 URL
- base-uri: \<base> 요소에 나타날 수 있는 URL을 제한
### __<span style="color:#ff9933">옵션</span>__
- \* : 모든 것을 허용
- 'none' : 모두 차단
- 'self' : 현재 도메인만 허용
- 'unsafe-inline' : 소스코드 내 인라인 자바스크립트 및 CSS 허용
- 'unsafe-eval' : eval 같은 텍스트-자바스크립트 메커니즘 허용

# 참고
- https://webstone.tistory.com/98
- https://jdh5202.tistory.com/820