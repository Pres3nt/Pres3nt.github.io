---

title: Vulnhub--hacknos2
categories: [渗透靶场, Vulnhub]

---

## 渗透复现

1. 目标站点为wordpress，通过wpscan进行漏洞扫描发现漏洞插件
2. 通过漏洞插件存在的目录穿越漏洞成功读取/etc/passwd文件中flag用户的密码
3. SSH登录flag用户后在备份文件中找到rohit用户的密码
4. 切换rohit用户，rohit用户能够以root权限执行任何文件，随后进行sudo su提权

## 知识扩展

1. wpsacn扫描wordpress站点
2. 以root权限切换root用户时，无需输入root密码

## 靶机地址

https://www.vulnhub.com/entry/hacknos-os-hacknos-21,403/

## 主机发现

（1）攻击机

```py
192.168.88.128
```

（2）目标靶机

```py
192.168.88.143
```

## 信息收集

### 端口扫描

![image-20240828160612323](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281606376.png)

### 端口访问

![image-20240828160622907](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281606958.png)

### 目录扫描

![image-20240828160634064](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281606119.png)

### 目录爆破

![image-20240828160640831](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281606882.png)

### 目录访问

（1）/tsweb/

![image-20240828160648894](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281606939.png)

## 实施攻击

（1）/tsweb目录扫描

![image-20240828160655529](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281606581.png)

（2）/tsweb/wp-login.php和/tsweb/wp-includes/访问，发现目标站点为wordpress

![image-20240828160705356](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281607400.png)

![image-20240828160709749](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281607795.png)

（3)利用wpscan进行漏洞扫描

```py
#扫描所有插件
wpscan --url http://192.168.88.143/tsweb/ -e ap
```

![image-20240828160717747](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281607800.png)

（4）插件gracemedia-media-player进行利用，该插件存在目录穿越漏洞，访问相关路径确实存在

![image-20240828160733606](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281607656.png)

![image-20240828160742253](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281607289.png)

（5）访问敏感文件，可以看到flag用户的密码，进行john爆破

![image-20240828160751195](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281607236.png)

![image-20240828160759549](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281607590.png)

![image-20240828160808462](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281608507.png)

（6）SSH登录flag用户

![image-20240828160816272](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281608326.png)

## 提权

（1）信息收集

```py
#flag用户的shell是rbash shell，需要提升shell权限
python3 -c 'import pty;pty.spawn("/bin/bash")'
```

![image-20240828160827740](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281608786.png)

![image-20240828160838150](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281608217.png)

![image-20240828160845524](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281608570.png)

（2）常规信息收集不具备提权条件，进行备份文件的查找或者上传内核漏洞检测脚本

![image-20240828160852809](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281608869.png)

![image-20240828160902966](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281609024.png)

（3）在备份文件/var/backups/passbkp中成功找到rohit用户的密码

```py
$1$rohit$01Dl0NQKtgfeL08fGrggi0
```

![image-20240828160914562](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281609608.png)

![image-20240828160924353](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281609397.png)

（4）密码进行john解密并切换至用户rohit

![image-20240828160932454](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281609497.png)

![image-20240828160942466](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281609515.png)

（5）信息收集，rohit用户能够以root权限执行任何文件

![image-20240828160953092](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281609131.png)

（6）提权成功，以root权限执行su，无需输入root密码即可切换root用户

![image-20240828161002219](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281610262.png)