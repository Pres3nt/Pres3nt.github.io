---

title: PortSwigger--Access control vulnerabilities
categories: [渗透靶场, PortSwigger]

---

## 1.**Access control vulnerabilities**

（1）登录访问/robots.txt文件，泄露/administrator-panel接口

![image-20240828183243615](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281832658.png)

（2）/administrator-panel接口不做访问限制，任意用户可访问

![image-20240828183249020](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281832055.png)

![image-20240828183256888](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281832930.png)

## 2.**Unprotected admin functionality with unpredictable** **URL**

（1）开启Burpsuite代理，访问主页观察HTTP流量

![image-20240828183303672](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281833710.png)

（2）将HTML请求发送至Repeate，进行重放，进行源码关键词检索，HTML源码中泄露控制台接口，并且该接口没有做访问限制，任意用户可访问

![image-20240828183313647](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281833692.png)

![image-20240828183322417](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281833975.png)

![image-20240828183336671](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281833699.png)

## 3.**User role controlled by request parameter**

（1）用户wiener登录，访问/admin页面

![image-20240828183344034](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281833072.png)

![image-20240828183352840](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281833881.png)

（2）修改Admin参数为true，成功进入/admin界面，Admin参数控制用户是否为管理员用户

![image-20240828183402843](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281834887.png)

![image-20240828183411155](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281834200.png)

![image-20240828183418558](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281834590.png)

## 4.**User role can be modified in user profile**

（1）用户wiener登录，进行邮箱更改

![image-20240828183425098](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281834135.png)

![image-20240828183431148](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281834185.png)

![image-20240828183439347](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281834386.png)

（2）JSON请求发送至Repeat，进行重放，观察服务端的回显

![image-20240828183446098](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281834140.png)

（3）猜测参数roleid控制用户权限，修改参数roleid为不同的值观察服务端回显

![image-20240828183456226](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281834270.png)

![image-20240828183502993](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281835036.png)

![image-20240828183510102](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281835144.png)

（4）当提交roleid参数为2时，用户wiener越权成为administrator用户

![image-20240828193811606](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281938662.png)

![image-20240828183536299](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281835334.png)

## 5.**User ID controlled by request parameter**

（1）用户wiener登录，HTML请求发送至Repeate模块进行重放，观察服务端回显

![image-20240828183543172](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281835210.png)

![image-20240828183552551](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281835593.png)

（2）猜测ID参数控制用户，修改参数ID为carlos，成功越权至carlos用户

![image-20240828183605406](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281836449.png)

（3）复制carlos用户API进行提交，通关靶场

![image-20240828183615487](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281836526.png)

![image-20240828183624211](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281836249.png)

## 6.**User ID controlled by request parameter, with unpredictable user** **IDs**

（1）用户wiener登录，HTML请求发送至Repeate模块进行重放，观察服务端回显

![image-20240828192243445](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281922489.png)

![image-20240828192252753](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281922805.png)

（2）猜测ID参数控制用户，在网站中寻找其他用户ID

![image-20240828192305086](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281923161.png)

![image-20240828192315392](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281923446.png)

![image-20240828192324159](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281923209.png)

![image-20240828192330729](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281923778.png)

（3）将wiener用户ID进行替换，成功越权至carlos用户

![image-20240828192341748](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281923798.png)

![image-20240828192348131](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281923175.png)

（4）提交carlos用户的APi，通关靶场

![image-20240828192358767](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281923811.png)

![image-20240828192405721](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281924759.png)

## 7.**User ID controlled by request parameter with data leakage in redirect**

（1）wiener用户登录，HTML请求发送至Repeate进行重放，观察服务端回显

![image-20240828192413451](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281924490.png)

![image-20240828192423308](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281924351.png)

（2）猜测参数ID控制用户，修改参数ID，页面出现302跳转-->302跳转页面回显内容，泄露用户信息

![image-20240828192448735](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281924787.png)

![image-20240828192459023](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281924074.png)

![image-20240828192512309](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281925360.png)

（3）提交carlos用户API，通关靶场

![image-20240828192523005](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281925049.png)

![image-20240828192533181](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281925230.png)

