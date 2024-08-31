---


title: PortSwigger--JWT
categories: [渗透靶场, PortSwigger]

---

## 1.**JWT** **authentication bypass via unverified signature**

（1）普通用户wiener登录，访问/admin页面，回显权限不够

![image-20240828200450536](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408282004576.png)

（2）抓包发现验证方式为JWT验证

![image-20240828200459528](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408282004580.png)

（3）修改JWTpayload部分sub参数为administrator

![image-20240828200509867](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408282005918.png)

（4）放包，普通用户wiener成功越权以administrator身份访问/admin页面

![image-20240828200521252](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408282005293.png)

## 2.**JWT** **authentication bypass via flawed signature verification**

（1）普通用户wiener登录，访问/admin页面，回显权限不够

![image-20240828200528688](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408282005727.png)

（2）将JWT中的signature部分删除，分隔符号"."，需要保留

![image-20240828200538068](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408282005119.png)

（3）修改header参数alg为：none，并且修改payload参数sub为：administrator

![image-20240828200545526](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408282005573.png)

![image-20240828200551564](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408282005612.png)

（4）放包，普通用户wiener成功越权以administrator身份访问/admin页面

![image-20240828200600839](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408282006881.png)

（5）删除用户carlos功能抓包，将JWT替换成为上述步骤修改过后的，越权删除用户carlos

![image-20240828200609427](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408282006480.png)

## 3.**JWT** **authentication bypass via weak signing key**

（1）普通用户wiener登录，访问/admin页面，回显权限不够

![image-20240828200615986](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408282006026.png)

（2）hashcat破解JWT密钥：secret1

```py
#hashcat -a 0 -m 16500 JWT 字典路径
hashcat -a 0 -m 16500 eyJraWQiOiJmYmZhMTZkYS1lZTU0LTQ2YjYtODU2My1iZDQ0MmNjZWExZjMiLCJhbGciOiJIUzI1NiJ9.eyJpc3MiOiJwb3J0c3dpZ2dlciIsImV4cCI6MTcyMzAxODM3Miwic3ViIjoid2llbmVyIn0.pGLfb3EbV1kf735C9vzZsTa8kvQwNdAwgPgtPRV-ofs jwt.secrets.list
```

![image-20240828200626703](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408282006774.png)

![image-20240828200638923](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408282006985.png)

（3）将普通用户wiener的JWT，复制到JWT在线签名平台

![image-20240828200648254](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408282006308.png)

![image-20240828200655750](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408282006799.png)

（4）修改payload部分参数sub，使用破解出来的密钥进行JWT加密

![image-20240828200705177](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408282007223.png)

（5）复制修改过后的JWT，替换原本的JWT内容

![image-20240828200716058](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408282007117.png)

（6）放包，成功绕过身份验证

![image-20240828200724898](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408282007941.png)

（7）删除用户carlos功能抓包，将JWT替换成为上述步骤修改过后的，越权删除用户carlos

![image-20240828200733658](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408282007709.png)

## 4.**JWT** **authentication bypass via jwk header injection**

（1）普通用户wiener登录，访问/admin页面，回显权限不够

![image-20240828200743661](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408282007705.png)

（2）使用JWT Editor模块新建RSA密钥

![image-20240828200753000](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408282007050.png)

![image-20240828200800897](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408282008952.png)

（3）payload部分用户声明参数sub修改为：administartor

![image-20240828200811005](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408282008055.png)

（4）Attack选择Embedded JWK（Header部分添加参数JWK，JWK指向新生成的RSA密钥，并且使用该RSA密钥进行签名）

![image-20240828200821985](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408282008035.png)

![image-20240828200830984](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408282008042.png)

![image-20240828200846107](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408282008165.png)

（5）放包，成功越权进入/admin页面

![image-20240828200854295](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408282008337.png)

（6）删除用户carlos功能抓包，重复上述操作，越权删除用户carlos

![image-20240828200903576](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408282009630.png)

![image-20240828200911619](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408282009668.png)

![image-20240828200918988](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408282009037.png)

## 5.**JWT** **authentication bypass via jku header injection**

（1）普通用户wiener登录，访问/admin页面，回显权限不够

![image-20240828200926584](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408282009626.png)

（2）使用JWT Editor模块新建RSA密钥

![image-20240828200935619](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408282009669.png)

（3）复制新创建的RSA密钥放置到HTTP请求伪造网站的body部分

![image-20240828201613945](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408282016005.png)

![image-20240828200956775](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408282009818.png)

```py
#将HTTP请求伪造网站的body部分替换成为keys数组，keys数组放置新建的RSA密钥
{
    "keys": []
}
```

![image-20240828201007594](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408282010643.png)

（4）抓包，将kid值替换新建RSA密钥的kid值，新增jku参数，内容为伪造网址路径，修改用户声明

![image-20240828201033912](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408282010963.png)

![image-20240828201045619](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408282010672.png)

（5）进行Header部分jku参数添加的时候，需要在上一个参数后添加逗号

![image-20240828201053241](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408282010293.png)

（6）利用新建RSA密钥对修改过后的JWT内容进行签名，放包成功越权访问/admin

![image-20240828201114962](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408282011018.png)

![image-20240828201123048](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408282011094.png)

（7）删除用户carlos功能抓包，将JWT替换成为上述步骤修改过后的，越权删除用户carlos

![image-20240828201128622](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408282011664.png)

## 6.**JWT** **authentication bypass via kid header path traversal**

（1）普通用户wiener登录，访问/admin页面，回显权限不够

![image-20240828201139182](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408282011224.png)

（2）生成NSK密钥，使用base64的空字节编码（AA==）替换k生成的值，因为JWT编辑器扩展不允许使用空字符串签署令牌，k为密钥

![image-20240828201147779](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408282011832.png)

（3）修改Header中的kid值指向/dev/null文件（该文件为空），Payload中的用户声明

![image-20240828201157419](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408282011476.png)

（4）利用新建的密钥进行签名

![image-20240828201206678](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408282012731.png)

（5）放包，成功越权访问/admin

![image-20240828201216571](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408282012619.png)

（6）重复上述操作进行越权删除用户，成功

![image-20240828201225820](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408282012866.png)