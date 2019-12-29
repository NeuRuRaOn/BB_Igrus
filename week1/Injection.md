# week1
## HTML injection
환경구축을 시켜둔 kali에서 Desktop으로 cd해준 후 gedit test.html 명령을 해줍니다.
gedit은 편리한 editor로 사용됩니다.
~~~html
<html>
<head>
<title>Test Title</title>
<body>
<h1>Hellow World1</h1>
<h2>Hellow World2</h2>
Hellow World
</body>
</html>
~~~
html은 위와 같이 구성됩니다. 이러한 html을 악의적으로 구성하여 취약한 매개변수에 삽입하는 공격을
"HTML 인젝션"이라고 부릅니다.
서버와 클라이언트가 통신을 할 때 사용하는 언어가 다릅니다. client의 경우에 html과 javascript를 사용합니다.
server는 php, jsp,asp 등을 사용합니다. 옛날에는 웹페이지가 정적으로 보였습니다. 여기서 정적이란
모든 사람에게 똑같이 보였다는 의미입니다. 그러나 현재는 사람마다 다른 형태의 사이트를 보여줘야되었고
javascript나 서버를 사용할 필요가 있게 되었습니다.
bee box의 html injection(get) 실습의 경우 성과 이름을 입력하는 창이 뜹니다. 여타 injecton과 마찬가지로
여기 
~~~
<h1> head</h1>
~~~
입력하면 원래 이름이 뜨던 부분에 head가 //안에 있는 것을 볼 수 있습니다.
http를 요청할 때 일정한 형태를 가지고 요청을 하게 됩니다. http는 head와 body포함되게 됩니다. 
header에 url이 들어가게 됩니다. 
실습을 진행하기 위해 proxy 서버를 127.0.0.1 8080 port로 설정시킵니다. burp suite로 가서
127.0.0.1로 설정되어있는 것을 확인한 후 intercept server responce도 intercept을 허용시켜줍니다.
이때 request는 client에서 server로,responce는 server에서 client를 의미합니다.
버프 수트를 사용하면 이름과 성으로 넘긴 값들을 확인하는 것이 가능하게 됩니다. 이때 ascii code값으로 확인가능합니다.
~~~html
<html>
<head>
<title>Test Title</title>
<body>
<h1>Hellow World1</h1>
<h2>Hellow World2</h2>
Hellow World
</body>
</html>
  <script>alert("TEST")</script>
~~~
라고 적은 후 이를 이름이나 성에 넣으면 TEST라는 alert가 발생합니다. 이미지를 가져오는 것도 가능합니다.
~~~
<img src=http://192.168.93.128/bWAPP/images/bee_1.png></img>
~~~
위의 code를 injection해줄 시 이미지를 불러올 수 있습니다.
단계를 medium으로 높여주면 앞에서의 방법이 통하지 않습니다.
html의 code를 확인해보면 security level이 올라간 것을 확인가능합니다. security level은 cookie에 명시되어 있습니다.
low일시에는 security level=0, medium일시에는 security level=1, high일 때는 2가 됩니다. 보안 단계를 표현하는 변수입니다.
low일 때는 no_check, medium일 때는 xss_check_1,high일 때는 xss_check_3를 사용하는 것을 알 수 있습니다.
xss_check_1에선 <와 >을 다른 값으로 바꾸어 줍니다. 하지만 애초에 아스키 값으로 encoding해서 보내주면 우회가 가능한 
간단한 보호기법입니다.
high level에서는 특수기호를 모두 처리해주는 것을 확인가능합니다. 따라서 아스키 값으로 encoding하더라도 막히게 됩니다.
high에서는 예시를 보여주지 않네요? 띠용 나중에 제가 한번 시도해보는 걸로 하겠습니다. 
우선 강의는 post 방식으로 넘어갑니다. tag 방식은 출력이 되는 것을 확인했습니다. post 방식은 get 방식과 달리
body 부분을 통해 정보를 전달하게 되네요. 길이도 측정이 되네요. pwnable에서와 마찬가지로 길이를 체크해주는 것이 중요하네요.
blog로 넘어갑니다. blog는 정보를 게시하는 방식에서 사용되는 방식이라고 합니다. 악의적인 사용자가 server에 악의적인 것을 저장시켜둔 후
여러 사용자의 것을 확인가능한 굉장히 큰 영향을 미치는 공격입니다. 앞의 두 방식과는 달리 stored 방식입니다.
reflected(get,post)는 입력된 값을 바로 바꾸었다면 stored는 조금 다릅니다. html code를 확인하면 sqli_check라는 다른 check를 사용하는 것을 알 수 있습니다. db에 저장될 때는 <,>를 체크하지 않지만 출력할 때 체크됩니다.

