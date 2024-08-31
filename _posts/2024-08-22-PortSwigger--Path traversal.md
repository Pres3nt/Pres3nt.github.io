---

title: PortSwigger--Path traversal
categories: [渗透靶场, PortSwigger]

---

## 1.**File path traversal, simple case**

（1）开启Burpsuite代理，访问主页观察HTTP流量

![image-20240828194249750](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281942796.png)

（2）选择一个带filename参数的请求发送至Repeate，进行重放-->图片是通过filename参数指向的图片文件位置进行读取的

![image-20240828194300808](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281943852.png)

（3）目录穿越读取配置文件

![image-20240828194308059](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281943104.png)

![image-20240828194317223](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281943257.png)

## 2.**File path traversal, traversal sequences blocked with absolute path bypass**

（1）开启Burpsuite代理，访问主页观察HTTP流量

![image-20240828194338889](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281943933.png)

（2）选择一个带filename参数的请求发送至Repeate，进行重放

![image-20240828194345591](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281943634.png)

（3）服务端提取路径进行白名单比较-->绝对路径读取

![image-20240828194406178](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281944225.png)

![image-20240828194416758](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281944793.png)

## 3.**File path traversal, traversal sequences stripped non-recursively**

（1）开启Burpsuite代理，访问主页观察HTTP流量

![image-20240828195244949](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281952990.png)

（2）选择一个带filename参数的请求发送至Repeate，进行重放

![image-20240828195251936](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281952979.png)

（3）服务端将"./"删除或者置空-->双写"../"绕过

![image-20240828195302216](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281953257.png)

![image-20240828195312502](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281953537.png)

## 4.**File path traversal, traversal sequences stripped with superfluous URL-decode**

（1）开启Burpsuite代理，访问主页观察HTTP流量

![image-20240828195320149](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281953196.png)

（2）选择一个带filename参数的请求发送至Repeate，进行重放

![image-20240828195327017](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281953061.png)

（3）服务端过滤"/"-->将"/"进行编码，一次编码不过可以尝试多次编码

![image-20240828195337301](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281953343.png)

![image-20240828195344554](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281953602.png)

![image-20240828195353960](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281953995.png)

## 5.**File path traversal, validation of start of path**

（1）开启Burpsuite代理，访问主页观察HTTP流量

![image-20240828195401469](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281954520.png)

（2）选择一个带filename参数的请求发送至Repeate，进行重放

![image-20240828195408001](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281954045.png)

（3）服务端验证路径初始部分-->保留初始路径再配合目录穿越

![image-20240828195417815](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281954868.png)

![image-20240828195426178](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281954213.png)

## 6.**File path traversal, validation of file extension with null byte bypass**

（1）开启Burpsuite代理，访问主页，观察HTTP流量

![image-20240828195436535](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281954581.png)

（2）选择一个带filename参数的请求发送至Repeate，进行重放

![image-20240828195446668](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281954714.png)

（3）服务端对文件后缀进行验证-->%00进行截断绕过

%00截断字符.jpg，同时服务器将.jpg作为后缀解析，但是文件处理读取../../../../etc/passwd

![image-20240828195507372](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281955423.png)

![image-20240828195513974](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281955013.png)

