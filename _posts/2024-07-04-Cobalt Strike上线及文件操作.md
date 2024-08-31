---

title: Cobalt Strike上线及文件操作
categories: [工具使用, Cobalt Strike]

---

（1）进入CS文件夹，该目录下开启终端

![image-20240828233735789](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408282337898.png)

（2）ROOT身份启动CS服务端

```py
#./teamserver 攻击机IP 连接密码（自己设置）
./teamserver 192.168.88.128 1234
```

![image-20240829004900438](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408290049523.png)

（3）同样在该文件夹目录下下，另启动一个终端，开启客户端

![image-20240828233803220](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408282338293.png)

（4）修改启动窗口参数，进入CS

![image-20240828233812155](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408282338275.png)

![image-20240828233819581](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408282338668.png)

（5）创建监听器

![image-20240828233832570](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408282338680.png)

![image-20240828233840028](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408282338149.png)

（6）监听器设置

![image-20240828233852474](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408282338592.png)

（7）生成payload，选择刚刚创建的监听器

![image-20240828233902108](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408282339214.png)

![image-20240828233913133](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408282339226.png)

（8）将payload复制至Windows靶机中，双击运行，回到CS页面，成功上线

![image-20240828233924749](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408282339912.png)

![image-20240828233936233](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408282339343.png)

（9）右键创建会话交互，会话窗口生成之后，再次右键设置回连间隔为1

![image-20240828233947126](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408282339230.png)

![image-20240828233952109](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408282339210.png)

![image-20240828234002480](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408282340569.png)

（10）回连间隔设置完成后，再次右键选择文件浏览

![image-20240828234010908](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408282340994.png)

（11）进行文件上传

![image-20240828234025417](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408282340530.png)

![image-20240828234037263](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408282340346.png)

![image-20240828234047333](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408282340424.png)

（12）Windows靶机创建文件，CS页面刷新

![image-20240828234052768](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408282340873.png)

（13）找到该文件，右键选择下载

![image-20240828234103317](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408282341409.png)

（14）打开下载列表，将文件下载至本地

![image-20240828234109616](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408282341700.png)

![image-20240828234121771](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408282341879.png)

![image-20240828234136778](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408282341898.png)