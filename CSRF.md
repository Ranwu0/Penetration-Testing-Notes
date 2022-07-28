# CSRF（跨站请求伪造漏洞）

**Cross-site request forgery简称为"CSRF"**

**在CSRF的攻击场景中攻击者会伪造一个请求(这个请求一般是一 个链接)**
**然后欺骗目标用户进行点击，用户一旦点击了这个请求，整个攻击也就完成了。**
**所以CSRF攻击也被称为为" one click "攻击**

 **CSRF概念：**[CSRF](https://so.csdn.net/so/search?q=CSRF&spm=1001.2101.3001.7020)跨站点请求伪造(Cross—Site Request Forgery)，跟XSS攻击一样，存在巨大的危害性，你可以这样来理解：
    攻击者盗用了你的身份，以你的名义发送恶意请求，对服务器来说这个请求是完全合法的，但是却完成了攻击者所期望的一个操作，比如以你的名义发送邮件、发消息，盗取你的账号，添加系统管理员，甚至于购买商品、虚拟货币转账等。 如下：其中Web A为存在CSRF漏洞的网站，Web B为攻击者构建的恶意网站，User C为Web A网站的合法用户。

# CSRF原理

**是因为web应用程序在用户敏感操作的时候，比如修改账号密码，转账等，没有校验表单token或http请求头中的referer，从而导致攻击者利用普通用户的身份完成攻击**



**你怎么知道他的后台地址或转账地址呢?**

**进行指纹识别cms或搭建框架进行复现，测试，再伪造**







# CSRF高危触发点

- 用户论坛
- 用户中心
- 反馈留言
- 交易管理
- 后台管理

------



# 漏洞复现

抓包

![image.png](https://cdn.nlark.com/yuque/0/2021/png/2476579/1628741370866-1f9de601-07d6-430a-a7a8-a9e93c13faef.png)



看到的是get提交的数据包`/vul/csrf/csrfget/csrf_get_edit.php?sex=gg&phonenum=1111&add=11111&email=111111&submit=submit`也就是说可以这样直接伪造提交的数据包从而就该服务器内容。





伪造服务器点击`<script src="http://10.1.1.7/vul/csrf/csrfget/csrf_get_edit.php?sex=33&phonenum=3333333&add=333333&email=33333333&submit=submit"></script>`



![在这里插入图片描述](https://img-blog.csdnimg.cn/bcb38822733f45c9b6045275d3525f47.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAd3lkMjAwMQ==,size_20,color_FFFFFF,t_70,g_se,x_16)

**抓取修改信息的get请求包**

通过抓包可以任意修改姓名性别，账号密码等，复制上面该链接诱导用户点击，即可通过用户A的cookie信息修改用户A本人的账户密码





# Burp快速生成poc

攻击者可以使用burp自带的功能快速生成

抓取存在CSRF漏洞的数据包，右键，Engagement tools——>Generate CSRF PoC





![图片](https://mmbiz.qpic.cn/mmbiz_png/rSyd2cclv2et9NHxRhN8exP4Ly6FKH9SGFoqCV6KbCDvbI44gicjY8kM3NYpJiaG5kiaMbzI7SYlS2rsicGGK5EUqA/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

会自动生成CSRF的攻击代码，点击Copy HTML

然后保存到我们本地，后缀为.html。双击打开该html文件，点击Submit request。只要当受害者点击了Submit request按钮，我们的CSRF攻击代码就执行了。

![图片](https://mmbiz.qpic.cn/mmbiz_png/rSyd2cclv2et9NHxRhN8exP4Ly6FKH9SFDTcWcNlIQubNkYGE9jibHekrgTq5oSOs45cdCQ7QpCoXM8jh51AmkA/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)







# CSRF危害

**可以利用伪造用户进行转账，发帖子，网购，修改密码等**

**还可以和xss漏洞结合使用**

**伪造http请求进行未授权操作，纂改，盗取网站上重要用户数据等**







# CSRF防护

**设置随机Token**

**校验referer**

**设置验证码**

**限制方式请求为post**