## 기타 injection
### iframe injection
* iframe은 html에 포함되어 있어 굉장히 유사합니다. 
* iframe은 한줄을 추가함으로써 대량의 source code를 불러오는 것이 가능
* robots.txt file - bot들이 정보를 긁어보는 것(crawling)을 허용할 것인지 허용하지 않을 것인지를 결정하는 문서
* iframe 실습 url을 보면 robots.txt를 iframe을 통해 불러오는 것을 확인가능
* 따라서 html을 통해서도 injection 가능합니다. 
~~~
http://192.168.93.128/bWAPP/iframei.php?ParamUrl=robots.txt%22width=%22250%22%3E%3C/iframe%3E%3Ciframe%20src=http://192.168.93.128/bWAPP/htmli_get.php%20%20width=%22250%22%3E%3C/iframe%3E&ParamWidth=250&ParamHeight=250
~~~
url을 위와 같이 변경하면 페이지가 작게 뜨는 것을 확인이 가능합니다. 신기하네요. 위의 url에 뿐만 아니라 source code(alert로 확인함)을 불러올수 있습니다. 얘들도 html code를 보면 xss_check 보호를 사용하는 것이 확인가능하네요. xss 보호기법은 굉장히 기본적이다는 걸 느끼네요.
* high에선 중간에 끼워넣은 code들은 실행이 되는 과정에서 사라지는 것을 확인할 수 있습니다.
### OS Command Injection
* commandi.php 파일에서 shell_exec를 통해 nslookup이 실행되는 것을 확인가능
* shell_exec은 위험한 함수이므로 보호되어야 함
* DNS LOOK UP 창을 마치 리눅스에서 shell과 같이 사용가능한 것을 볼 수 있습니다. 실행권한이 주어지는 shell_exec을 통해서인 듯합니다
* escapeshellcmd- 특수기호를 배제시켜 실행되는 것을 막아주는 함수
### PHP Code injection
* this is just a test page, reflecting back your message라는 문구를 확인가능
* source code를 보면 message가 버튼이라는 것을 확인
* bee box로 돌아와서 phpi.php를 확인하면 eval라는 함수를 볼 수 있음
~~~
<?php
echo "NeuRu RaOn";
@eval("echo \"Neuru Raon\";");
?>
~~~
* 위의 php 파일을 만들어줍니다. bwapp에서 실행하여 출력이 동일한 것을 확인했습니다.
* message를 통해 php code를 직접 작성하는 것이 가능
* 아래와 같이 system 함수까지 실행이 가능해집니다.
~~~
<?php
echo "NeuRu RaOn";
@eval("echo \"Neuru Raon\"; system('ls');");
?>
~~~
* shell을 연결하는 것도 가능함- nc를 통해 연결하여 사용합니다.
* 리눅스에서 -> nc -l -p 666 // 듣고 있는 상태로 만들어줍니다.
* 이제 url에서 신호를 보내도록 해주면 kali에서 명령어를 넣을 수 있는 상태로 만들어준 것을 볼 수 있네요.
### SSI injection
* server side includes의 약자입니다. shtml이라는 확장자를 사용하네요
* <!--#echo var="DATE_LOCAL" --> 을 name 부분에 넣어주면 date를 알려주는 것을 확인가능함
* 원리를 설명하는데 뭔가 주먹구구식이라 따로 정리가 필요함
## SQL 기초
* 데이터 베이스에 대해 정리하는 강의 - 간단히 정리하고 넘어가겠습니다.
* mysql이 돌아가고 있습니다. 
* mysql에 접속할 때는 mysql -u root -p 로 관리자 권한으로 접속하는 것이 좋습니다.
* 하나의 data base에는 여러 개의 table를 형성하는 것이 가능
* 하나의 mysql에는 또 여러개의 data base가 있음
* select는 조회할 때 , insert는 삽입할 때 사용해줌
* select + where 를 통해 조건을 부여하여 조회가 가능
* select + limit 레코드 수에 제한을 줄 수 있음
* where +union select에서 where에 거짓값을 넣어주면 union 값만 출력됨
* information_schema에서의 정보를 빼오는데 union select를 사용함
## SQL Injection 기초
* sql injection-사용자가 입력한 값을 검증하지 않고 데이터베이스 쿼리 일부분으로 포함될 때 발생하는 취약점
* client -(portal)-> web -(sql)->database 와 같은 과정을 거치게 됨//저기 web에서 database로 전달되는 sql를 변조하여 공격
* where no=3 뒤에  or 1=1을 더해서 모두 true가 되어 모든 값을 출력하게 함
* where no=0 union select 1,2,3, @@oversion; 을 통해 1,2,3의 값들을 확인가능 (union이 뒤의 값들을 확인하는 것을 가능하게 해줌)
* 앞에서도 말했듯이 information schema는 굉장히 많은 내용을 담고 있음
* information_schema 내에는 함수들도 저장되어 있기에 굉장히 중요
* 주석을 처리하는 것을 배워야 하는데 이는 원래 전달되는 url에서 사용하지 않아야 할 부분은 주석처리할 수 있기 때문입니다.
* 주석처리를 해주지 않으면 오류가 발생하게 됩니다.
* search 창에 ' 1=1 # 를 inject 해줌으로써 모든 영화의 정보가 나열되도록 했습니다.
* "' union select 1,2,3,4,5,6,7 # 또는 0' union select 1,2,3,4,5,6,7 # 처럼 없는 값을 넣어주고 실행하면 각 num가 어는 부분을 관리하는지 확인할 수 있습니다.
* 시스템 변수 및 함수
  * database()-DB명을 알려주는 함수
  * user()-현재 사용자의 아이디
  * system_user()-최고 권한 사용자의 아이디
  * @@version-db server의 버전
  * @@datadir-db server가 존재하는 디렉토리
