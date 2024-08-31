---

title: PortSwigger--SQL injection
categories: [渗透靶场, PortSwigger]

---

## 1.**SQL injection** **vulnerability** **in WHERE clause allowing retrieval of hidden data**

（1）商店界面筛选礼物类商品，页面显示3个产品

![image-20240827231021801](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408272310941.png)

![image-20240827231056249](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408272310332.png)

（2）闭合SQL语句，注释符注入，显示隐藏商品

![image-20240827231115886](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408272311981.png)

![image-20240827231129961](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408272311075.png)

## 2.**SQL injection** **vulnerability** **allowing login bypass**

（1）单引号闭合用户名SQL语句并进行注释符注释

![image-20240827231217510](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408272312593.png)

（2）实现免密登录

![image-20240827231223509](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408272312585.png)

## 3.**SQL injection attack, querying the database type and version on Oracle**

（1）order by判断列数

![image-20240827231305977](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408272313065.png)

![image-20240827234001853](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408272340977.png)

（2）字符串常量判断列数据类型

![image-20240827234022975](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408272340086.png)

![image-20240827234030394](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408272340524.png)

（3）查询oracle数据库版本信息

![image-20240827231357163](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408272313243.png)

![image-20240827231408594](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408272314677.png)

## 4.**SQL injection attack, querying the database type and version on** **MySQL** **and Microsoft**

（1）order by 判断列数

![image-20240827231419131](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408272314218.png)

![image-20240827231426917](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408272314010.png)

（2）判断列的数据类型以及回显

![image-20240827234055683](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408272340806.png)

（3）查询数据库版本信息

![image-20240827231452843](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408272314937.png)

![image-20240827231507202](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408272315294.png)

## 5.**SQL injection attack, listing the database contents on non-Oracle databases**

（1）判断数据库列数为2

![image-20240827231547672](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408272315768.png)

![image-20240827231555570](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408272315663.png)

（2）判断各列的数据类型均为字符串

![image-20240827234121808](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408272341945.png)

（3）查询数据库版本信息

![image-20240827234141659](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408272341814.png)

（4）查询所有的表名

![image-20240827234151720](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408272341863.png)

（5）排除默认的表，筛选user表

![image-20240827234211710](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408272342850.png)

![image-20240827234220513](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408272342653.png)

（6）列出users_sdudlf表的段名，发现username和password

![image-20240827234234272](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408272342393.png)

（7）查询username表和password表中的数据，成功拿到管理员账号密码

![image-20240827234251918](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408272342064.png)

![image-20240827234300056](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408272343178.png)

## 6.**SQL injection attack, listing the database contents on Oracle**

（1）判断列数为2

![image-20240827234313767](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408272343890.png)

![image-20240827234328682](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408272343847.png)

（2）判断各列的数据类型为字符串

![image-20240827234340904](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408272343029.png)

（3）列出所有的表名

![image-20240827234348836](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408272343971.png)

（4）列出user段名

![image-20240827231754086](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408272317172.png)

（5）爆数据

![image-20240827234409476](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408272344595.png)

![image-20240827234423652](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408272344773.png)

## 7.**SQL injection UNION attack, determining the number of columns returned by the** **query**

（1）order by/union select 判断列数

![image-20240827231840533](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408272318618.png)

![image-20240827231928314](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408272319420.png)

![image-20240827231939004](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408272319097.png)

## 8.**SQL injection UNION attack, finding a column containing text**

（1）order by 判断列数

![image-20240827231949821](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408272319918.png)

（2）union select 'a','b','c'进行可回显列数判断

![image-20240827232004339](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408272320423.png)

## 9.**SQL injection UNION attack, retrieving data from other tables**

（1）判断列数为2

![image-20240827232045038](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408272320154.png)

![image-20240827232056564](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408272320653.png)

（2）判断各列的数据类型都为字符串

![image-20240827232105167](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408272321269.png)

（3）判断数据库类型

![image-20240827232124924](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408272321041.png)

（4）去掉pg默认表，找到users表

![image-20240827232138841](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408272321971.png)

（5）列出users表中的段，拿到用户名和密码

![image-20240827232147634](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408272321753.png)

![image-20240827232155225](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408272321336.png)

（6）登录

![image-20240827232214269](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408272322360.png)

## 10.**SQL injection UNION attack, retrieving multiple values in a single column**

（1）判断数据库版本

![image-20240827232222004](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408272322100.png)

（2）判断列的数值类型和可回显列

![image-20240827232229268](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408272322348.png)

![image-20240827232240047](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408272322157.png)

（3）查询所有表名

![image-20240827232256292](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408272322409.png)

（4）查询users表中所有的段

![image-20240827232304235](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408272323339.png)

（5）将username和password合并一列带出

![image-20240827232319896](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408272323996.png)

![image-20240827232332199](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408272323316.png)

## 11.**Blind SQL injection with conditional responses**

（1）寻找注入点，category参数不存在注入点

![image-20240827232446643](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408272324785.png)

（2）cookie参数存在注入

![image-20240827232456681](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408272324811.png)

![image-20240827232506105](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408272325266.png)

（3）判断数据库，返回正确，mysql数据库

![image-20240827232540198](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408272325299.png)

![image-20240827232549531](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408272325643.png)

（4）判断是否存在users表，返回为真

