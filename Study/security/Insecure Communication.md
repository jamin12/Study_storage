# Insecure Communication
## __<span style="color:#9999ff">IPSEC</span>__
- __<span style="color:#ff9933">TCP/IP 프로토콜</span>__
  - 
  - 인터넷의 중심인 전송 계층과 네트워크 계층에 걸쳐 있다.
  - 전송 제어 프로토콜인 TCP는 전송을 위한 것이고 IP는 네트워킹을 위한 것이다.
</br>
</br>

  - IPsec은 IP에 자체적인 보안 기능이 없기 때문에 개발
  - TLS(Transport LAyer Security)는 무자 그대로 전송 계층에서 통신을 암호화하는 프로토콜
- IPsec은 통신 세션에 각 IP패킷을 암호화하고 인증하는 안전한 인터넷 프로토콜 통신을 위한 인터넷 프로토콜 스위트
- IPsec은 네트워크 계층에 존재
- ![](https://player.slidesplayer.org/86/14078073/slides/slide_23.jpg)
- __<span style="color:#ff9933">전송모드</span>__
  - 
  - ![](https://i0.wp.com/byul124.cafe24.com/blog/wp-content/uploads/1/1331597366.jpg?resize=600%2C153)
  - ip 패킷의 페이로드를보호하는 모드, 즉 IP의 상위 프로토콜 데이터를 보호하는 모드이다.
  - Ip 패킷의 페이로드만 IPsec로 캡슐화 하고 IP헤더는 그대로 유지 -> 네트워크상 패킷 전송에 문제가 발생하지 않는다.
  - IP헤더를 보호하지 않기 때문에 트래픽 흐름(최초 출발지 및 최종 목적지) 분석될 수 있다.
  - 일반적으로 End Point 구간의 IP패킷 보호를 위해 사용한다.
- __<span style="color:#ff9933">터널모드</span>__
  - 
  - ![](https://i0.wp.com/byul124.cafe24.com/blog/wp-content/uploads/1/1165076035.jpg?resize=600%2C147)
  - ip패킷 전체를 보호하는 모드
    - ip패킷 전체를 IPsec로 캡슐화 하여 IP헤더를 식별할 수 없기 떄문에 네트워크상 패킷 전송 불가능 -> 전송구간 주소 정보를 담은 New IP헤더를 추가
    - 원본 IP 헤더를 보호하기 떄문에 최초 출발지 및 최종 목적지에 대한 트래픽 정보의 기밀성을 보장해준다.
    - 새로운 IP 헤더를 통해 보안/터널 게이트웨이 구간 정보는 노출될 수 있기 때문에 제한적 트래픽 흐름의 기밀성을 보장해준다.
    - 터널/보안 게이트웨이 구간 IP 패킷 보호는 물리적으로 떨어진 위치의 본점, 지점 사이에 다수의 사용자 호스트와 서버로 구성된 VPN 환경을 예로 들 수 있다.

## __<span style="color:#9999ff">TSL</span>__
- __<span style="color:#ff9933">전송계층 상에서 클라이언트, 서버에 대한 인증 및 데이터 암호화 수행</span>__
  - 
  - 클라이언트와 서버 양단 간 응용계층 및 TCP전송계층 사이에서 안전한 보안 채널을 형성해주는 역할을 하는 보안용 프로토콜
  - ![](https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcQfRMkRy2n56VT7aijtGKshG7wTpaOOnvRSvooZjFiI_MSqG2g7-KOoYK802B4zYa0kFm8&usqp=CAU)
  - ![](http://www.ktword.co.kr/img_data/2552_1.JPG)
  - |구분|구성요소|설명|
    |---|---|---|
    |동작원리|Handshake protocol|서버/클라이언트 상호 인증</br> 대칭키/암축방식/키 교환 방식 통한 보안 협상|
    |보안서비스|Change Cipher Spec|Handshake프로토콜에 의해 협상된 암오화 방식이 이후부터 적용됨을 상대방에게 알림|
    ||Alert Protocol|세션의 종료 또는 오류 발생 시 이를 상대방에게 알림|
    ||Record Protocol|메시지 분할, 압축 메시지 인증, 암호화 작업 수행|
  - Record protocol 그림
  - ![](https://www.researchgate.net/profile/Wazen-Shbair/publication/321347130/figure/fig7/AS:631648328634428@1527608105667/TLS-record-format.png)

- __<span style="color:#ff9933">SSL/TLS 주요기능</span>__
  - 상호인증
    - 공개키 인증서를 이용하여 클라이언트/서버 두 응용 간에 상대방에 대한 서버,클라이언트 상호 인증
  - 메시지 인증(메세지 무결성)
    - 메세지 인증 코드 HMAC에 의한 메세지 무결성 제공
  - 메시지 압축
    - 디폴트는 NULL
  - 암호화용 세션 키 생성(대칭 키 합의)을 위한 키교환
    - RSA : 두 키(공개 키 및 개인키)가 하나의 수 체계를 형성(서버 공개키 사용)
    - Diffie-Hellman : Diffie-Hellman프로토콜을 기반으로 한 키 교환 방식
  - 생성된 공유 비밀키에 의해 암호화된 종단간 안전한 연결 통로 제공
    - 스트림 암호화 : 40,128비트의 RC4
    - 블록 암호화 IDEA, 40,56 비트의 DES 168비트의 3DES등
## __<span style="color:#9999ff">MSNMS</span>__
- 보안이랑 상관없다(이거 왜 있는지 모르겠다. 안해두 될듯?)

## __<span style="color:#9999ff">파일 업로드 취약점</span>__
- 웹 서비스 첨부파일, 환경 설정 미흡을 이용하여 악의적인 스크립트가 포함된 파일을 업로드 한 후 웹 서버에 침투를 하는 공격
- 공격자는 서버 사이드 스크립트(PHP,JSP 등)을 이용하여 웹쉘을 제작
- 웹쉘은 원격에서 웹 서버를 제어하기 위한 목적으로 만들어졌으나, 지금은 웹쉘 = 악성코드라고 분류를 하여 안티바이러스에서 탐지함
- 게시판 첨부 파일, 이력서 첨부 파일, 이미지 첨부파일, 웹 채팅방 파일 공유 기능 등에서 발생
- __<span style="color:#ff9933">목적</span>__
  - 
  - 웹 서버를 통해 데이터베이스의 정보를 획득
    - 데이터베이스에 직접 공격을 할 수 없기 때문에 웹 서버를 침투한 후 소스코드내 데이터베이스 연결 정보를 통해 개인 정보 가져옴
    - 웹 서버를 통해 데이터베이스의 2차 공격도 진행 가능함
    - sql injection과 다른점 : 웹에서 노출되는 정보들을 하나씩 가져오는 sql injection과 달리 파일 업로드 취약점은 웹쉘을 통해 시스템에 아예 침투하고 난 뒤 원하는 데이터들을 마음껏 가져오는 것
  - 웹 서버를 시작으로 근접 네트워크 침투
    - 데이터베이스에 직접 공격을 할 수 없기 떄문에 웹 서버를 침투한 후 내부 시스템의 정보 획득
    - 내부 포탈 서버, 로그 서버 등 내부 시스템을 대상으로 포트포워딩,터널링 기법을 통해 공격자와 직접 연결을 함
- ![](https://cdn.inflearn.com/public/files/courses/325249/d35d00cc-9ab4-4ff8-b568-bac037e904ac/05_%EC%98%81%ED%96%A5%EB%A0%A5.png)