* 0'union select 1,table_name,3,4,5,6,7 from information_schema.tables # 을 집어넣으면 information_schema내의 table 정보를 출력해주네요
* linux에서 hash-identifier를 통해 어떤 hash가 사용되었는지 구분할 수 있습니다.
* sqli_check 보호기법에 대해 확인해보면 mysql_real_escape_string을 사용하고 있는 것을 알 수 있었습니다.
* mysql_real_escape_string - 취약한 글자들을 백 슬레시를 붙여서 처리해주는 함수
* sql injection의 get/select 방식에서는 limit를 통해 정보를 제한해야 원하는 정보를 얻을 수 있습니다.
~~~
http://192.168.93.128/bWAPP/sqli_2.php?movie=6%20or%201=1%20limit%201,1%20#&action=go
~~~
* fetch object 함수를 사용하면 secure coding이 가능(미리 sql 구문이 어떻게 생겼는지 web이 미리 보내놓은 후 이와 다른 값이 보내질 경우 무시해버립니다)
* sql injection(post/search) 방식의 경우 앞에서와 마찬가지로 url을 통해 확인하지 못합니다(post는 body를 통하기 때문)
* burp suite를 통해 body 부분을 잡은 후 or 1=1 # 를 더해주면 모든 목록을 확인이 가능합니다.
## SQL injection(sqlmap 과 metasploit 활용)
### sqlmap
* sqlmap의 활용
  * mysql,oracle,postgresql,microsoft sql server 등의 관리시스템을 지원
  * boolean-based blind,time-based blind, error-based, union query-based와 같은 sql-injection  기술을 지원
  * 또한 여러가지 활동을 합니다. blog.naver.com/isc0304/220372379862 참고
* -p TESTPARAMETER : 테스트할 파라미터 값을 알려줍니다
* ifnull()은 null이라면 앞의 값을 아니라면 뒤의 값을 출력해줍니다
* dbs를 통해 어떤 database들이 돌아가고 있는지 확인할 수 있습니다.
* sql map은 블로그를 보고 다시 정리하는 시간이 필요할 듯요
* select(sleep(5))을 통해 thread를 잠재워 버리기 때문에 이런 testing 방식은 조심해야 합니다.
* 따라서 timebase test는 빼고 하는 것이 좋습니다.
### metasploit
* bwapp-sqli inurl : github를 구글에 검색 후 첫번째 뜨는 github link에 접속하여 시작
* 주소를 github에서 얻은 후 kali에 wget이라고 치고 뒤에 복사한 주소를 넣어줍니다 -> 그러면 저장이 됩니다.
* 그리고 cp를 bwapp-sqli.rb /usr/share/metasploit-framework/modules/exploits/multi/http/bwapp-cmdi.rb 해줍니다.
* msfconsole(metasploit를 사용해주게 해주는 shell)를 open해줍니다.
* 이제 searcj bwapp을 하면 존재할텐데 여기서 name 부분의 값을 복사 후 use 뒤에 붙여넣기 해주면 됩니다.
* 위의 과정을 통해 cmdi를 사용하기 위한 설정 과정이 끝났습니다.
* CHECK을 통해 없는 것이나 취약한 것을 찾아줍니다
* set RHOST 192.168.93.128(BEEBOX 주소)를 통해 RHOST를 설정시켜줄 수 있습니다.
* run을 하게 되면 metapreter가 붙게 되는데 이는 흔히 얘기하는 backdoor 역할을 합니다.
## blind sql injection
* blind sql injection은 쿼리의 결과를 함과 거짓으로만 출력하는 페이지에서 사용하는 공격입니다.
* 마치 장님이 길을 하나하나 짚고 가는 듯이 페이지의 반응을 확인하여 데이터베이스의 내용을 추측하는 공격
* 원하는 데이터의 길이를 먼저 확인 (length 함수를 사용)
* 다음 원하는 데이터의 스트링을 확인하여 유추( substr, substring,right, left와 같이 string을 비교해주는 함수를 사용)
* blind sql 기초구문 [[ascii(substr((select table_name from information_schema.tables where table_type='base table' limit 0,1),1,1)); ]]
* select length(select table_name form tables limit 0,1)) =14;와 같이 원하는 데이터의 길이가 입력한 값(14)와 동일한지 주어지는 true(1)와 false(0)를 통해 확인
* substr은 하나씩 가져와서 비교하는 것이 가능해집니다.
* blindsql은 수동으로 안합니다. 보통은 python을 사용합니다.
* sql-injection - blind - boolean-based를 보면 참일때는 exist, 거짓일때는 not exist가 발생하는 것을 알 수 있습니다.
* timebased는 앞에서 설명했듯이 거짓일 때는 시간이 안 걸리고 반환이되고 사실일 때는 시간이 걸려서 반환이 되게 됩니다. -> 시간이 우리가 받을 수 있는 반응

