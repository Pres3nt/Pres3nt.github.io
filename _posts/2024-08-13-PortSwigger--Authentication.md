---

title: PortSwigger--Authentication
categories: [渗透靶场, PortSwigger]

---

## 1.**Username enumeration via different responses**

（1）固定密码进行用户名爆破，通过回显长度不同，发现用户名

![image-20240829001956615](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408290019733.png)

![image-20240829002003470](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408290020544.png)

（2）利用爆破出来的用户名test进行密码的爆破，成功爆破出账号密码

![image-20240829002011906](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408290020997.png)

![image-20240829002020172](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408290020259.png)

## 2.2FA **simple bypass**

（1）通过wiener用户获取成功登录后的接口

```py
#通过账号密码邮箱验证码
#成功获取到wiener用户登录成功后所在路径为：/my-account?id=wiener
https://0a07000204bf1303807f6c49009f00e9.web-security-academy.net/my-account?id=wiener
```

![image-20240829002047170](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408290020249.png)

（2）抓包修改接口，carlos用户绕过第二层邮箱验证

```py
#carlos用户只知道账号密码，不知道邮箱验证码
```

![image-20240829002057469](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408290020552.png)

![image-20240829002106332](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408290021425.png)

![image-20240829002114434](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408290021525.png)

（3）carlos用户成功绕过第二重邮箱验证码登录

![image-20240829002122370](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408290021452.png)

## 3.**Password reset broken logic**

（1）wiener用户进行密码重置

![image-20240829002131833](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408290021976.png)

（2）数据包发送至Repeater，放包确认正确修改密码的响应状态码为302

![image-20240829005022074](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408290050217.png)

（3）删除token，再次放包发现响应状态码还是302，说明后端不对token进行校验

![image-20240829002149756](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408290021839.png)

（4）修改username参数为carlos，成功重置carlos用户的密码

![image-20240829002201290](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408290022392.png)

![image-20240829002207883](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408290022960.png)

## 4.**Username enumeration via subtly different responses**

（1）固定密码爆破用户名，发现没有异常明显的回显包，无法进行正确判断

![image-20240829002215275](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408290022364.png)

（2）Intruder模块的setting中找到Grep-Extract功能（增加回显信息），选中报错内容进行条件添加

![image-20240829002225184](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408290022278.png)

![image-20240829002233052](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408290022151.png)

（3）再次进行爆破，发现当用户名为root时，报错内容少了.号，证明root有可能是用户名

![image-20240829002244591](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408290022696.png)

（4）以用户名为root进行密码爆破，成功爆破密码121212

![image-20240829002255203](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408290022312.png)

![image-20240829002304201](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408290023299.png)

## 5.**Username enumeration via response timing**

（1）爆破结果界面选择显示响应时间

![image-20240829002315837](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408290023937.png)

![image-20240829002321651](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408290023736.png)

（2）开启回显内容显示之后发现该站点存在IP爆破限制

![image-20240829002332633](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408290023704.png)

（3）利用X-Forwarded-For赋值随机值来绕过IP限制

![image-20240829002347157](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408290023261.png)

（4）在利用响应时间进行结果判断的时候需要将爆破密码设置的长一些（时间更加明显）

![image-20240829002402237](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408290024332.png)

（5）利用爆破出的用户名，成功爆破出密码

![image-20240829002415483](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408290024568.png)

![image-20240829002424904](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408290024981.png)

## 6.**Broken brute-force protection,** **IP** **block**

（1）登录框抓包爆破carlos密码，发现登录失败3次就无法继续爆破

![image-20240829002706259](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408290027363.png)

![image-20240829002714113](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408290027223.png)

（2）利用正确账号登录和目标账号交叉方式爆破方式绕过，让chatgpt处理交叉账号密码数据

![image-20240829002721527](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408290027616.png)

![image-20240829002731137](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408290027230.png)

（3）选择鱼叉爆破

![image-20240829002753826](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408290027923.png)

（4）302是登录成功的跳转，找到carlos成功跳转的

![image-20240829002805388](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408290028502.png)

![image-20240829002813382](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408290028473.png)

## 7.**Username enumeration via account lock**

（1）爆破模式集束炸弹，每个账号爆破五次

![image-20240829002831142](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408290028245.png)

![image-20240829002836986](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408290028085.png)

（2）密码设置为数字随机值，每个密码爆破5次

