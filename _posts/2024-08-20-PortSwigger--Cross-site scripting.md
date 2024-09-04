---

title: PortSwigger--Cross-site scripting
categories: [渗透靶场, PortSwigger]

---

## 知识扩展

- XSS测试流程

  存在数据交互框（输入框），提交测试数据，观察数据是否回显，数据不回显则进行源码查看寻找是否存在可利用的javascript代码段

  

- document.write是Javascript向动态页面写入内容的一个方法

  通过document.write方法向页面插入内容时，可以通过闭合原标签并且拼接<script>标签代码从而实现弹窗

  

- innerhtml是javascript中获取或设置HTML内容的一个属性，可以替换或更新元素的内容

  浏览器为了防止XSS，禁止innerHTML执行<script>标签的代码，因此，当页面是通过innerhtml属性实现元素内容写入时，需要借助事件处理来触发XSS

  

- attr()是javascript/jquery中获取或设置HTML元素属性的函数，简单来说就是attr()可以改变HTML元素的属性

  

- javascript:xxxxx 是一种URL协议

  当用户在URL参数中输入/点击链接带有javascript:js代码时，浏览器会执行javascript:后面跟随的JS代码

  javascript:协议常用于URL传参处/href元素处

  

- 尖括号被编码，可以通过事件处理实现弹窗



## 1.**Reflected** **XSS** **into** **HTML** **context with nothing encoded**

（1）输入框提交测试数据，数据回显，说明用户输入会写入HTML代码中

![image-20240830173919108](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408301739182.png)

（2）插入<script>标签代码实现弹窗

![image-20240903150619868](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202409031506968.png)

![image-20240904130757420](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202409041307510.png)

![image-20240903150647822](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202409031506912.png)

## 2.**Stored** **XSS** **into** **HTML** **context with nothing encoded**

（1）评论处三个输入框提交测试数据，comment输入框内容回显

![image-20240903151319191](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202409031513272.png)

![image-20240903151402549](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202409031514634.png)

（2）Comment输入框提交弹窗代码，访问blog页面自动触发弹窗

![image-20240903151658253](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202409031516339.png)

![image-20240903151725382](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202409031517461.png)

![image-20240903151717126](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202409031517195.png)

## 3.**DOM** **XSS** **in** document.write **sink using source** location.search

（1）输入框提交测试数据，数据回显

![image-20240903152136672](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202409031521763.png)

（2）提交弹窗代码，无法实现弹窗

![image-20240903152209980](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202409031522046.png)

（3）查看网页源码，trackSearch函数通过document.write将<img>内容写入HTML页面中，GET获取query

![image-20240904130300563](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202409041303681.png)

（4）相比于<script>标签直接插入HTML源码中，这里页面是通过document.write来将内容写入页面中的，因此只需要闭合<img>标签，再拼接<script>标签，那么document.write在写入页面时会将<img>和<script>的内容一起写入页面源码中

```python
"><script>alert('hacker')</script>
```

![image-20240903153234818](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202409031532911.png)

![image-20240903153014999](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202409031530076.png)

## 4.**DOM** **XSS** **in** innerHTML **sink using source** location.search

（1）输入框提交测试数据，数据回显，插入弹窗代码无法实现弹窗

![image-20240903224841338](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202409032248426.png)

![image-20240903224807096](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202409032248189.png)

（2）查看源码，query通过GET获取，query的值并不是直接插入页面源码中，而是通过innerHTML方法将query的值传入searchmessage，需要注意的是innerHTML不会执行<script>标签的代码，因此需要通过事件处理程序属性进行XSS触发

```py
#当<img>标签中的src中指向异常值时,将会执行onerror语句
<img src=1 onerror=alert('hacker')>
```

![image-20240903225214209](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202409032252310.png)

![image-20240903225838324](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202409032258435.png)

## 5.**DOM** **XSS** **in** **jQuery** **anchor** href **attribute sink using** location.search **source**

（1）Submit提交测试数据，不回显

![image-20240903230052330](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202409032300404.png)

![image-20240903230142342](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202409032301408.png)

（2）查看源码，attr()函数将returnpath的内容转换为href，并且将其赋予给backlink，当用户点击back元素时页面会跳转至returnpath内容处

![image-20240903230800978](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202409032308051.png)

