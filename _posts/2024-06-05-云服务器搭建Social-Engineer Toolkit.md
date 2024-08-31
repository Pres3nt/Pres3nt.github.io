---

title: 云服务器搭建Social-Engineer Toolkit
categories: [环境部署, 渗透工具]

---

## 云服务器搭建Social-Engineer Toolkit

```python
git clone https://github.com/trustedsec/social-engineer-toolkit.git
cd social-engineer-toolkit.git
pip install -r requirements.txt
python3 setup.py
```

## 克隆站点进行钓鱼

（1）启动SET工具，选1，进入社会工程攻击

```python
setoolkit
```

![image-20240828180657879](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281806930.png)

（2）选2，进行钓鱼网站攻击，进入webattack

![image-20240828180704093](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281807139.png)

（3）选3，身份验证获取攻击，再选3，进行Web站点克隆

![image-20240828180712425](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281807470.png)

（4）选择完成后回车，输入想要克隆的URL页面

![image-20240828180722190](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281807235.png)

（5）IP+端口（默认80）进行页面访问，如图，该页面即为钓鱼网站

![image-20240828180743206](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281807254.png)

（6）随便输入账号密码进行登录，成功获取到账号密码，并且钓鱼完成后网页跳转至真实网页

![image-20240828180756619](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281807662.png)

![image-20240828180805308](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408281808350.png)