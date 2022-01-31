# HTTP 状态码

## HTTP状态码分类

| 分类 | 分类描述                                       |
| ---- | ---------------------------------------------- |
| 1**  | 信息，服务器收到请求，需要请求者继续执行操作   |
| 2**  | 成功，操作被成功接收并处理                     |
| 3**  | 重定向，需要进一步的操作以完成请求             |
| 4**  | 客户端错误，请求包含语法错误或无法完成请求     |
| 5**  | 服务器错误，服务器在处理请求的过程中发生了错误 |

## 常见的状态码

* 100  Continue  客户端应继续其请求
* 200  OK  请求成功。一般用于GET与POST请求
* 201  Created  已创建。成功请求并创建了新的资源
* 202  Accepted  已接受。已经接受请求，但未处理完成
* 204  No Content  无内容。服务器成功处理，但未返回内容。
* 301  Moved Permanently 永久重定向
* 302 Found 临时重定向
* 304 Not Modified 无修改。当命中协商缓存的时候会发送这个码
* 400  Bad Request  客户端请求的语法错误，服务器无法理解
* 401  Unauthorized  请求要求用户的身份认证
* 403  Forbidden  服务器理解请求客户端的请求，但是拒绝执行此请求
* 404  Not Found  服务器无法根据客户端的请求找到资源（网页）
* 414  Request-URI Too Large  请求的URI过长
* 500  Internal Server Erro  服务器内部错误，无法完成请求
* 501  Not Implemented  服务器不支持请求的功能
* 502 Bad Gateway  作为网关或者代理工作的服务器尝试执行请求时，从远程服务器接收到了一个无效的响应

临时重定向和永久重定向的区别：

比如你的网站从 HTTP 升级到了 HTTPS 了，以前的站点再也不用了，应当返回301，这个时候浏览器默认会做缓存优化，在第二次访问的时候自动访问重定向的那个地址。
而如果只是暂时不可用，那么直接返回302即可，和301不同的是，浏览器并不会做缓存优化。

# HTTP 缓存

## 什么是 HTTP 缓存

http缓存指的是: 当客户端向服务器请求资源时，会先抵达浏览器缓存，如果浏览器有“要请求资源”的副本，就可以直接从浏览器缓存中提取而不是从原始服务器中提取这个资源。

常见的http缓存只能缓存get请求响应的资源,http缓存都是从第二次请求开始的。第一次请求资源时，服务器返回资源，并在respone header头中回传资源的缓存参数；第二次请求时，浏览器判断这些请求参数，命中强缓存就直接200，否则就把请求参数加到request header头中传给服务器，看是否命中对比缓存，命中则返回304，否则服务器会返回新的资源。

## HTTP缓存分类

根据是否需要重新向服务器发起请求来分类，可分为(强制缓存，对比缓存).

强缓存如果生效,那么不再和服务器发生交互.

对比缓存不管生不生效,都需要跟服务器发生交互.

也就是说强缓存可以不发请求,而对比缓存都得发请求.

跟强缓存相关的 API 有 Expires 和 Cache-Control等,跟对比缓存相关的有 Etag等,两种缓存可以同时存在,但是优先级是强缓存更高.

## 强缓存

强缓存跟缓存数据的时间有效性有关,当浏览器没有缓存数据的时候,会发送第一次请求给服务器,服务器会在响应头中附带相关的缓存规则,响应头中有关的字段就是Expires/Cache-Control.

### Expires

Expires中会表明具体的到期时间,在时间内都可以进行缓存.但是这里会有一个 BUG,因为本地时间跟服务器时间不一定相同,而且本地时间可以修改,比如说我的本地时间往前调整了两年,那么浏览器的强缓存就会一直延续两年,所以这种方式已经过时不用了

### Cache-Control

Cache-Control可以设置缓存的时间,比如它有个属性为 max-age,可以设置缓存在多少秒之后失效

