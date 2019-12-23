# week1
## injection
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
http를 요청할 때 일정한 형태를 가지고 요청을 하게 됩니다. http는 head와 body 그리고
url이 포함되게 됩니다.
