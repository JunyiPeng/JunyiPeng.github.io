---
title: 理解 CORS (Cross-Origin Resource Sharing)
category: 搬砖码农
date: 2016-05-15 23:22:25
tags: 
- http
- cors
---

## 存在即是合理
> 首先理解一下「跨站HTTP请求」

跨站HTTP(Cross-Site HTTP Reuqest)请求，是指发起请求的资源所在域不同于该请求所指向资源所在域的HTTP请求。
比如说，域名A(`http://domaina.example`)的某 Web 应用程序中通过`<img>`标签引入了域名B(`http://domainb.foo`)站点的某图片资源(`http://domainb.foo/image.jpg`)，域名A的那 Web 应用就会导致浏览器发起一个跨站 HTTP 请求。
在现在的Web开发中，使用跨站HTTP请求加载各类资源(包括CSS、图片、JavaScript脚本以及其他类资源)，已经成为了一种普遍且流行的方式。

> 出于安全考虑，浏览器会限制脚本中发起的跨站请求

比如，使用 XMLHttpRequest 对象发起 HTTP 请求就必须遵守同源策略。 具体而言，Web 应用程序能且只能使用 XMLHttpRequest 对象向其加载的源域名发起 HTTP 请求，而不能向任何其它域名发起请求。
要注意的是，跨域并非浏览器限制了发起跨站请求，而是跨站请求可以正常发起，但是返回结果被浏览器拦截了。最好的例子是CSRF跨站攻击原理，请求是发送到了后端服务器无论是否跨域。


## 理解同源策略

