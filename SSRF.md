# SSRF漏洞(服务器端请求伪造)

# SSRF原理

**服务器端请求伪造，利用一个可以发起网络请求的服务，可以用外网来攻击内网里的客户端，也可以当作跳板攻击其他服务**



![img](https://www.t00ls.cc/attachments/month_1707/1707150408d39c5b2dcf5e1a20.jpg)





# 漏洞利用手段

可以对外网，内网，本地进行端口扫描

攻击运行在内网或本地的漏洞程序

攻击内网或外网的web应用

使用file协议读取本地文件

http://www.xingkonglangzi.com/ssrf.php?url=192.168.1.10:3306   // 探测3306端口是否开启 有回显代表开启

http://www.xingkonglangzi.com/ssrf.php?url=file:///c:/windows/win.ini   //读取本地文件





# 漏洞出现点

- 分享页：通过url地址分享网页内容
- 转码服务
- 在线翻译
- 图片加载与下载
- 图片，文章收藏功能

**从URL关键字中寻找**



![img](https://img2018.cnblogs.com/i-beta/1646039/201911/1646039-20191106063648464-50325334.png)





# SSRF绕过方法

1. @										http://abc.com@127.0.0.1       //利用@符号实现绕过
2. 添加端口号　　　　　　http://127.0.0.1:8080
3. 短地址　　　　　　　　https://0x9.me/cuGfD
4. 可以指向任意ip的域名　          xip.io
5. ip地址转换成进制来访问            192.168.0.1=3232235521（十进制）
6. 非HTTP协议
7. DNS Rebinding



# 暴力破解探测内网端口

**burp抓包**

**发送到intrude，设置需要爆破的变量**

![image-20220322210207424](C:\Users\雷神\AppData\Roaming\Typora\typora-user-images\image-20220322210207424.png)





**设置字典：**

**Number: From:1 - To:65535 - Step:1**

**数字类型（Number）——这种类型的Payload是指根据配置，生成一系列的数字作为Payload**

**Type表示使用序列还是随机数**

**From表示从什么数字开始**

**To表示到什么数字截止**

**Step表示步长是多少**







![image-20220322210425191](C:\Users\雷神\AppData\Roaming\Typora\typora-user-images\image-20220322210425191.png)



**爆破完毕结果**



![在这里插入图片描述](https://img-blog.csdnimg.cn/20210110221711863.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MzA0OTE4,size_16,color_FFFFFF,t_70)

**扫描/探测后，开放了：80、8080、912、902、3306端口**

**80：HTTP**

**8080：代理服务器端口(因为打开了BurpSuite)**

**912：虚拟机监听端口**

**902：Vmware使用的端口**

**3306：Mysql服务默认端口**





# SSRF修复建议

- 限制请求端口只能为web端口，只允许http和https请求
- 限制不能访问内网的IP，以防对内网进行攻击
- 隐蔽返回的详细信息