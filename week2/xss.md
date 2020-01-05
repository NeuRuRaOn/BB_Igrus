# week2
## 크로스 사이트 스크립팅(stored)
* html에서 사용되는 모든 tag들을 막을 수 없기 때문에 발생하는 취약점
* tag를 모두 제거하면 굉장히 정적이기 때문에 tag를 사용할 수 밖에 없음
* blog와 같은 형태를 뛰고 있을 때 취약한 사이트(low level)은 tag를 넣어주면 명령어 실행이 가능
* user-agent switcher란 웹 브라우저를 원하는 형태로 넘겨주는 것이 가능하게 해줌
* user agent 실습에서는 user agent를 바꾸어 주면 injection이 가능하게 됨
## 크로스 사이트 스크립팅(reflected)
* 악의적인 사용자가 희생자에게 메일이나 게시물을 등록해서 사용자가 직접 실행하도록 유행하는 것이 stored와 다름
* stored는 실행을 악의적 사용자가, reflected는 희생자가 함
* 주로 실행이 넣는대로 실행되는지 확인한 후에 악의적 code를 넣는 형태로 진행됨
* json 실습에서는 넣는 부분에 script를 한번 닫아준 후 다시 열어서 원하는 명령어를 넣는 형식
* ajax의 경우 script를 바로 넣어도 실행되지 않았다
  * 이때 ajax는 실시간으로 데이터를 확인하는 역할을 함
  * 원하는 형태로 script를 넣는 것은 안 되나 막고 있지 않은 <img> tag를 이용하면 삽입이 가능
  * img tag에 onerror를 설정시켜주면 script가 가능하다(ex onerror='alert(1)')
* eval
  * eval의 경우 url을 건드리게 됨
  * eval 함수 내에 자신이 원하는 명령어를 넣으면 sql injection과 같이 작동함
* bbcode
  * bbcode 실습에 들어가면  cve가 있는데 bbcode tag를 통해 scripting이 가능하다는 것을 보여줌
* php-self
  * 다른 것과 마찬가지로 넣은 script가 실행됨
  * php-self란 서버가 가지고 있는 변수로 서버 단위로 가지고 있음. 전역변수로 사용됨
  * php-self가 form 내에서 echo되고 있음
  * 여기서 go 버튼을 부르면 php_self가 정의되어있는 php-self.php가 진행됨
  * /xss_php_self.php/"/><script>alert(1)</script> 를 입력하면 실행됨(신기)
사실 alert 창을 보는 것이 중요한 것이 아니라 alert창이 뜨는 것을 통해서 명령어가 실행이 가능해 정보를 가져올 수 있다는 것을 이해해야 함
