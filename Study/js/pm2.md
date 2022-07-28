
# pm2
## __<span style="color:#9999ff">정의</span>__
- pm2는 node.js 어플리케이션을 쉽게 관리할 수 있게 해주는 Process Manager이다. node.js 어플리케이션을 cluter mode로 실행시킨다거나, 메모리가 넘친다거나, 오류로 인해 프로세스가 종료되는 등의 상황에 직면했을 때 각각의 상황을 사용자가 모두 신경 써서 처리해줄 수도 있지만, 너무 복잡하고 신경 써야할 일들이 많아진다.
- pm2의 cluster 모드는 node.js의 cluter module을 이용해 기본적으로 싱글 스레드인 nodejs를 멀티 스레드로 구동시켜 준다.
## __<span style="color:#9999ff">간단 활용</span>__
- **자세한 내용은 공식 홈페이지를 통해 알아보자**
```
pm2 start <app>

# 앱 이름 설정
--name <app_name>

# 파일이 수정될 때 자동 재시작
--watch

# 메모리 임계값 설정
--max-memory-restart <200MB>

# 로그파일 지정
--log <log_path>

# 자동 재시작 사이의 지연
--restart-delay <delay in ms>

# 로그 앞에 시간표시
--time

# 자동 재시작 제한
--no-autorestart

# pm2 log를 들어갈 필요 없이 바로 로그를 확인 가능
--no-daemon

#  재시작
pm2 restart app_name 
pm2 reload app_name 
# 정지
pm2 stop app_name 
# 삭제
pm2 delete app_name 

# 현재 프로세스 저장
pm2 save

# 컴퓨터 재시작시 자동으로 프로세스 시작
pm2 startup

# 이전에 저장한 프로세스들 실행
pm2 resurrect

```
- 클러스터 모드
- ```
    module.exports = {
    apps : [{
        script    : "api.js",
        instances : "max",
        exec_mode : "cluster"
      }]
    }
    ```
- 멀티 스레드로 사용하고 싶으면 cluster모드를 사용하면 된다.

# 참고
- https://armadillo-dev.github.io/javascript/nodejs/node-js-pm2/
- https://pm2.keymetrics.io/docs/usage/quick-start/