[Cross-Site Sharing Standard](https://www.w3.org/TR/cors) 是W3C推荐的一种机制，让Web应用服务器能支持跨站访问控制，从而使得安全地进行跨站数据传输成为可能。下面截取其中「[Syntax](https://www.w3.org/TR/cors/#syntax)」章节，来说明一下请求与响应的规范。


> For Response

### Access-Control-Allow-Origin Response Header
该请求头表示所请求的资源是否接受来自指定origin(根据 Origin Request Header)的请求.
```
Access-Control-Allow-Origin: <origin> | *
```
举个栗子,允许来自 http://baidu.com 的请求,你可以这样指定:
```
Access-Control-Allow-Origin: http://baidu.com
```

### Access-Control-Allow-Credentials Response Heade
告知客户端,当请求的credientials属性是true的时候,响应是否可以被得到.当它作为预请求的响应的一部分时,它用来告知实际的请求是否使用了credentials.注意,简单的GET请求不会预检,所以如果一个请求是为了得到一个带有credentials的资源,而响应里又没有Access-Control-Allow-Credentials头信息,那么说明这个响应被忽略了.
```
Access-Control-Allow-Credentials: true | false
```

### Access-Control-Expose-Headers Response Header
设置浏览器允许访问的服务器响应请求的头信息的白名单:
```
Access-Control-Expose-Headers: X-A-Custom-Header, X-B-Custom-Header
```
这样，浏览器就可以得到服务器响应请求的头信息中的` X-A-Custom-Header`、` X-B-Custom-Header`

### Access-Control-Max-Age Response Header
这个头告诉我们这次预请求的结果的有效期是多久：
```
Access-Control-Max-Age: <delta-seconds>
```
`delta-seconds` 参数表示,允许这个预请求的参数缓存的秒数,在此期间,不用发出另一条预检请求. 

### Access-Control-Allow-Methods Response Header
指明资源可以被请求的方式有哪些(一个或者多个). 这个响应头信息在客户端发出预检请求的时候会被返回.
```
Access-Control-Allow-Methods: <method>[, <method>]*
```

### Access-Control-Allow-Headers Response Header
也是在响应预检请求的时候使用.用来指明在实际的请求中,可以使用哪些自定义HTTP请求头.比如
```
Access-Control-Allow-Headers: X-Custom-Header
```
这样在实际的请求里,请求头信息里就可以有这么一条:
```
X-Custom-Header: hello world
```

> For Request

### Origin Request Header
表明发送请求或者预请求的域
```
Origin: <origin>
```
参数`origin`是一个URI,告诉服务器端,请求来自哪里.它不包含任何路径信息,只是服务器名.

### Access-Control-Request-Method Request Header
在发出预检请求时带有这个头信息,告诉服务器在实际请求时会使用的请求方式
```
Access-Control-Request-Method: <method>
```

### Access-Control-Request-Headers
在发出预检请求时带有这个头信息,告诉服务器在实际请求时会携带的自定义头信息.如有多个,可以用逗号分开.
```
Access-Control-Request-Headers: <field-name>[, <field-name>]*
```


## 举三个栗子
### 一个简单的请求
比如说，假如站点 `http://foo.example` 的网页应用想要访问 `http://bar.other` 的资源。以下的 JavaScript 代码应该会在 foo.example 上执行：
```
var invocation = new XMLHttpRequest();
var url = 'http://bar.other/resources/public-data/';
   
function callOtherDomain() {
  if(invocation) {    
    invocation.open('GET', url, true);
    invocation.onreadystatechange = handler;
    invocation.send(); 
  }
}
```
让我们看看，在这个场景中，浏览器会发送什么的请求到服务器，而服务器又会返回什么给浏览器：
```
GET /resources/public-data/ HTTP/1.1
Host: bar.other
User-Agent: Mozilla/5.0 (Macintosh; U; Intel Mac OS X 10.5; en-US; rv:1.9.1b3pre) Gecko/20081130 Minefield/3.1b3pre
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
Accept-Language: en-us,en;q=0.5
Accept-Encoding: gzip,deflate
Accept-Charset: ISO-8859-1,utf-8;q=0.7,*;q=0.7
Connection: keep-alive
Referer: http://foo.example/examples/access-control/simpleXSInvocation.html
Origin: http://foo.example


HTTP/1.1 200 OK
Date: Mon, 01 Dec 2008 00:23:53 GMT
Server: Apache/2.0.61 
Access-Control-Allow-Origin: *
Keep-Alive: timeout=2, max=100
Connection: Keep-Alive
Transfer-Encoding: chunked
Content-Type: application/xml

[XML Data]
```
第 1~10 行是 浏览器 发出的请求头。注意看第10行的请求头 Origin，它表明了该请求来自于 `http://foo.exmaple`。

第 13~22 行则是 `http://bar.other` 服务器的响应。如第16行所示，服务器返回了响应头 Access-Control-Allow-Origin: *，这表明服务器接受来自任何站点的跨站请求。如果服务器端仅允许来自 `http://foo.example` 的跨站请求，它可以返回：
```
Access-Control-Allow-Origin: http://foo.example
```
现在，除了 `http://foo.example`，其它站点就不能跨站访问 `http://bar.other` 的资源了。

如上，通过使用 Origin 和 Access-Control-Allow-Origin 就可以完成最简单的跨站请求。不过 Access-Control-Allow-Origin 需要为 * 或者包含由 Origin 指明的站点。

### 预请求 Prefilght
不同于上面讨论的简单请求，“预请求”要求必须先发送一个 OPTIONS 请求给目的站点，来查明这个跨站请求对于目的站点是不是安全可接受的。这样做，是因为跨站请求可能会对目的站点的数据造成破坏。 当请求具备以下条件，就会被当成预请求处理：

* 请求以 GET, HEAD 或者 POST 以外的方法发起请求。或者，使用 POST，但请求数据为 application/x-www-form-urlencoded, multipart/form-data 或者 text/plain 以外的数据类型。比如说，用 POST 发送数据类型为 application/xml 或者 text/xml 的 XML 数据的请求。
* 使用自定义请求头（比如添加诸如 X-PINGOTHER

如示例：
```
var invocation = new XMLHttpRequest();
var url = 'http://bar.other/resources/post-here/';
var body = '{C}{C}{C}{C}{C}{C}{C}{C}{C}{C}Arun';
    
function callOtherDomain(){
  if(invocation)
    {
      invocation.open('POST', url, true);
      invocation.setRequestHeader('X-PINGOTHER', 'pingpong');
      invocation.setRequestHeader('Content-Type', 'application/xml');
      invocation.onreadystatechange = handler;
      invocation.send(body); 
    }

......
```
如上，以 XMLHttpRequest 创建了一个 POST 请求，为该请求添加了一个自定义请求头(X-PINGOTHER: pingpong)，并指定数据类型为 application/xml。所以，该请求是一个“预请求”形式的跨站请求。

让我们看看服务器与浏览器之间具体的交互过程：
```
OPTIONS /resources/post-here/ HTTP/1.1
Host: bar.other
User-Agent: Mozilla/5.0 (Macintosh; U; Intel Mac OS X 10.5; en-US; rv:1.9.1b3pre) Gecko/20081130 Minefield/3.1b3pre
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
Accept-Language: en-us,en;q=0.5
Accept-Encoding: gzip,deflate
Accept-Charset: ISO-8859-1,utf-8;q=0.7,*;q=0.7
Connection: keep-alive
Origin: http://foo.example
Access-Control-Request-Method: POST
Access-Control-Request-Headers: X-PINGOTHER


HTTP/1.1 200 OK
Date: Mon, 01 Dec 2008 01:15:39 GMT
Server: Apache/2.0.61 (Unix)
Access-Control-Allow-Origin: http://foo.example
Access-Control-Allow-Methods: POST, GET, OPTIONS
Access-Control-Allow-Headers: X-PINGOTHER
Access-Control-Max-Age: 1728000
Vary: Accept-Encoding, Origin
Content-Encoding: gzip
Content-Length: 0
Keep-Alive: timeout=2, max=100
Connection: Keep-Alive
Content-Type: text/plain

POST /resources/post-here/ HTTP/1.1
Host: bar.other
User-Agent: Mozilla/5.0 (Macintosh; U; Intel Mac OS X 10.5; en-US; rv:1.9.1b3pre) Gecko/20081130 Minefield/3.1b3pre
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
Accept-Language: en-us,en;q=0.5
Accept-Encoding: gzip,deflate
Accept-Charset: ISO-8859-1,utf-8;q=0.7,*;q=0.7
Connection: keep-alive
X-PINGOTHER: pingpong
Content-Type: text/xml; charset=UTF-8
Referer: http://foo.example/examples/preflightInvocation.html
Content-Length: 55
Origin: http://foo.example
Pragma: no-cache
Cache-Control: no-cache

Arun


HTTP/1.1 200 OK
Date: Mon, 01 Dec 2008 01:15:40 GMT
Server: Apache/2.0.61 (Unix)
Access-Control-Allow-Origin: http://foo.example
Vary: Accept-Encoding, Origin
Content-Encoding: gzip
Content-Length: 235
Keep-Alive: timeout=2, max=99
Connection: Keep-Alive
Content-Type: text/plain

[Some GZIP'd payload]
```
第1至12行，使用一个 OPTIONS 发送了一个“预请求”。浏览器 根据请求参数，决定需要发送一个“预请求”，来探明服务器端是否接受后续真正的请求。 OPTIONS 是 HTTP/1.1 里的方法，用来获取更多服务器端的信息，是一个不应该对服务器数据造成影响的方法。 随同 OPTIONS 请求，以下两个请求头一起被发送：
```
Access-Control-Request-Method: POST
Access-Control-Request-Headers: X-PINGOTHER
```
请求头Access-Control-Request-Method可以提醒服务器跨站请求将使用POST方法，而请求头Access-Control-Request-Headers则告知服务器该跨站请求将携带一个自定义请求头X-PINGOTHER。这样，服务器就可以决定，在当前情况下，是否接受该跨站请求访问。

第15至27行是服务器的响应。该响应表明，服务器接受了客服端的跨站请求。具体可以看看第18至21行：
```
Access-Control-Allow-Origin: http://foo.example
Access-Control-Allow-Methods: POST, GET, OPTIONS
Access-Control-Allow-Headers: X-PINGOTHER
Access-Control-Max-Age: 1728000
```
响应头Access-Control-Allow-Methods表明服务器可以接受POST, GET和 OPTIONS的请求方法。请注意，这个响应头类似于HTTP/1.1 Allow: response header，但仅限于访问控制的场景下。而响应头Access-Control-Allow-Headers则表示服务器接受自定义请求头X-PINGOTHER。就像Access-Control-Allow-Methods一样，Access-Control-Allow-Headers允许以逗号分隔，传递一个可接受的自定义请求头列表。最后，响应头Access-Control-Max-Age告诉浏览器，本次“预请求”的响应结果有效时间是多久。在上面的例子里，1728000秒代表着20天内，浏览器在处理针对该服务器的跨站请求，都可以无需再发送“预请求”，只需根据本次结果进行判断处理。


### 附带凭证信息的请求
XMLHttpRequest和访问控制功能，最有趣的特性就是，发送凭证请求（HTTP Cookies和验证信息）的功能。一般而言，对于跨站请求，浏览器是不会发送凭证信息的。但如果将XMLHttpRequest的一个特殊标志位设置为true，浏览器就将允许该请求的发送。

`http://foo.example`站点的脚本向`http://bar.other`站点发送一个GET请求，并设置了一个Cookies值。脚本代码如下：
```
var invocation = new XMLHttpRequest();
var url = 'http://bar.other/resources/credentialed-content/';
    
function callOtherDomain(){
  if(invocation) {
    invocation.open('GET', url, true);
    invocation.withCredentials = true;
    invocation.onreadystatechange = handler;
    invocation.send(); 
  }
```
如你所见，第七行代码将XMLHttpRequest的withCredentials标志设置为true，从而使得Cookies可以随着请求发送。因为这是一个简单的GET请求，所以浏览器不会发送一个“预请求”。但是，如果服务器端的响应中，如果没有返回Access-Control-Allow-Credentials: true的响应头，那么浏览器将不会把响应结果传递给发出请求的脚本程序，以保证信息的安全。

客服端与服务器端交互示例如下：
```
GET /resources/access-control-with-credentials/ HTTP/1.1
Host: bar.other
User-Agent: Mozilla/5.0 (Macintosh; U; Intel Mac OS X 10.5; en-US; rv:1.9.1b3pre) Gecko/20081130 Minefield/3.1b3pre
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
Accept-Language: en-us,en;q=0.5
Accept-Encoding: gzip,deflate
Accept-Charset: ISO-8859-1,utf-8;q=0.7,*;q=0.7
Connection: keep-alive
Referer: http://foo.example/examples/credential.html
Origin: http://foo.example
Cookie: pageAccess=2


HTTP/1.1 200 OK
Date: Mon, 01 Dec 2008 01:34:52 GMT
Server: Apache/2.0.61 (Unix) PHP/4.4.7 mod_ssl/2.0.61 OpenSSL/0.9.7e mod_fastcgi/2.4.2 DAV/2 SVN/1.4.2
X-Powered-By: PHP/5.2.6
Access-Control-Allow-Origin: http://foo.example
Access-Control-Allow-Credentials: true
Cache-Control: no-cache
Pragma: no-cache
Set-Cookie: pageAccess=3; expires=Wed, 31-Dec-2008 01:34:53 GMT
Vary: Accept-Encoding, Origin
Content-Encoding: gzip
Content-Length: 106
Keep-Alive: timeout=2, max=100
Connection: Keep-Alive
Content-Type: text/plain


[text/plain payload]
```
虽然第11行指定了要提交到`http://bar.other`的内容的Cookie信息,但是如果bar.other的响应头里没有Access-Control-Allow-Credentials:true(第19行),则响应会被忽略. 特别注意: 给一个带有withCredentials的请求发送响应的时候,服务器端必须指定允许请求的域名,不能使用'*'.上面这个例子中,如果响应头是这样的:Access-Control-Allow-Origin: * ,则响应会失败. 在这个例子里,因为Access-Control-Allow-Origin的值是`http://foo.example`这个指定的请求域名,所以客户端把带有凭证信息的内容被返回给了客户端. 另外注意第22行,更多的cookie信息也被创建了

## 参考
[Access_control_CORS](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Access_control_CORS)
[Cross-Origin Resource Sharing Standard](https://www.w3.org/TR/cors/)
[Same-origin_policy](https://developer.mozilla.org/en-US/docs/Web/Security/Same-origin_policy)