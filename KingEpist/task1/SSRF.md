# SSRF란?
* Server-side request forgery
* 공격자가 server-side application이 arbitrary domain에 HTTP request를 보내도록 유도하는 것을 허용하는 취약점
* server가 server 자체에 연결하거나 다른 web-based service나 타사의 system에 연결하는 것을 가능하게 함
# SSRF의 영향
* arbitrary command execution이 공격자가 가능하게 됨
# SSTF 공격(server 자체를 대상)
* application이 application을 hosting하고 있는 서버 자체에 다시 HTTP request을 보내도록 유도하는 공격(lookback network interface를 통해)
* 127.0.0.1과 같은 hostname이나 localhost를 url에 포함시키는 것을 포함
* 예를 들어 
~~~
POST /product/stock HTTP/1.0
Content-Type: application/x-www-form-urlencoded
Content-Length: 118

stockApi=http://stock.weliketoshop.net:8080/product/stock/check%3FproductId%3D6%26storeId%3D1
~~~
    이러한 값이 쇼핑몰 사이트에서 넘어갈 때 attacker가 이 request를 수정하는 것이 가능하다.
~~~
POST /product/stock HTTP/1.0
Content-Type: application/x-www-form-urlencoded
Content-Length: 118

stockApi=http://localhost/admin
~~~
여기서 서버는 /admin url의 content를 fetch하고 그것을 user에게 return 할것이다.
이 방법을 사용함으로써 /admin url이 local machine 그 자체로부터 올 수 있다.

참조:https://portswigger.net/web-security/ssrf