![image](https://user-images.githubusercontent.com/53749772/107609964-58b58380-6c7b-11eb-80f9-4ea1e66c3941.png)

上面的例子就是设置了多少秒的缓存时间,这种方式比Expires靠谱得多.

## 对比缓存

对比缓存的意思就是发给服务器做一下资源对比,看看是不是需要缓存数据.

这里的资源对比需要一个标识,这个标识也是第一次请求时服务器返回给浏览器保存在浏览器缓存库中的,那么对比缓存就会发送带这个标识的请求给服务器看看是不是一样的,如果一样的话那就用缓存数据好了.判断成功后会返回304状态码.

对比成功时,由于服务器只返回响应头,不需要返回响应主体,所以数据量大大降低.

这里就需要提到缓存标识,缓存标识一共分两种:修改时间和资源唯一标识Etag
### 修改时间
修改时间需要用到两个字段Last-Modified  /  If-Modified-Since

Last-Modified是第一次请求时服务器发送给浏览器的响应头字段,上面记录服务器资源的最后修改时间.

If-Modified-Since是浏览器第二次请求时发送的响应头字段,服务器通过这个响应头字段来对比一下自己的最后修改时间.

如果时间一样,说明资源没有修改过,则响应304状态码,告诉浏览器用缓存数据.

如果时间不一样,说明资源有动过,那么就响应200状态码,并把报文主体也发过去.

### 唯一标识Etag
这个是由服务器决定规则的标识,当第一次访问时,服务器就会在响应头中带着这个标识,这个标识长这样
![image](https://user-images.githubusercontent.com/53749772/107611124-3cffac80-6c7e-11eb-8c6b-db4aaaef70a8.png)

当再次访问服务器时,浏览器会在请求头中发送If-None-Match这个字段并附上之前的 Etag 标识,如果服务器发现有这个标识就会跟自己的唯一标识做对比.

如果一样,说明资源没改过,响应304状态码,告诉浏览器用缓存数据

如果不一样,说明资源改动过,响应200并且发最新的资源主体过去.

## 总结

HTTP 缓存就类似于浏览器跟服务器中间的一个缓存数据库,至于用不用这个缓存库就需要服务器来指定.

服务器有两种方式:强制缓存和对比缓存.强制缓存比对比缓存优先级高

强制缓存不会发请求给服务器,它就是设定一个时间,这个时间可以分为 Expires 和 Cache-Control,Expires就是定一个具体的时间节点,但是有 bug,会因为服务器时间跟本地时间不同而有误差.而Cache-Control就是指定多少秒内用强制缓存.当强制缓存成功后返回200.

对比缓存就是发送一段请求给服务器,请求信息可以有两种:修改时间和唯一标识.

两种都是需要跟服务器的信息进行对比,对比成功就返回304,告诉浏览器用缓存数据,对比失败就返回200,然后把用户需要请求的数据作为资源主体发回去.



# GET 和 POST 的区别

## 从语义的角度来看
GET 是获取数据,POST 是提交数据

## 从缓存的角度看
GET 会让浏览器缓存,而 POST 不会

## 从发送数据的角度看
GET 是在 url 上使用?xx=xxx&xx=xxx这样的参数形式发送请求数据

而 POST 则是在 HTTP 请求主体中发送的

## 从请求的长度看
GET 由于是在 URL 上面发送请求数据,而浏览器都加了URL 长度限制,所以GET 请求无法发送超过浏览器限制的长度.

POST 对长度没限制,对数据大小有限制.

## 从安全性看
GET比POST更不安全，因为参数直接暴露在URL上，所以不能用来传递敏感信息。

## 从发送数据包来看
一般 GET 只发一个包(废话.只获取数据当然只用发一个请求头)

而 POST 请求一般发两个包,第一次发送请求头,服务器返回100状态码,第二次才发送数据体(视浏览器而定)



# Cookie、Session、localStorage、sessionStorage

## Cookie是什么

Cookie 是服务器发送给浏览器的一段信息，它的大小为4kb 左右，它里面会记录一些用户信息相关的数据。当浏览器请求服务器资源时会带上它。Cookie 可以用来确定用户身份.

Cookie 可以设置有效时间,

## Session是什么

Session翻译过来是会话的意思，它表示服务器跟浏览器一段时间的会话.Session也可以用来确定用户身份

由于 HTTP 请求是无状态的,什么是无状态呢?就是请求之间互不相干,比如用户登录了一次,再打开另一个同源的页面,再发送请求时,服务器不知道用户登录了,不过有解决办法,服务器可以让用户在发送请求的请求头上写 Cookie,就可以告诉服务器用户是不是登录的.

不过 cookie 有个弱点就是它保存在浏览器上,会被人修改,不够安全.于是就产生了 session ,它主要的思想就是产生一个 sessionID 放到 cookie 上,然后给只要判断 cookie 有没有过期,如果过期了就重新生成一个sessionID.通过sessionID就是可以判断登录情况.

session 主要用来解决两次 HTTP 请求的关联问题.

举个例子：

当用户请求登录的时候，如果没有问题，在服务端生成一条记录，在这个记录里可以说明登录的用户是谁，然后把这条记录的id发送给客户端，客户端收到以后把这个id存储在cookie里，下次该用户再次向服务端发送请求的时候，可以带上这个cookie，这样服务端会验证一下cookie里的信息，看能不能在服务端这里找到对应的记录，如果可以，说明用户已经通过了身份验证，就把用户请求的数据返回给客户端。

以上所描述的过程就是利用session，那个id值就是sessionid。我们需要在服务端存储为用户生成的session，这些session会存储在内存，磁盘，或者数据库。

## Cookie和Session的区别

Cookie 存在于浏览器上，Session存在于服务器上，Session是基于Cookie实现的，它会将Session ID 放到Cookie上。

## localStorage

用于前端储存用的，不会发送到服务器上，大小为5M 左右

## sessionStorage

跟 localStorage差不多,最大的区别就是时间不同,localStorage如果不清除,会永久存在,而sessionStorage在会话结束时就清除数据,比如关闭了浏览器,数据就清除了.

# CSRF攻击 

## 什么是CSRF

CSRF(Cross Site Request Forgery)即跨站请求伪造。就是利用后台有规律的接口，诱骗用户点击或者通过特殊操作发送伪造的请求。如果用户此时登录了就会被服务器认为是合法的操作。

一句话：攻击者盗用了你的身份，以你的名义发送恶意请求

## CSRF原理

![image](https://user-images.githubusercontent.com/53749772/108193765-c4eb2800-7150-11eb-8370-28129a275bd0.png)

从上图可以看出，要完成完整的 CSRF 攻击，需要这样的条件

1.需要同时有两个页面，b 页面向 a 页面发送请求，这样浏览器会带 a 页面的 cookie

2.需要通过cookie 骗过服务器

举个例子：

我经过测试发现有一个后台请求可以给我刷礼物，接口是这样的

```html
<img src=http://www.mybank.com/Transfer.php?toBankId=11&money=1000>
```

然后我只要让用户能够点击这张图片，那么会发送一个请求给服务器，服务器此时查看用户发送过来的请求里面有 cookie，认为是合法的操作，那么就会给我的账户打钱，这种方式就算是跨站请求伪造。

通过例子可以看出来：**CSRF攻击是源于WEB的隐式身份验证机制！WEB的身份验证机制虽然可以保证一个请求是来自于某个用户的浏览器，但却无法保证该请求是用户批准发送的！**

## 常见CSRF的方式

### GET方式 

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

通过请求头中的Referer来检查来源，如果来源不是本站就不做任何行动。这种方式有一定风险，因为Referer是浏览器自动发送的，有些老版的浏览器依然可以通过某种方法来修改Referer，不过好在新版本的浏览器比较有保障。

如果要用这种方式实现，需要注意一点，Header 上的Referer属性有可能会被用户取消掉，所以写逻辑时需要增加判断有没有带Referer，如果没有带拒绝掉就行了。

这种方式就相当于把主动权交给了浏览器，而且没有带Referer属性的请求会被过滤，所以这并不是最好的实现

# XSS攻击 

## 什么是 XSS 攻击

XSS全拼为Cross-site scripting,中文译名跨站脚本攻击,因为缩写 CSS 与层叠样式表冲突,所以改成 XSS。

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

归根结底XSS 攻击就是攻击者利用某些漏洞来让用户的浏览器去执行一些原本不存在的代码。

## XSS 攻击的危害

XSS攻击可以干的事情还有很多，这里举两个例子

1.窃取网页浏览中的cookie值

cookie相当于一个令牌,有时候我们用户登录后会有这段 cookie,如果没有经过设置,那么就可以通过`document.cookie`来获取。

如果被人拿到了这段 cookie，那么账号就可能会被人登录，甚至用户都不知道自己的账号被窃取了。

好在可以设置HttpOnly属性，这样 cookie 就不会被`document.cookie`获取，可以缓解 XSS 攻击

2.劫持流量实现恶意跳转

如果在网页中写`<script>window.location.href="http://www.baidu.com"</script>`，那么就会自动打开百度的首页，这种就叫劫持流量。

早在2011年新浪就曾爆出过严重的xss漏洞，导致大量用户自动关注某个微博号并自动转发某条微博。

## 预防 DOM 型 XSS 攻击

XSS 攻击有很多，这里举例跟前端关系最紧密的DOM 型攻击

DOM 型 XSS 攻击，实际上就是网站前端 JavaScript 代码本身不够严谨，把不可信的数据当作代码执行了。

在使用 .innerHTML、document.write() 时要特别小心，不要把不可信的数据作为 HTML 插到页面上，而应尽量使用 .textContent、.setAttribute() 等。

在 React 中，有个 API 叫`dangerouslySetInnerHTML`,当使用它的时候，千万要小心，需要向其传递包含 key 为 __html 的对象，以此来警示你

```javascript
function createMarkup() {
  return {__html: 'First &middot; Second'};
}

function MyComponent() {
  return <div dangerouslySetInnerHTML={createMarkup()} />;
}
```