## 8.**User ID controlled by request parameter with password disclosure**

（1）用户wiener登录，发现用户密码没有隐藏

![image-20240828192539734](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281925778.png)

![image-20240828192549621](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281925665.png)

（2）F12打开开发者工具，选中密码框可以直接看到用户密码

![image-20240828192601156](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281926204.png)

（3）观察HTTP流量，HTML请求中存在参数ID，修改参数ID越权至administrator用户页面

![image-20240828192611232](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281926288.png)

![image-20240828192619439](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281926489.png)

（4）打开开发者工具选中密码框，成功获取administrator用户密码

![image-20240828192626090](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281926143.png)

![image-20240828192633539](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281926592.png)

![image-20240828192643195](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281926245.png)

![image-20240828192652189](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281926236.png)

## 9.**Insecure direct object references**

（1）进入主页，观察Live chat客服

![image-20240828192659525](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281926581.png)

![image-20240828192704767](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281927815.png)

（2）点击View transcript会下载一个txt文档，观察HTTP流量

![image-20240828192713398](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281927446.png)

（3）修改GET路径，下载其他文件，成功读取到其他用户的历史聊天记录

![image-20240828192722567](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281927616.png)

![image-20240828192728430](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281927480.png)

（4）登录用户carlos

![image-20240828192738113](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281927161.png)

![image-20240828192745603](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281927647.png)

## 10.**URL-based access control can be circumvented**

服务端基于URL对用户进行访问控制

（1）访问admin界面，服务端对用户身份进行校验

![image-20240828192755485](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281927548.png)

![image-20240828192802748](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281928800.png)

（2）通过HTTP请求头X-Original-URL访问/admin，绕过URL对/admin页面的访问控制

![image-20240828192813592](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281928647.png)

![image-20240828192822340](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281928390.png)

![image-20240828192829866](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281928918.png)

![image-20240828192835629](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281928688.png)

![image-20240828192845066](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281928110.png)

## 11.**Method-based access control can be circumvented**

服务端基于HTTP请求方法的不同对用户进行访问控制

（1）登录用户administrator进行carlos用户的权限提升，观察其数据包结构

（Post方式提交username和action两个参数）

![image-20240828192924307](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281929356.png)

![image-20240828192932312](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281929371.png)

（2）登录用户wiener，将HTML请求发送至Repeate进行重放

![image-20240828193006358](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281930409.png)

![image-20240828193024154](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281930208.png)

（3）Post方式向/admin-roles提交表单，服务端用户校验未通过

![image-20240828193033197](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281930250.png)

（4）修改HTTP方法为GET，用户wiener成功提权

![image-20240828193040240](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281930292.png)

![image-20240828193049526](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281930573.png)

## 12.**Multi-step process with no access control on one step**

服务端基于参数确认对用户进行访问控制

（1）登录用户administrator进行carlos用户的权限提升，观察其数据包结构

（Post方式提交username，action，confirmed三个参数）

![image-20240828193100806](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281931858.png)

（2）登录用户wiener，将HTML请求发送至Repeate进行重放

![image-20240828193108412](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281931461.png)

![image-20240828193115789](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281931843.png)

（3）Post方式向/admin-roles提交表单，wiener用户成功实现越权

![image-20240828193123076](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281931131.png)

![image-20240828193131192](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281931243.png)

## 13.**Referer-based access control**

服务端基于HTTP头中的Referer字段对用户进行访问控制

（1）登录用户administrator进行carlos用户的权限提升，观察其数据包结构

（Get方式提交username，action两个参数，并且请求中多出了Referer字段）

![image-20240828193302803](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281933859.png)

![image-20240828193313274](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281933331.png)

（2）登录用户wiener，将HTML请求发送至Repeate进行重放

![image-20240828193322859](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281933908.png)

![image-20240828193330606](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281933659.png)

（3）Get/Post方式请求/admin-roles，更改请求方法无法绕过服务端对用户的校验

![image-20240828193339981](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281933037.png)

![image-20240828193348299](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281933353.png)

（4）修改referer字段中的/login为/admin，wiener用户成功提权

![image-20240828193357923](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281933982.png)

![image-20240828193857772](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281938821.png)