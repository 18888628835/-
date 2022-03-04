# HTTP 状态码

## HTTP 状态码分类

| 分类  | 分类描述                                       |
| ----- | ---------------------------------------------- |
| 1\*\* | 信息，服务器收到请求，需要请求者继续执行操作   |
| 2\*\* | 成功，操作被成功接收并处理                     |
| 3\*\* | 重定向，需要进一步的操作以完成请求             |
| 4\*\* | 客户端错误，请求包含语法错误或无法完成请求     |
| 5\*\* | 服务器错误，服务器在处理请求的过程中发生了错误 |

## 常见的状态码

- 100 Continue 客户端应继续其请求
- 200 OK 请求成功。一般用于 GET 与 POST 请求
- 201 Created 已创建。成功请求并创建了新的资源
- 202 Accepted 已接受。已经接受请求，但未处理完成
- 204 No Content 无内容。服务器成功处理，但未返回内容。
- 301 Moved Permanently 永久重定向
- 302 Found 临时重定向
- 304 Not Modified 无修改。当命中协商缓存的时候会发送这个码
- 400 Bad Request 客户端请求的语法错误，服务器无法理解
- 401 Unauthorized 请求要求用户的身份认证
- 403 Forbidden 服务器理解请求客户端的请求，但是拒绝执行此请求
- 404 Not Found 服务器无法根据客户端的请求找到资源（网页）
- 414 Request-URI Too Large 请求的 URI 过长
- 500 Internal Server Erro 服务器内部错误，无法完成请求
- 501 Not Implemented 服务器不支持请求的功能
- 502 Bad Gateway 作为网关或者代理工作的服务器尝试执行请求时，从远程服务器接收到了一个无效的响应

临时重定向和永久重定向的区别：

比如你的网站从 HTTP 升级到了 HTTPS 了，以前的站点再也不用了，应当返回 301，这个时候浏览器默认会做缓存优化，在第二次访问的时候自动访问重定向的那个地址。
而如果只是暂时不可用，那么直接返回 302 即可，和 301 不同的是，浏览器并不会做缓存优化。

# GET 和 POST 的区别

## 从语义的角度来看

GET 是获取数据,POST 是提交数据

## 从缓存的角度看

GET 会让浏览器缓存,而 POST 不会

## 从发送数据的角度看

GET 是在 url 上使用?xx=xxx&xx=xxx 这样的参数形式发送请求数据

而 POST 则是在 HTTP 请求主体中发送的

## 从请求的长度看

GET 由于是在 URL 上面发送请求数据,而浏览器都加了 URL 长度限制,所以 GET 请求无法发送超过浏览器限制的长度.

POST 对长度没限制,对数据大小有限制.

## 从安全性看

GET 比 POST 更不安全，因为参数直接暴露在 URL 上，所以不能用来传递敏感信息。

## 从发送数据包来看

一般 GET 只发一个包(废话.只获取数据当然只用发一个请求头)

而 POST 请求一般发两个包,第一次发送请求头,服务器返回 100 状态码,第二次才发送数据体(视浏览器而定)

# Cookie、Session、localStorage、sessionStorage

## Cookie 是什么

Cookie 是服务器发送给浏览器的一段信息，它的大小为 4kb 左右，它里面会记录一些用户信息相关的数据。当浏览器请求服务器资源时会带上它。Cookie 可以用来确定用户身份.

Cookie 可以设置有效时间,

## Session 是什么

Session 翻译过来是会话的意思，它表示服务器跟浏览器一段时间的会话.Session 也可以用来确定用户身份

由于 HTTP 请求是无状态的,什么是无状态呢?就是请求之间互不相干,比如用户登录了一次,再打开另一个同源的页面,再发送请求时,服务器不知道用户登录了,不过有解决办法,服务器可以让用户在发送请求的请求头上写 Cookie,就可以告诉服务器用户是不是登录的.

不过 cookie 有个弱点就是它保存在浏览器上,会被人修改,不够安全.于是就产生了 session ,它主要的思想就是产生一个 sessionID 放到 cookie 上,然后给只要判断 cookie 有没有过期,如果过期了就重新生成一个 sessionID.通过 sessionID 就是可以判断登录情况.

session 主要用来解决两次 HTTP 请求的关联问题.

举个例子：

当用户请求登录的时候，如果没有问题，在服务端生成一条记录，在这个记录里可以说明登录的用户是谁，然后把这条记录的 id 发送给客户端，客户端收到以后把这个 id 存储在 cookie 里，下次该用户再次向服务端发送请求的时候，可以带上这个 cookie，这样服务端会验证一下 cookie 里的信息，看能不能在服务端这里找到对应的记录，如果可以，说明用户已经通过了身份验证，就把用户请求的数据返回给客户端。

