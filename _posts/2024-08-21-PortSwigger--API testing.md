---

title: PortSwigger--API testing
categories: [渗透靶场, PortSwigger]

---

## 1.**Exploiting an** **API** **endpoint using documentation**

（1）端口登录，更新邮箱功能点抓包

![image-20240828181543682](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281815744.png)

![image-20240828181552349](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281815396.png)

（2）修改请求方式为GET/POST，尝试接口未授权

![image-20240828181603056](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281816098.png)

![image-20240828181614214](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281816257.png)

![image-20240828181622496](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281816542.png)

（3）未授权访问/api/接口

![image-20240828181630103](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281816140.png)

（4）修改请求方法为DELETE，删除用户carlos

![image-20240828181637638](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281816682.png)

![image-20240828181700941](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281817976.png)

## 2.**Exploiting server-side parameter pollution in a** **query** **string**

（1）开启Burpsuite代理，忘记密码功能点访问，查看HTTP流量

![image-20240828181706544](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281817580.png)

![image-20240828181714222](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281817259.png)

（2）将JS文件发送至Repeate进行接口关键词检索，发现重置密码接口参数reset_token，如果能够获取reset_token的值，就能实现用户密码重置

![image-20240828181721800](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281817845.png)

（3）提交administrator重置密码的请求，POST包中的参数为username=administrator，而服务端返回administrator用户的email-->说明存在隐藏参数并且该参数的默认值为email

![image-20240828181733559](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281817596.png)

![image-20240828181741012](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281817049.png)

![image-20240828181747563](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281817607.png)

（4）"#"截断后续参数请求，观察响应-->报错"Field not specified "-->说明隐藏参数是：field

![image-20240828181801018](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281818061.png)

（5）拼接field参数设置其值为email，服务器正常回显administrator用户email-->说明参数格式正确

![image-20240828181810838](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281818881.png)

（6）修改field的值，尝试获取其他内容的回显，服务端依旧回显email-->服务端可能存在过滤

![image-20240828181852382](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281818428.png)

（7）将"&"进行URL编码再尝试，成功回显field参数指定内容-->说明服务端对"&"进行了检测

![image-20240828181900896](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281819941.png)

（8）设置field值为reset_token，成功获取administrator用户重置密码的token

![image-20240828181908057](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281819099.png)

（9）构造GET请求，进入administrator用户重置密码界面

![image-20240828181918355](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281819400.png)

![image-20240828181926352](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281819397.png)

![image-20240828181934487](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281819518.png)

（10）登录administrator用户，进入Admin panel界面删除carlos用户

![image-20240828181939790](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281819829.png)

![image-20240828181947555](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281819588.png)

![image-20240828181953504](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281819544.png)

## 3.**Finding and exploiting an unused** **API** **endpoint**

（1）wiener用户登录，开启Burpsuite代理，查看Product1的HTTP流量

![image-20240828182025609](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281820648.png)

![image-20240828182031555](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281820596.png)

（2）将JSON文件发送至Repeat观察服务端响应内容-->服务端回显商品价格和相关信息![image-20240828182038436](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281820481.png)

（3）修改HTTP方法为OPTIONS，请求服务端接受的HTTP方法-->PATCH可用

![image-20240828182048997](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281820041.png)

（4）修改HTTP方法为PATCH，观察服务端响应内容-->缺少content-type

![image-20240828182056202](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281820246.png)

（5）添加Content-type，JSON空数据-->缺少price参数

![image-20240828182102461](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281821505.png)

![image-20240828182110659](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281821703.png)

（6）添加price参数，服务端响应price参数对应的值-->price可被修改

![image-20240828182127864](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281821904.png)

![image-20240828182134018](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281821067.png)

（7）刷新页面，发现product1的价格已经被修改成0元，添加购物车，实现0元购

![image-20240828182141152](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281821204.png)

![image-20240828182150212](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281821248.png)

![image-20240828182156873](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281821908.png)

## 4.**Exploiting a mass assignment** **vulnerability**

（1）开启Burpsuite代理，查看商品详情，将product1加入购物车中,查看购物车详情，观察HTTP流量

![image-20240828182248882](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281822923.png)

![image-20240828182254848](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281822891.png)

![image-20240828182303861](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281823905.png)

![image-20240828182310972](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281823022.png)

（2）JSON包发送至repeate进行请求，观察服务端响应内容-->服务端返回商品折扣，商品相关信息

![image-20240828182416676](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281824724.png)

（3）修改HTTP方法为OPTIONS，查询服务器接受的HTTP方法

![image-20240828182422347](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281824394.png)

（4）修改HTTP方法为POST，添加JSON空数据-->缺少product1相关信息数组

![image-20240828182428580](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281824628.png)

（5）复制GET请求的响应内容，将price修改为0，以POST方式提交-->服务端回显资金不足-->服务端验证前端提交的商品价格

![image-20240828182438053](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281824101.png)

![image-20240828182445577](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281824613.png)

（6）修改商品折扣力度为100，成功0元购-->服务端对前端提交的数据只对商品价格进行验证并没有对商品折扣进行验证

![image-20240828182451890](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281824936.png)

![image-20240828182459821](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281824857.png)

## 5.**Exploiting server-side parameter pollution in a** **REST** **URL**

（1）开启Burpsuite代理，访问密码找回界面，观察HTTP流量

![image-20240828182506293](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281825333.png)

![image-20240828182512068](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281825111.png)

（2）JS文件进行接口关键词审计-->重置密码接口参数passwordResetToken，如果能获取到该token，就能构造GET请求进行用户密码重置

![image-20240828182521068](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281825120.png)

（3）提交administrator用户的重置密码请求，JSON文件发送至Repeate进行请求-->回显administrator用户email-->说明存在隐藏参数并且该参数的默认值为email

![image-20240828182530433](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281825476.png)

![image-20240828182536171](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281825221.png)

（4）"#"截断后续参数请求，观察响应内容-->报错"Invalid route."-->说明#起到了截断作用，但是服务端并没有在报错中回显相关的错误数据

![image-20240828182545732](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281825782.png)

![image-20240828182551640](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281825676.png)

（5）既然无法通过参数截断报错来获取相关参数信息，那就换种思路：尝试目录穿越读取API配置文件

```py
JSON数据美化一下
https://www.itinfotools.com/json
```

![image-20240828182559268](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281825320.png)

![image-20240828182612066](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281826119.png)

![image-20240828182629487](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281826536.png)

（6）通过目录穿越和参数截断成功读取配置文件信息

获取到API Version1的绝对路径格式为：/api/internal/v1/users/{username}/field/{field}

尝试读取username：../../../../api/internal/v1/users/administrator/field/username#

![image-20240828182649437](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281826514.png)

（7）修改field参数为passwordResetToken，获取passwordResetToken

构造GET请求重置administrator用户密码

![image-20240828182659386](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281826435.png)

![image-20240828182711709](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281827767.png)

![image-20240828182721135](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281827180.png)

（8）登录administrator，删除用户carlos

![image-20240828182729764](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281827812.png)

![image-20240828182736440](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281827486.png)