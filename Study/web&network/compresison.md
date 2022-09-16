# Compresison
- 압축을 해서 웹서버에서 더 작은 파일을 서빙할 수 있도록 하는 기술이다.
- URL을 통해서 웹사이트에 접속을 하면, 브라구너는 웹서버가 압축을 해서 보낸다는 것을 인지한다.
- 압축을 푸는 것이 네트워크 요청보다 빠르다.
## __<span style="color:#9999ff">압축 알고리즘</span>__
### __<span style="color:#ff9933">종단 간 압축</span>__
- 압축에 있어, 종단간 압축은 웹 사이트의 가장 큰 성능 이득이 발생하는 곳입니다. 
- 서버에서 http 파일을 압축하여 전송하고 클라이언트가 압축을 풀어서 사용자에게 제공하는 압축방식이다.
- 서버는 요청에 Accept-Encoding헤더를 보고 그에 맞는 압축 알고리즘으로 데이터를 압축한다.
- 응답 헤더에 Content-Encoding헤더를 통해 클라이언트에게 해당 파일이 어떤 알고리즘으로 압축되었는지를 알려준다.
- Vary헤더에 Content-Encoding을 포함시켜서 응답해야한다.
> Vary헤더는 클라이언트가 캐시된 리소스를 사용할 떄 해당 리소스를 재사용해도 되는지를 판단하는 기준을 제시한다.
### __<span style="color:#ff9933">Hop-by-Hop 압축</span>__
- 클라이언트와 서버 사이의 경로 사에 있는 어떤 두 노드 사이에서 메세지의 바디에 압축이 일어난다.
- ![](https://ngwoon.github.io/assets/images/post/Network/HTTP-%EC%95%95%EC%B6%95/hop-by-hop.png)
- 요청 측의 노드가 TE헤더로 원하는 압축 방식을 기술한다. 서버에서는 압축 없는 본 데이터를담아 응답하고 응답 측의 노드가 이전에 TE헤더에 기술했던 방식으로 데이터를 압축 후 다음 노드에게 전달한다. 요청 측 노드가 해당 응답을 받으면 본 데이터로 복원한 뒤 클라이언트에게 전달한다.
- 데이터의 크기가 상당히 클 때 주로 사용된다.
## __<span style="color:#9999ff">종류</span>__
### __<span style="color:#ff9933">Gzip</span>__
- 최대 70%까지 사이즈를 줄여주는 가장 보편적인 압축 알고리즘이다.
- 웹사이트 파일의 중복콛, 띄어쓰기의 양을 줄여서 동작하고, 9단계에 걸친 옵션을 제공하여 압축량과 압축에 걸리는 시간을 세세하게 조정할 수도 있다.
### __<span style="color:#ff9933">Brotli</span>__
- Gzip보다 js는 14% HTML은 21%, css는 17%더 작게 만들어준다고 설명 되어있따.

# 참고
- https://yceffort.kr/2021/01/brotli-better-html-compression
- https://developer.mozilla.org/ko/docs/Web/HTTP/Compression
- https://ngwoon.github.io/network/2020/09/26/HTTP-%EC%95%95%EC%B6%95/