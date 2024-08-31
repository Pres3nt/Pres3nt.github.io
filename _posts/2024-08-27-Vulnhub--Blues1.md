---

title: Vulnhub--Blues1
categories: [渗透靶场, Vulnhub]

---

## 渗透复现

1. 目标主机开放21端口，存在ZIP压缩文件的泄露
2. 破解ZIP文件提示参数drip，结合FUZZ爆破出来的index.php页面，进行文件包含的尝试
3. 通过文件包含读取出/etc/passwd，和robots.txt泄露的/etc/dripispowerful.html文件，进行SSH远程登录
4. 获取Shell后发现存在SUID-Polkit文件，通过Polkit漏洞成功提权

## 知识扩展

1. FTP端口的匿名访问
2. 页面index.php，如果存在参数，可能存在文件包含
3. Polkit提权可以利用python脚本

## 靶机地址

https://www.vulnhub.com/entry/dripping-blues-1,744/

## 主机发现

（1）攻击机

```py
192.168.88.128
```

（2）目标主机

```py
192.168.88.138
```

## 信息收集

### 端口扫描

![image-20240828153908341](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281539516.png)

### 端口访问

（1）80端口

![image-20240828153919866](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281539939.png)

（2）21端口

```py
#anonymous匿名访问
#默认密码为：anonymous
ftp 192.168.88.138
anonymous

#将压缩包下载至本地
get respectmydrip.zip
```

![image-20240828153930163](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281539270.png)

![image-20240828153953696](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281539801.png)

### 目录扫描

```py
http://192.168.88.138/
```

![image-20240828153959665](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281539751.png)

### 目录爆破

![image-20240828154018985](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281540104.png)

### 目录访问

（1）/robots.txt

![image-20240828154033503](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281540623.png)

（2）/dripisreal.txt

![image-20240828154041963](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281540057.png)

（3）/etc/dripispowerful.html

![image-20240828154051075](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281540158.png)

## 实施攻击

（1）解压ftp端口获取到的zip包，进行压缩包密码的破解

![image-20240828154100802](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281541911.png)

![image-20240828154113943](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281541069.png)

（2）查看压缩包，存在txt文件和secret压缩包，并且secret压缩包无法破解

![image-20240828154124696](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281541833.png)

（3）txt提示drip，而目录存在index.php，因此index.php可能存在参数为drip的文件包含（切换源代码查看更加清晰）

![image-20240828154141915](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281541011.png)

![image-20240828154202550](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281542702.png)

（4）可以读取/etc/passwd文件，再进一步尝试读取/etc/shadow文件

![image-20240828154210723](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281542825.png)

（5）/etc/shadow文件无法读取，尝试读取先前的/etc/dripispowerful.html文件

```py
#找到用户账号密码
thugger/imdrippinbiatch
```

![image-20240828154219115](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281542217.png)

（6）ssh远程进行登录

![image-20240828154229401](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281542508.png)

## 提权

（1）信息收集

![image-20240828154242824](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281542923.png)

![image-20240828154254557](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281542675.png)

（2）存在Suid-polkit文件，目标主机没有gcc，make环境，GLIBC版本为2.31

![image-20240828154308361](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281543471.png)

（3）采用CVE-2021-3560的python脚本进行提权

```py
#靶机没有git命令，将脚本下载至攻击机再上传至目标主机
https://github.com/Almorabea/Polkit-exploit
```

![image-20240828154321891](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281543970.png)

![image-20240828154331478](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281543590.png)

（4）成功提权

![image-20240828154340153](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281543263.png)