# Spring + MySql

### 둘이 연결 함 해보다가 하루 종일 걸려서 기록해봅니다..

---

## MySql Root 비밀번호 분실 시 (v.8.0.28 기준)

1. 일단 버전 확인하기 (버전마다 명령어가 다르더라..)
2. 터미널에서 `mysql.server start --skip-grant-tables` 로 시작
3. `mysql -u root`로 비밀번호 없이 로그인 가능
4. `use mysql;`
5. `select user, host, authentication_string, plugin from user;`로 확인
6. `alter user root@% identified with mysql_native_password by 'password'`
   - 처음에는 이 명령어가 안먹혔다.
   - `update mysql.user set authentication_string=null where user='root';`
   - `update mysql.user set authentication_string=n'password' where user='root';`
   - 이거를 중간에 하고 5번을 하니까 된거다... 왜 된건지 모르겠음,,
7. 혹시 모르니까 `flush privileges;` 입력

---

## MySql 계정 생성 및 권한 부여

1. `create user '계정 이름'@localhost identified by '비밀번호';`
2. - 일부 권한 부여
     - `grant all privileges on '스키마 이름'.* to '계정이름'@localhost with grant option;`
   - 모든 권한 부여
     - `grant all privileges on *.* to '계정 이름'@localhost with grant option;`
3. 권한 확인
   - `show grants for '계정 이름'@localhost;`
