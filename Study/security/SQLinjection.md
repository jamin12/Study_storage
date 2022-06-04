# SQLinejction
## __<span style="color:#9999ff">SQL 개념</span>__
- 관계형 데이터베이스 관리 시스템의 데이터를 관리하기위해 설계된 특수 목적의 프로그래밍 언어
- 대다수 상용 또는 공개 데이터베이스 관련 프로글매은 SQL을 표준으로함

## __<span style="color:#9999ff">SQL 구분</span>__
- 데이터 조작 언어(data manipulation language, DML)
  - |SQL DML|설명|
    |---|---|
    |SELECT|데이터베이스에서 데이터를 추출한다.|
    |UPDATE|데이터베이스에 있는 데이터를 수정한다.|
    |DELETE|데이터베이스에서 데이터를 삭제한다.|
    |INSERT|데이터베이스에 새로운 데이터를 추가한다.|
- 데이터 정의 언어(data definition Language, DDL)
  - |SQL DML|설명|
    |---|---|
    |CREATE DATABASE|새로운 데이터베이스를 생성한다.|
    |ALTER DATABASE|데이터베이스를 수정한다.|
    |CREATE TABLE|새로운 테이블을 만든다.|
    |ALTER TABLE|테이블을 수정한다.|
    |DROP TABLE|테이블을 삭제한다.|
    |CREATE INDEX|인덱스(검색 키)를 만든다.|
    |DROP INDEX|인덱스를 삭제한다.|
- 데이터 제어 언어
  - |SQL DML|설명|
    |---|---|
    |GRANT|데이터베이스에 대한 사용자의 엑세스 권한을 제공.|
    |UPDATE|GRANT 명령으로 주어진 엑세스 권한을 철회.|

## __<span style="color:#9999ff">SQL인젝션 공격의 원리</span>__
- __<span style="color:#ff9933">SQL인젝션 공격의 원리</span>__
  - SQL인젝션 공격에 취약점이 발생하는 곳은 웹 어플리케이션과 데이터베이스가 연동되는 부분에 공격자가 임의의 sql 명령어를 삽입하여 공격
  - 보통 사용자 로그인 부분, 게시물 검색 부분, 우편번호 검색 부분, 자료실 등이 대표적
- __<span style="color:#ff9933">예시</span>__
  - SELECT * FROM user_data WHERE last_name = ''**or''=''**
