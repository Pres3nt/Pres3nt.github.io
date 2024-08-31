---

title: BurpSuite抓包Js文件
categories: [工具使用, BurpSuite]

---

（1）启动BP，进入设置

![image-20240828232655782](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408282326860.png)

（2）在设置界面配置请求包的条件

![image-20240828232708254](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408282327323.png)

（3）进入edit，将符号|^js$进行删除，即可抓包js文件

![image-20240828232715402](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408282327507.png)

![image-20240828232727871](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408282327947.png)

（4）回到浏览器，开启BP监听，访问js文件，发现此时依旧不可抓包

![image-20240828232737623](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408282327713.png)

（5）在此页面进行ctrl+F5操作（刷新浏览器缓存），成功抓包js文件

![image-20240828232754492](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408282327594.png)