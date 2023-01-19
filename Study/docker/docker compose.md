# docker compose
- 여러 개의 컨테이너로부터 이루어진 서비스를 구축, 실행하는 순서를 자동으로 하여, 관리를 간단히 하는 기능이다.
- compose파일을 준비하여 커맨드를 1회 실행하는 것으로, 그 파일로 부터 설정을 읽어들여 모든 컨테이너 서비스를 실행시키는 것이 가능하다.
## 주요 단계
1. 각각의 컨테이너의 Dockerfile을 작성한다.(기존에 있는 이미지를 사용할 경우 불필요)
2. docker-compose.yml을 작성하고, 각각 독립된 컨테이너의 실행 정의를 실시한다.(경우에 따라는 구축 정의도 포함)
3. docker-compose up 커맨드를 실행하여 정의한 컨테이너를 개시한다.
## docker-compose.yml 기본 문법
1. ### version
   - docker-compose.yml파일으 ㅣ명세 버전, 버전에 따라 도커 엔진 버전도 다르다.
   - ``` docker
     version: '2'
2. ### services
   - 컨테이너에 사용할 이미지 이름과 태그(버전) 태그를 생략하면 최신 버전을 설치한다.
   - 이미지가 업승면 자동으로 pull하기 떄문에 수작업으로 로컬에 설치할 필요가 없다.
   - ports가 있어서 일반 dockerfile을 구성할 때와 마찬가지로 호스트 포트로 접근해야 내용을 볼 수 있다. 
     - ``` docker
         services:
          django:
            image: ...
            ports:
              - "8000:80" // 호스트 포트:컨테이너 포트
          postgres:
        	image: ....
   1. restart
      - 재시작 정책. pm2대신 사용하기도 하고, 도커 컨테이너가 실행되는 순간 접근하여 접근 시간에 따른 실패를 방지하고 자동으로 재시작하게끔 해주기도 한다.
      - ``` docker
        services:
          django:
            image: ...
            restart: always // "no", always, on-failure, unless-stopped
          postgres:
          	image: ....
   2. bulid
       - 이미지 자체 빌드 후 사용할 경우 build를 이용해 사용한다. 이미지 빌드를 위한 dockerfile이 필요하니까 지정해주면 된다. 자체 빌드이니 image속성 대신 사용한다.
       - ``` docker
         services:
           django:
             build:
               context: .
               dockerfile: ./compose/django/Dockerfile-dev 
           postgres:
           	image: ....
   3. environment
        - 사용하고자 하는 이미지의 환경 변수.
        - 여기서, dockerfile의 환경변수와 docker-compose.yml의 환경변수가 중복되어서 올라가면 어떻게 되느냐?는 의문이 생깁니다.
        - 우선순위는 다음과 같습니다. 즉, docker.compose.yml이 덮어쓰게 됩니다.
        1. docker-compose [run / exec] -e key:value
        2. docker-compose.yml의 environment
        3. Dockerfile의 ENV
        - ``` docker
           services:
             db:
               image: mysql:5.7 // 사용할 이미지
               volumes:
                 - ./mysql:/var/lib/mysql // 볼륨(컨테이너가 죽어도 데이터를 유지)
               restart: always // 컨테이너가 죽지 않고 계속
               environment: // 환경변수
                 MYSQL_ROOT_PASSWORD: wordpress
                 MYSQL_DATABASE: wordpress
                 MYSQL_USER: wordpress
                 MYSQL_PASSWORD: wordpress
   4. volumes
       - container의 데이터 휘발성 때문에 데이터를 container가 아닌 호스트에 저장할 때
      - ``` docker
        services:
          nginx:
            image: nginx
            ports:
              - 8080:80
            volumes:
              - ./:/usr/share/nginx/html
            link:
              - mysql:db
   5. link
      - docker cli 사용할 때 --link와 동일하다. {연결할 컨테이너 이름}:{해당 컨테이너에서 참고할 이름}
      - 그러나 기본적으로 docker-compose 내부에선 모든 컨터네이너가 소통할 수 있기 때문에 사용되지 않음.
      - ``` docker
        services:
          nginx:
            image: nginx
            ports:
              - 8080:80
            link:
              - mysql:db
   6. depends_on
      - 의존성 명시다. 예를 들어 아래와 같이 써 있다면, nginx는 mysql에 의존하고 있으므로 mysql 이미지가 먼저 실행된 후에 그 다음에 nginx 이미지가 실행된다.
      - ``` docker
        services:
          mysql:
            image: mysql
            ...
          nginx:
            image: nginx
            ports:
              - 8080:80
            depends_on:
              - mysql
# 참고
- https://docs.docker.com/compose/compose-file/
- https://darrengwon.tistory.com/793
- https://0902.tistory.com/6