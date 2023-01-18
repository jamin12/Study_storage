# OS command Injection
- 시스템의 명령어 쿼리문에 주입하여 서버 운영체제에 접근하는 공격 즉, 웹페이지에 시스템 명령어를 주입하여 쉘을 획들하는 공격이다.
- 다양한 웹 애플리케이션 제작용 언어는 시스템에 내장되어있는 프로그램들을 호출할 수 있는 함수를 지원한다.
- PHP(system), Node.js(child_process), python(os.system)
## 발생할 수 있는 문제들
- 전체 시스템 탈취
- 서비스거부
- 서버 내 중요한 정보유출
- 다른 시스템에 대한 공격용 패드
- Botnet또는 cryptomining을 위한 시스템으로 전락
## 예방하기
1. ### OS command를 실행시키지 말기
   - 아예 사용하지 않는것을 권장
   - os command대신 3rd party의 라이브러리를 사용하는것을 추천
   - ```
        rm 대신 java.nio.file.Files.deleteIfExists(file),

        cp대신 java.nio.file.Files.copy(source, destination)
2. ### 최소한의 권한으로만 실행하기
   - 피치못하게 사용해야할 경우 실행 권한을 root가 아닌 제한된 권하는 가진 user로 실항하게 한다.
3. ### command를 shell interpreter를 통해서 실행시키지 말것
   - sh, bash등 이런 interpreter로 command를 돌리게 되면 원래 목적과는 맞지 않는 명령어들을 실행할 가능성이 있다.
   - rm -rf를 command injection했다고 했을 때,
   - ``` 
        /bin/sh -c "/bin/rm/var/app/logs/x;rm –rf /" -> 파이프 구문이 실행됨
   - rm바이너리 파일로 명령어를 실행시킨다면
   - ```
        /bin/rm /var/app/logs/x;rm -rf / -> 실행 안됨(rm 명령어 포맷에 맞지 않기 때문)
4. ### system command를 실행할 때 안전한 funtion사용하기
   - ```
        Runtime.getRuntime().exec("/usr/bin/nmap " + ipAddress);
   - 개발자가 의도하지 않는 파이프를 통해 여러 malicious한 코드를 적으면 실행이 된다.
   - ```
        Runtime.getRuntime().exec(new String[]{"/usr/bin/nmap",ipAddress});
   - 두 개의 배열(“/usr/bin/nmap”, “ipAddress”)로만 인식되어 여러 malicious코드를 실행시킬 가능성을 배제시킬 수 있습니다.
5. ### 사용자의 입력이 command실행에 영향을 끼치지 못하게 하기
   - 예들들어 파일을 삭제하는 액션을 설계해야하는 경우, 유저와 파일으 ㅣ매핑테이블 같은 것을 만들어서 파일의 id만을 입력하게 한다.
   - ``` 
        realName = getRealFileName(fileID);
        Runtime.getRuntime().exec(new String[]{"/bin/rm","/var/app/logs/"+realName});
6. ### input vaildation(Whitelist)사용
   - blacklist로는 기상천외한 공격들을 완벽하게 막을 수 없기 때문에 whitelist를 사용하는 것을 강력하게 권고한다.
# 참고
- https://learn.dreamhack.io/187#2
- https://gruuuuu.github.io/security/injection/