![image-20240829005105992](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408290051122.png)

（3）根据账号锁定的回显内容确认正确的用户名

![image-20240829002855004](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408290028110.png)

（4）爆破密码，成功登录

![image-20240829002901478](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408290029581.png)

![image-20240829002910525](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408290029615.png)

## 8.2FA **broken logic**

（1）wiener用户抓包登录，删除token依旧可以进行登录，后端不对token进行验证，存在越权

![image-20240829002918410](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408290029509.png)

![image-20240829002928487](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408290029592.png)

（2）在wiener用户页面刷新抓包，修改用户声明参数越权至carlos用户页面

![image-20240829002939476](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408290029580.png)

![image-20240829002947429](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408290029552.png)

（3）验证码抓包，进行验证码爆破

![image-20240829002957680](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408290029804.png)

![image-20240829003006148](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408290030265.png)

（4）成功爆破验证码，登录carlos用户

![image-20240829003018210](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408290030308.png)

![image-20240829003025976](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408290030094.png)

## 9.**Brute-forcing a stay-logged-in cookie**

（1）登录winner页面后刷新抓包，发现stay-logged-in参数是加密过后的账号密码

![image-20240829003039516](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408290030610.png)

![image-20240829003048048](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408290030151.png)

![image-20240829003058566](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408290030693.png)

（2）退出wiener账号，刷新页面抓包

![image-20240829003108692](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408290031793.png)

![image-20240829003119224](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408290031324.png)

（3）修改GET路径为/my-account，按照加密方式爆破stay-logged-in的值

![image-20240829003135858](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408290031974.png)

![image-20240829003144969](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408290031078.png)

（4）成功登录carlos用户

![image-20240829003151897](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408290031004.png)

![image-20240829003205665](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408290032768.png)

![image-20240829003215836](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408290032938.png)

![image-20240829003225639](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408290032793.png)

## 10.**Offline password cracking**

（1）登录wiener用户，并且开启服务器

![image-20240829005153555](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408290051674.png)

![image-20240829003243509](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408290032629.png)

（2）主页找到一篇文章，评论插入XSS代码

![image-20240829003250120](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408290032222.png)

![image-20240829003304688](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408290033783.png)

![image-20240829003316213](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408290033318.png)

（3）回到服务器查看监听日志，成功获取用户cookie

![image-20240829003327235](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408290033335.png)

![image-20240829003337424](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408290033539.png)

（4）解密cookie

![image-20240829003343163](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408290033254.png)

![image-20240829003352064](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408290033164.png)

（5）成功登录用户

![image-20240829003400240](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408290034322.png)

## 11.**Password reset poisoning via middleware**

（1）开启服务器，进入重置密码，重置carlos用户，抓包

![image-20240829003410456](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408290034572.png)

![image-20240829003422889](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408290034995.png)

![image-20240829003433836](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408290034949.png)

（2）添加X-Forwarded-Host标头，地址为服务器地址（去掉http://）

![image-20240829003445853](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408290034970.png)

![image-20240829003456202](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408290034328.png)

（3）放包，在服务器日志中获取carlos用户token值

![image-20240829003509201](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408290035341.png)

（4）重置wiener用户获取到重置密码的连接

![image-20240829003519459](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408290035605.png)

（5）将wiener用户token替换成为carlos的token，成功越权修改carlos用户的密码

![image-20240829003530268](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408290035389.png)

![image-20240829003540731](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408290035885.png)

## 12.**Password brute-force via password change**

（1）登录wiener用户，发现存在更改密码功能

![image-20240829003551522](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408290035636.png)

（2）修改密码，抓包

```py
#原密码正确，新密码不一致-->New passwords do not match
#原密码不正确，新密码一致-->302跳转
#原密码错误，新密码不一致-->Current password is incorrect

#爆破思路：设置新密码不一致，爆破原密码，筛选New passwords do not match的包
```

![image-20240829003607831](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408290036944.png)

![image-20240829003617860](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408290036992.png)

![image-20240829003625184](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408290036300.png)

![image-20240829003633461](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408290036590.png)

（3）修改用户声明参数为carlos，爆破原密码，Grep -Match功能匹配字符串

![image-20240829003642922](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408290036018.png)

![image-20240829003857339](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408290038443.png)

![image-20240829003702573](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408290037717.png)

（4）成功登录carlos用户

![image-20240829003711626](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408290037730.png)