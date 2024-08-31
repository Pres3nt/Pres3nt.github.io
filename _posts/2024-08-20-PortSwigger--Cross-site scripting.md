---

title: PortSwigger--Cross-site scripting
categories: [渗透靶场, PortSwigger]

---

## 知识扩展

## 1.**Reflected** **XSS** **into** **HTML** **context with nothing encoded**

（1）输入框提交数据123，数据回显

![image-20240830173919108](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408301739182.png)

（2）插入<script>标签代码实现弹窗

## 2.**Stored** **XSS** **into** **HTML** **context with nothing encoded**

（1）评论处测试数据

（2）name处插入代码，虽然插入恶意代码，但是浏览器将其作为文本存贮不进行解析，因此不会执行

（3）comment处插入JS代码，浏览器将其作为HTML标签解析执行，访问页面自动触发弹窗

## 3.**DOM** **XSS** **in** document.write **sink using source** location.search

（1）判断是否写入HTML代码，输入参数被img参数控制

（2）抓包判断HTML是否解析

（3）闭合img参数再进行<script>标签的执行（反射型XSS）

（4）闭合img参数再进行<svg>标签的执行（DOM型XSS）

## 4.**DOM** **XSS** **in** innerHTML **sink using source** location.search

（1）确定源码

```python
innerHTML是DOM属性
innerHTML会将query的内容直接插入searchMessage参数中，如果query包含HTML标签或脚本，innerHTML会将其进行解析并执行
query的值为参数search的值
```

（2）插入<script>标签，无法写入HTML代码中：innerHTML属性不接受任何脚本元素

```python
<script>alert('hacker')</script>
```

（3）

```python
<img src=1 onerror=alert(1)>
```



## 5.**DOM** **XSS** **in** **jQuery** **anchor** href **attribute sink using** location.search **source**

（1）随意输入字符，发现不会被写入HTML源码中

```py
#JS代码解析
源码将returnpath参数的值赋值为href属性
href属性是backlink的元素，简单来说就是returnpath的URL赋值给backlink，当用户点击back功能时候会跳转至returnpath指定的URL
```

（2）为returnpath参数赋值

```python
#脚本解析：javascript当出现在URL中时，浏览器将会执行：后面的代码
javascript:alert('hacker')
```

## 6.**DOM** **XSS** **in** **jQuery** **selector** **sink using a hashchange event**

（1）hashchange事件

```python
当URL中的哈希部分"#"后的内容发生变化时，浏览器会触发hashchange事件
这段代码根据URL中的哈希值滚动到页面中对应"h2"元素处
```

（2）插入hash值，实现弹窗

```python
'#<img src=1 onerror=alert(1)>'
```

## 7.**Reflected** **XSS** **into attribute with angle brackets HTML-encoded**

（1）输入作为文本，赋值给value，因此并不会直接执行<script>标签

（2）onmouseover属性适用所有HTML元素

（3）当鼠标指向search框时，触发弹窗

## 8.**Stored** **XSS** **into anchor** `href` **attribute with double quotes HTML-encoded**

（1）输入测试

（2）查看源码，发现Website能直接控制href的值

（3）设置href的值

（4）点击触发弹窗

## 9.**Reflected** **XSS** **into a** **JavaScript** **string with angle brackets** **HTML** **encoded**

（1）输入测试，发现输入直接在<script>标签中

（2）尝试闭合执行弹窗