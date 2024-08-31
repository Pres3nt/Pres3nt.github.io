---
title: Github+Jekyll搭建Chirpy站点完整流程
categories: [环境部署, 博客搭建]
---

## 一、环境配置

1.Ruby安装（安装过程中勾选选项：MSYS2 and MINGW development tool chain）

[Downloads (rubyinstaller.org)](https://rubyinstaller.org/downloads/)

2.Git安装

[Git - Downloads (git-scm.com)](https://git-scm.com/downloads)

3.Jekyll

```py
#Windows+R启动CMD窗口执行命令
gem install jekyll bundler
```

4.检查环境配置

```python
#未报错证明配置完成
ruby -v
gem -v
jekyll -v
```

## 二、博客搭建

（1）使用Jekyll主题Chirpy的官方模板进行Github新建仓库

```python
https://github.com/cotes2020/chirpy-starter/generate
```

![image-20240827103613763](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408271036820.png)

（2）配置Github仓库，并拉取项目至本地文件夹（方便后续进行站点优化和文章发布的文件夹，建议新建一个）

![image-20240827103802965](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408271038030.png)

![image-20240827103907006](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408271039083.png)

![image-20240827105214500](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408271052549.png)

![image-20240827105042948](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408271050023.png)

![image-20240827105304569](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408271053648.png)

（3）进入刚刚拉取的项目根目录下，进行本地环境的搭建

```py
#执行下面命令如出现如图所示的报错时,找到根目录下的Gemfile文件,删除其最后一行内容即可
bundle exec jekyll serve
```

![image-20240831164759978](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408311648038.png)

![image-20240831164830759](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408311648820.png)

![image-20240831164853879](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408311648946.png)

（4）复制隐藏的开发文件夹到项目根目录下，方便后续对站点的优化

```python
#执行以下命令找到隐藏文件的路径
bundle info --path jekyll-theme-chirpy
```

![image-20240831200451776](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408312004842.png)

![image-20240831200605084](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408312006149.png)

## 三、推送GitHub

将项目推送至Github后访问仓库名，出现Demo模板时证明博客站点部署完成

后续博客的修改优化以及文章发布时只需在本地项目进行修改再同步推送Github即可

```python
#项目根目录下执行命令将本地项目同步推送至Github
git add ./文件名		   //将文件添加至本地仓库("."代表所有文件)
git commit -m "注释"		//提交修改,填写变更注释
git push -u origin 		  //本地项目同步Github远程仓库
```

![image-20240831201744872](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408312017933.png)

![image-20240831201850503](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408312018583.png)

## 四、博客优化

参考以下官方文档和大佬博客进行站点的后续相关配置和优化

https://winxuan.github.io/categories/build/

https://pansong291.github.io/chirpy-demo-zhCN/posts/write-a-new-post/

https://zhaozq2.github.io/posts/cust-theme-chirpy/