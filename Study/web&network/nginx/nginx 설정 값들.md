# Nginx 설정 값들
## __<span style="color:#9999ff">전체 블록</span>__
1. ### __<span style="color:#ff9933">worker_processes</span>__
   - nginx의 실행 가능한 worker 프로세스의 수를 지정한다.
   - auto로 설정 시 자동으로 이 값을 찾아준다.
   - ``` 
        worker_processes auto;
2. ### __<span style="color:#ff9933">worker_rlimit_nofile</span>__
   - woker process들에서 최대로 열린 파일들의 수를 제한할 수 있다.
   - 이 수가 클수록 메인 프로세스를 재시작 할 필요가 없어진다고 한다.
   - 대신 열려있는 파일의 수 만큼 서버의 부하가 커진다.
   - ```
        worker_rlimit_nofile 65535;
## __<span style="color:#9999ff">Event 블록</span>__
1. ### __<span style="color:#ff9933">worker_connections</span>__
   - 이벤트 블록 내에서 설정하는 worker_connections를 설정 할 때는 proxy서버를 통해 연결된 커넥션들을 포함한 클라이언트들의 모든 커넥션들의 숫자를 고려해야한다.
   - 위에서 지정한 **worker_rlimit_nofile**의 수를 넘어서는 안된다.
   - Default: worker_connections 512; 
   - ```
        worker_connections 65534;
2. ### __<span style="color:#ff9933">use epoll</span>__
   - connection processing method를 정한다.
   - standard는 select 인데, 조금 더 효율적으로 바뀐 것이 epoll방식이라고 한다.
   - ```
        use epoll;  
3. ### __<span style="color:#ff9933">multi_accept</span>__
   - off로 되어있으면 woker프로세스는 한번에 하나의 커넥션만을 허용한다.
   - on으로 되어있다면 모든 새로운 커넥션을 한번에 다 허용할 수 있다.
   - Default: multi_accept off;
## __<span style="color:#9999ff">http 블록</span>__
1. ### __<span style="color:#ff9933">Upstream</span>__
   - upstream 지시어는 upstream서버에 대해서 설정하기 위한 것이다
   - 인자로 설정할 upstream 서버의 이름을 넣어주면 도니다.
   - 서버들은 TCP이든 UNIX 도메인 소켓이든 모두 listen할 수 있습니다.
   - ```
        upstream backend {  # upstream 이름
            server 127.0.0.1:3000; # 서버
            keepalive 32;
        }
2. ### __<span style="color:#ff9933">types</span>__
   - request의 MIME 타입들에 대해 파일이름을 매칭시켜 줍니다.(확장자는 대소문자 구분하지 않는다.)
   - default_type application/octet-stream; 디폴트 타입도 설정 가능
3. ### __<span style="color:#ff9933">keepalive_timeout</span>__
   - keepalive로 무한정 접속을 연결시켜 놓다보면, 서버를 사용하지 않는 혹은 못하는 connection까지 모두 keep하고 있으므로 자원의 손실이 발생하게 된다. keepalive_timeout로 일정시간지나도록 요청이 없으면 connection을 끊도록 할 수 있다.
   - ```
        keepalive_timeout 5;
4. ### __<span style="color:#ff9933">reset_timedout_connection</span>__
   - time out 커넥션을 리셋할 것인지에 대한 여부 설정
   - 클라이언트 연결이 시한 만료가 될 때 연결 상태에 따라 이 연결과 연관된 정보가 메모리에 남게 된다. 이 옵션을 활성화 시키면 시한이 만료된 후에 이 연결과 연관된 모든 정보가 삭제된다
   - time out된 keep-alive커넥션은 클로즈드되게 된다.
5. ### __<span style="color:#ff9933">gzip</span>__
   - text데이터들을 압축해서 전송해주는 옵션
   - __<span style="color:#14a492">gzip_min_length</span>__
     - 암축할 최소 길이
     - Content-length헤더에 기록된 값과 비교해서 압출할지를 정한다. 
     - default: 20byte
   - __<span style="color:#14a492">gzip_comp_level</span>__
     - 압축레벨 보통 3~4정도가 적당하다.
   - __<span style="color:#14a492">gzip_types</span>__
     - 압축할 타입에 대해서 정의
     - text/plain, application/json 같은 타입을 정의해서 사용한다.
   - __<span style="color:#14a492">gzip_proxied</span>__
     - nginx가 proxy서버로서 동작할 때 압축을 할 것인지에 대한 설정
     - nginx는 request헤더 필드의 via값을 보고 proxy서버로서 동작하는지를 판단합니다.
     - 아래 코드의 expired는 response헤더에 Expires필드 값이 있을 때 압축하라고 하는거고
     - no-cache, no-store, private은 response헤더에 Cache-Control이 no-cache, no-store, private일 때 압축하라는 것 입니다. 
     - auth는 request헤더값이 Authorization필드를 가지고 있을 때 압축하라는 것이다.
     - ```
        gzip            on;
        gzip_comp_level 3;
        gzip_min_length 1000;

        gzip_types text/plain text/css application/json application/x-javascript text/xml application/xml application/xml+rss text/javascript;

        gzip_proxied expired no-cache no-store private auth;
6. ### __<span style="color:#ff9933">client_body_timeout</span>__
   - client의 request body를 읽을 때의 timeout을 정의한다.
   - default 60s
   - ```
        client_body_timeout 25;
7. ### __<span style="color:#ff9933">send_timeout</span>__
   - client에게 response를 전송할 때의 timeout값을 설정한다.
   - 여기서 설정한 시간안에 client가 아무것도 전송받지 못하면 connection이 closed된다.
   - default 60s