以上所描述的过程就是利用 session，那个 id 值就是 sessionid。我们需要在服务端存储为用户生成的 session，这些 session 会存储在内存，磁盘，或者数据库。

## Cookie 和 Session 的区别

Cookie 存在于浏览器上，Session 存在于服务器上，Session 是基于 Cookie 实现的，它会将 Session ID 放到 Cookie 上。

## localStorage

用于前端储存用的，不会发送到服务器上，大小为 5M 左右

## sessionStorage

跟 localStorage 差不多,最大的区别就是时间不同,localStorage 如果不清除,会永久存在,而 sessionStorage 在会话结束时就清除数据,比如关闭了浏览器,数据就清除了.

# CSRF 攻击

## 什么是 CSRF

CSRF(Cross Site Request Forgery)即跨站请求伪造。就是利用后台有规律的接口，诱骗用户点击或者通过特殊操作发送伪造的请求。如果用户此时登录了就会被服务器认为是合法的操作。

一句话：攻击者盗用了你的身份，以你的名义发送恶意请求

## CSRF 原理

![image](https://user-images.githubusercontent.com/53749772/108193765-c4eb2800-7150-11eb-8370-28129a275bd0.png)

从上图可以看出，要完成完整的 CSRF 攻击，需要这样的条件

1.需要同时有两个页面，b 页面向 a 页面发送请求，这样浏览器会带 a 页面的 cookie

2.需要通过 cookie 骗过服务器

举个例子：

我经过测试发现有一个后台请求可以给我刷礼物，接口是这样的

```html
<img src=http://www.mybank.com/Transfer.php?toBankId=11&money=1000>
```

然后我只要让用户能够点击这张图片，那么会发送一个请求给服务器，服务器此时查看用户发送过来的请求里面有 cookie，认为是合法的操作，那么就会给我的账户打钱，这种方式就算是跨站请求伪造。

通过例子可以看出来：**CSRF 攻击是源于 WEB 的隐式身份验证机制！WEB 的身份验证机制虽然可以保证一个请求是来自于某个用户的浏览器，但却无法保证该请求是用户批准发送的！**

## 常见 CSRF 的方式

### GET 方式

```javascript
<img src=http://www.mybank.com/Transfer.php?toBankId=11&money=1000>
```

用这样的方式可以骗取浏览器的 cookie 和服务器的身份识别

### POST 方式

`img` 标签不能帮我们发 `post`请求,那么就可以使用 `from`

```javascript
<form action="http://bank.example/withdraw" method=POST>
    <input type="hidden" name="account" value="xiaoming" />
    <input type="hidden" name="amount" value="10000" />
    <input type="hidden" name="for" value="hacker" />
</form>
<script> document.forms[0].submit(); </script>
```

## CSRF 的防护策略

现在的防护一般由服务端同学完成，策略很多，这里只介绍一种。对应前端面试应该差不多了，因为这是后端的范畴。

### 同源检测

服务端设置验证 HTTP Referer 字段

通过请求头中的 Referer 来检查来源，如果来源不是本站就不做任何行动。这种方式有一定风险，因为 Referer 是浏览器自动发送的，有些老版的浏览器依然可以通过某种方法来修改 Referer，不过好在新版本的浏览器比较有保障。

如果要用这种方式实现，需要注意一点，Header 上的 Referer 属性有可能会被用户取消掉，所以写逻辑时需要增加判断有没有带 Referer，如果没有带拒绝掉就行了。

这种方式就相当于把主动权交给了浏览器，而且没有带 Referer 属性的请求会被过滤，所以这并不是最好的实现

# XSS 攻击

## 什么是 XSS 攻击

XSS 全拼为 Cross-site scripting,中文译名跨站脚本攻击,因为缩写 CSS 与层叠样式表冲突,所以改成 XSS。

XSS 攻击通常是利用网页开发造成的漏洞,通过巧妙的方法注入恶意指令代码到网页中,让用户加载并执行的网页程序。

举个例子:

比如有一个留言板,前端代码是这样写的

```javascript
  留言板
  <div></div>
  <input type="text">
  <button>提交</button>
  <script>
   var div= document.querySelector('div')
   var input= document.querySelector('input')
   var button= document.querySelector('button')
   button.onclick=()=>{
     div.innerHTML=input.value
   }
  </script>
```

当用户输入正确的内容时,留言板当然没问题

![image](https://user-images.githubusercontent.com/53749772/108172117-61a0cc00-7137-11eb-9176-41f853b714a3.png)

但是当用户是这样留言的

```javascript
<script>alert(“我要攻击你了”)</script>
```

那么页面就会变成这样

![image](https://user-images.githubusercontent.com/53749772/108172153-6d8c8e00-7137-11eb-9e5b-0501cd4fcf4c.png)

于是等下次浏览器再次解析的时候,就会打印这段话。

如果把这个代码换成其他恶意脚本程序，就是一次 XSS 攻击

归根结底 XSS 攻击就是攻击者利用某些漏洞来让用户的浏览器去执行一些原本不存在的代码。

## XSS 攻击的危害

XSS 攻击可以干的事情还有很多，这里举两个例子

1.窃取网页浏览中的 cookie 值

cookie 相当于一个令牌,有时候我们用户登录后会有这段 cookie,如果没有经过设置,那么就可以通过`document.cookie`来获取。

如果被人拿到了这段 cookie，那么账号就可能会被人登录，甚至用户都不知道自己的账号被窃取了。

好在可以设置 HttpOnly 属性，这样 cookie 就不会被`document.cookie`获取，可以缓解 XSS 攻击

2.劫持流量实现恶意跳转

如果在网页中写`<script>window.location.href="http://www.baidu.com"</script>`，那么就会自动打开百度的首页，这种就叫劫持流量。

早在 2011 年新浪就曾爆出过严重的 xss 漏洞，导致大量用户自动关注某个微博号并自动转发某条微博。

## 预防 DOM 型 XSS 攻击

XSS 攻击有很多，这里举例跟前端关系最紧密的 DOM 型攻击

DOM 型 XSS 攻击，实际上就是网站前端 JavaScript 代码本身不够严谨，把不可信的数据当作代码执行了。

在使用 .innerHTML、document.write() 时要特别小心，不要把不可信的数据作为 HTML 插到页面上，而应尽量使用 .textContent、.setAttribute() 等。

在 React 中，有个 API 叫`dangerouslySetInnerHTML`,当使用它的时候，千万要小心，需要向其传递包含 key 为 \_\_html 的对象，以此来警示你

```javascript
function createMarkup() {
  return { __html: 'First &middot; Second' };
}

function MyComponent() {
  return <div dangerouslySetInnerHTML={createMarkup()} />;
}
```

# HTTP 和 HTTPS

## HTTP 和 HTTPS 是什么

http 是超文本传输协议，是一个在计算机中专门用于两点之间传输视频、音频、图片、文字等超文本数据的通信约定和规范

https 是在 http 的安全版本。它在 TCP/IP 上的传输层又架设了一层 SSL/TL 安全协议，负责加密通信。加密的方式结合了对称加密、非对称加密、数字签名、数字证书等技术，能够在不安全的环境中为通信的双方创建一个秘密的，安全的传输通道。

## HTTP 和 HTTPS 的区别

1. 加密

   HTTP 传输的数据是未加密的，也就是明文的。

   HTTPS 传输的数据是加密的

2. 费用

   https 需要 ca 证书，这是收费的

   http 不需要，所以是免费的

3. 端口号

   默认的 http 端口号是 80，https 端口号是 443

4. 安全性

   https 是 http 的安全版本，自然安全性更高

# HTTP2.0

HTTP2.0 是 HTTP1.1 的扩展版本，它主要补充了 1.1 的一些问题：

- 二进制协议，不再是纯文本。HTTP2.0 会将所有的传输信息分割为更小的信息或者帧，并对他们进行二进制编码
- 可以发起多个请求，多路复用。做到同一个连接并发处理多个请求。改善了：HTTP1.1 也可以多建立几个 TCP 连接，来支持处理更多并发的请求，但是创建 TCP 连接本身也是有开销的。
- 压缩请求头，减少数据传输量。
- 允许服务器向客户端推送数据，服务器可以对客户端的一个请求发送多个响应，这样做最大的好处就是可以触发浏览器缓存。
- 增加安全性，要求加密通信。

# TCP 和 UDP 的区别

TCP 和 UDP 都属于传输层。

TCP 是完整的、可靠的、有序的、有状态的协议，必须在双方建立连接才可以发送数据，而且需要保证数据不丢失和重复。

UDP 是不可靠的，它没有状态也不需要事先建立连接就可以随意发送数据，但是并不保证数据一定能发送给对方。

TCP 的数据是有先后顺序的，UDP 是顺序发，乱序收。

TCP 适合大数据量传输，比如收发邮件、传输文件等，UDP 适合视频、音频等多媒体通信。