![image-20240827232601450](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408272326553.png)

（5）判断是否存在管理员用户，返回为真

![image-20240827232610015](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408272326125.png)

（6）爆破判断administrator用户的密码长度为20位，当大于20时，条件不成立

![image-20240827232618146](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408272326246.png)

![image-20240827232629962](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408272326068.png)

（7）爆破administrator用户密码

![image-20240827232637026](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408272326152.png)

![image-20240827232647560](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408272326683.png)

![image-20240827232655237](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408272326339.png)

（8）按payload的顺序进行排序，成功登录administrator用户

![image-20240827232703125](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408272327251.png)

![image-20240827232709364](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408272327463.png)

## 12.**Blind SQL injection with conditional errors**

（1）判断注入点

![image-20240827232717911](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408272327013.png)

（2）判断数据库版本为oracle

![image-20240827232732278](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408272327395.png)

![image-20240827232745180](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408272327278.png)

（3）判断存在users表，存在administrator用户

![image-20240827232752890](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408272327000.png)

![image-20240827232808877](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408272328978.png)

（4）用户密码为20位

![image-20240827232820540](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408272328649.png)

（5）逐字符爆破用户密码

![image-20240827232829955](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408272328078.png)

![image-20240827232911682](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408272329784.png)

![image-20240827232926529](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408272329649.png)

（6）成功登录administrator用户

![image-20240827232935386](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408272329496.png)

![image-20240827232947818](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408272329916.png)

## 13.**Visible error-based SQL injection**

（1）确认注入点，当SQL语句异常时，服务器会抛出异常数据内容

![image-20240827233021090](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408272330212.png)

（2）判断列数为1，并且SQL注入无回显

![image-20240827233031168](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408272330288.png)

![image-20240827233039431](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408272330576.png)

（3）删除用户ID，cast函数将ID进行数据类型转换为整型，转换失败发现会抛出该数据

![image-20240827233047038](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408272330171.png)

（4）利用cast函数异常转换数据抛出

![image-20240827233057859](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408272330996.png)

（5）cast只能处理单个数据

![image-20240827233106509](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408272331626.png)

（6）cast对列的返回内容进行限定

![image-20240827233116931](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408272331043.png)

![image-20240827233123489](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408272331629.png)

（7）成功利用报错回显拿到administrator用户的账号密码

![image-20240827233135457](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408272331569.png)

## 14.**Blind SQL injection with time delays**

（1）参数插入

![image-20240827233148527](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408272331650.png)

（2）mysql时延判断，基本无延迟

![image-20240828002439166](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408280024295.png)

（3）PortgreSQL时延注入判断，成功延迟

![image-20240827233212445](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408272332556.png)

![image-20240827233235952](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408272332062.png)

## 15.**Blind SQL injection with time delays and** **information retrieval**

（1）时延注入判定为pg数据库

![image-20240827233319426](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408272333552.png)

（2）时延利用确认administrator用户的存在

![image-20240827233334464](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408272333587.png)

（3）根据时延判断用户密码，这里BP的线程需要设置为1，否则响应时间可能会导致不一样

![image-20240827233345358](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408272333516.png)

（4）利用时延回显不同来爆破密码，同样线程许需要设置为1

![image-20240827233356357](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408272333469.png)

![image-20240827233404211](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408272334331.png)

![image-20240827233414243](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408272334365.png)

(5)成功登录

![image-20240827233442548](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408272334648.png)

## 16.**Blind SQL injection with out-of-band interaction**

（1）开启外带服务器，将服务器地址粘贴至XML中的远程URL

![image-20240827233454689](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408272334806.png)

![image-20240827233507397](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408272335543.png)

（2）成功外带

![image-20240827233525947](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408272335089.png)

![image-20240827233540864](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408272335020.png)

## 17.**Blind SQL injection with out-of-band data exfiltration**

（1）开启服务器并且复制URL地址

![image-20240827233623439](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408272336562.png)

（2）注入语句拼接URL插入XML中

![image-20240827233630138](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408272336262.png)

（3）放包成功外带管理员账号密码

![image-20240827233638359](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408272336468.png)

![image-20240827233651323](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408272336471.png)

## 18.**SQL injection with** **filter** **bypass via XML encoding**

（1）进入商品页面，存在检查库存功能点，抓包发现存在XML引用

![image-20240827233701545](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408272337698.png)

![image-20240827233732499](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408272337641.png)

（2）尝试在XML实体中进行SQL注入，发现服务端存在WAF拦截

![image-20240827233743939](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408272337067.png)

（3）将SQL语句进行编码，绕过WAF对XML实体的字符串检测，判断列数为1

```py
#XML在线加密网站
https://coderstoolbox.net/string/#!encoding=xml&action=encode&charset=none
```

![image-20240827233755210](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408272337344.png)

![image-20240827233802968](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408272338100.png)

![image-20240827233810382](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408272338498.png)

（4）通过XML实体注入实现联合查询

![image-20240827233822328](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408272338439.png)

![image-20240827233834525](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408272338647.png)

![image-20240827233854241](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408272338369.png)

![image-20240827233901888](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408272339023.png)

（5）成功登录administrator用户

![image-20240827233909494](https://cdn.jsdelivr.net/gh/Pres3nt/Typoraimages@master/images/202408272339619.png)