8. ### __<span style="color:#ff9933">access_log</span>__
   - access로그에 대한 설정을 할 수 있습니다.
   - default "access_log logs/access.log combined;"
   - ```
        access_log logs/access.log main;
   - 유저수가 많은 대형 서비스의 경우는 access 로그를 작성하는 것이 부담이 될 수 있으므로 off로 해놓는 것도 생각해 볼수 있는 옵션이다.

## __<span style="color:#9999ff">server 블록</span>__
1. ### __<span style="color:#ff9933">Location</span>__
   - request URL에 따른 설정을 하는 곳이다. 
   - URL에 대한 매칭은 텍스트 값을 prefix로 하여 매칭하거나
   - 주어진 정규식을 이용하여 매칭할 수 있다.
   - __<span style="color:#14a492">header 정보 세팅</span>__
     - 헤더정보 추가 
       - 아래와 같이 add_header 지시어를 이용해서 expires커스텀한 헤더정보를 추가 할 수 있따.
       - ```
            location =/test.jpg {
                add_header   expires  modified +24h;
                add_header   custom_header  "custom_header_test"
            }
     - allow and deny
       - 허용 ip와 거부 ip설정
       - ``` 
            server {
                listen   80;
                server_name .internal.io;

                location /private {
                    allow 182.16.100.0/8;
                    deny all;
                }
            }
2. ### __<span style="color:#ff9933">server_name</span>__
   - 가상 서버의 이름을 정할 떄 사용한다.
   - 첫 번째 이름이 primary서버 네임이 된다.
3. ### __<span style="color:#ff9933">listen</span>__
   - ip를 위한 address와 port번호를 설정할 수 있다.
   - UNIX도메인을 위한 path를 설정해 줄 수도 있다.
   - port번호가 없으면 80포트로 listen하게 된다.
   - ```
        listen 127.0.0.1:8000;
        listen localhost:3000;
        listen 127.0.0.1;
        listen 443; #for https
        listen *:8000;

        # IPv6
        listen [::]:8000;
        listen [::1];
4. ### __<span style="color:#ff9933">SSL관련한 설정</span>__    
   - ``` 
        server {
            ssl_certificate      cert/example.com.chained.crt;
            ssl_certificate_key  private/example.com.key;
            ssl_session_timeout  60;
        }
3. ### __<span style="color:#ff9933">return</span>__    
   - return을 사용하면 processing을 멈추고, code가 주어지면 해당 코드와 함께 client에게 return해 줍니다.
   - ```
        return code
        return code URL
        return URL
   - nginx에서 \$scheme변수는 http인지 https인지를 나타내 주고
   - $request_uri는 뒤에 변수들이 붙어있는 request URI를 의미합니다.
   - 예를들면, "www.oldDomain.com/test?score=88"에서 .com 뒷부분을 의미합니다.
   - 301은 Moved Permanently(영원히 이동)을 의미합니다.
   - ``` 
        server {
            listen 80;
            listen 443 ssl;
            server_name www.oldDomain.com;
            return 301 $scheme://www.newDomain.com$request_uri;
        }
   - 특히 3xx로 code를 return하려면 이동한 url을 인자로 꼭 넣어주어야 한다.
   - response의 body에 아래와 같은 방법으로 text를 전달할 수도 있습니다.
   - 예를 들어서, 유효하지 않은 인증 token을 가지고 있는 클라이언트에게, 아래와 같이 보낼 수 있다.
   - ``` 
        return 401 "token이 expire되어서 접근이 제한되었습니다."
6. ### __<span style="color:#ff9933">proxy_pass</span>__    
   - nginx가 proxy로서 동작할 때,
   - request를 중계받는 서버의 protocol과 address 그리고 location에 매핑될 URI를 설정한다.
   - 아래 코드는 upstream서버인 backend로 보내주는 코드다.
   - ```
        upstream backend {
            server 127.0.0.1:3000;
            keepalive 32;
        }
        server {
            listen 80;
            server_name test.com;
            location /care {
                proxy_pass http://backend/care
                proxy_http_version 1.1;
                proxy_set_header Connection "";
            }
        }
7. ### __<span style="color:#ff9933">proxy_set_header</span>__
   - nginx가 proxy로 중계할 때 중계받는 nodejs같은 서버에 request header를 다시 재정의해서 전달할 때 사용한다.
   - host는 nginx의 변수인 $host를 사용하면 되고
   - 이외의 header는 아래와 같이 해주면 됩니다.
   - Connection은 upstream서버를 사용하기 위한 설정으로,
   - 필요하지 않으면 사용하지 않아도 됩니다
   - ```
        upstream backend {
            server 127.0.0.1:3000;
            keepalive 32;
        }
        server {
        listen 80;
        server_name test.com;
            location /care {
                proxy_set_header Host $host;
                proxy_set_header X-Real-IP $remote_addr;
                proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
                proxy_set_header X-Forwarded-Proto $scheme;
                proxy_set_header Connection "";
                proxy_http_version 1.1;
                
                proxy_pass http://backend/care 
            }
        }
8. ### __<span style="color:#ff9933">proxy_set_header</span>__
   - try_files는 인자로 해당 파일을 찾을 수 있는 몇개의 path들을 넣을 수 있는데
   - 먼저 정의된 path부터 해당 파일이 있는지 찾아서 request요청을 처리하는데 사용된다.
   - 아래에서는 request할 때 들어온 uri경로에서 찾아보고 
   - 없으면 /images/default.gif로 redirect해 준다.
   - ```
        location /images/ {
            try_files $uri /images/default.gif;
        }

# 참고
- https://yangbongsoo.tistory.com/13
- https://heeseong.co.kr/m/218
- http://nginx.org/en/docs/ngx_core_module.html
- https://developer88.tistory.com/299
- https://bentist.tistory.com/81