（3）变量传参，<script>标签的代码无法被直接执行，既然returnpath是GET获取，那么就可以通过javascript:方案来直接执行JS代码

```python
javascript:alert('hacker')
```

![image-20240903233830544](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202409032338592.png)

![image-20240903233838603](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202409032338658.png)

## 6.**DOM** **XSS** **in** **jQuery** **selector** **sink using a hashchange event**

（1）页面除了View blog没有其他地方可进行数据交互，进行源码查看，并且检索<script>标签

![image-20240903235056689](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202409032350794.png)

（2）代码看不懂没关系，让GPT分析一下，简单来说就是页面准许URL通过"#"来进行页面的跳转

![image-20240903235527005](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202409032355074.png)

（3）<scrpit>标签无法直接执行，需要通过事件处理实现弹窗

```python
#<img src=1 onerror=alert(1)>
```

![image-20240903235857618](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202409032358685.png)

## 7.**Reflected** **XSS** **into attribute with angle brackets HTML-encoded**

（1）输入框提交测试数据，内容回显，<script>标签无法实现弹窗

![image-20240904000331881](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202409040003940.png)

![image-20240904000434036](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202409040004095.png)

（2）源码查看，发现<script>标签作为input的value值，尝试闭合input后发现尖括号被URL编码

![image-20240904000802996](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202409040008076.png)

![image-20240904000936765](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202409040009840.png)

（3）要绕过尖括号被编码，同样可以通过使用事件处理实现弹窗

```python
#autofocus 在页面加载完成时,自动将焦点(光标)放置在输入框上,无需用户点击即可输入
#onfocus javascript事件属性,当元素获得焦点时自动触发,由于输入框自动获得焦点(光标)，onfocus事件被触发
"autofocus onfocus=alert('hacker') "
```

![image-20240904104950599](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202409041049663.png)

## 8.**Stored** **XSS** **into anchor** `href` **attribute with double quotes HTML-encoded**

（1）页面除了View blog 没有其他地方可以进行数据交互，进行源码查看，并检索<script>标签，观察是否存在可利用代码段

![image-20240904110706819](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202409041107904.png)

（2）Home页面无可利用处，进入View Blog 输入框提交测试数据，发现name和comment都回显

![image-20240904110931808](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202409041109867.png)

![image-20240904111854140](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202409041118202.png)

（3）<script>标签插入尝试，<script>并没有被执行，源码中检索<script>标签，发现尖括号被进行编码

![image-20240904111836255](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202409041118314.png)

![image-20240904112049509](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202409041120584.png)

（4）但是查看评论时可以发现website输入的内容会变成href属性，那么在href中插入javascript:方案是否能直接实现弹窗

![image-20240904111101518](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202409041111593.png)

（5）Website内容注入javascript:方案，点击实现弹窗

![image-20240904112301448](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202409041123511.png)

![image-20240904112452245](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202409041124306.png)

![image-20240904112502041](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202409041125093.png)

## 9.**Reflected** **XSS** **into a** **JavaScript** **string with angle brackets** **HTML** **encoded**

（1）输入框提交测试数据，发现内容回显，插入<script>标签没实现弹窗

![image-20240904112941920](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202409041129987.png)

![image-20240904113007231](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202409041130302.png)

（2）查看源码，检索<script>标签，发现encodeURIComponent函数将用户输入的特殊字符进行URL编码

![image-20240904114227498](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202409041142584.png)

（3）document.write向页面中插入<img>，如果能闭合searchterms并且拼接弹窗函数就能实现弹窗

```python
#单引号闭合searchterms,分号执行多条语句
';alert('hacker')
```

![image-20240904114725494](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202409041147590.png)

（4）注释掉后面自带的分号，成功实现弹窗

```python
';alert('hacker')//
```

![image-20240904113647156](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202409041136224.png)

## 10.DOM XSS in `document.write` sink using source `location.search` inside a select element

（1）页面源码检索<script>标签查看是否存在可利用代码段

![image-20240904213616803](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202409042136981.png)

（2）Home无利用代码段，进入商品页面进行源码查看，document.write将<option>和store拼接后写入页面，GET获取storeid

![image-20240904213848742](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202409042138871.png)

（3）闭合<option>拼接<script>标签即可实现弹窗

```python
&storeId=</option><script>alert('hacker')</script>
```

![image-20240904214228388](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202409042142446.png)