# Tomcat7+弱密码&&后端Getshell漏洞

# 漏洞描述

tomcat默认账号密码;tomcat/tomcat

**Tomcat 支持通过后端部署war 文件，所以我们可以直接将webshell 放到web 目录中。为了访问后端，需要权限**

```
Tomcat7+的权限如下：
经理（后台管理）
manager-gui（html页面权限）
manager-status（查看状态的权限）
manager-script（文本界面权限和状态权限）
manager-jmx（jmx 权限和状态权限）
主机管理器（虚拟主机管理）
admin-gui（html 页面权限）
admin-script（文本界面权限）
```

**可以看出，用户tomcat拥有上述所有权限，密码为tomcat。**
**正常安装Tomcat8默认没有用户，管理页面只允许本地IP访问。只有管理员手动修改了这些属性，我们才能进行攻击。修改后则需要爆破才能登录。**

**【漏洞利用】：通过默认的弱密码，可后台部署WAR包，上传shell。**

**用户权限在conf/tomcat-users.xml文件中配置：**

```
<?xml version="1.0" encoding="UTF-8"?>
<tomcat-users xmlns="http://tomcat.apache.org/xml"
              xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
              xsi:schemaLocation="http://tomcat.apache.org/xml tomcat-users.xsd"
              version="1.0">

    <role rolename="manager-gui"/>
    <role rolename="manager-script"/>
    <role rolename="manager-jmx"/>
    <role rolename="manager-status"/>
    <role rolename="admin-gui"/>
    <role rolename="admin-script"/>
    <user username="tomcat" password="tomcat" roles="manager-gui,manager-script,manager-jmx,manager-status,admin-gui,admin-script" />

</tomcat-users>
```



# 漏洞复现

访问http://xxx/manager/html，打开Tomcat后台管理页面。

默认账密：tomcat/tomcat

![在这里插入图片描述](https://img-blog.csdnimg.cn/8648cbf47e9a4900b54c475138224e25.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5Lmm5bGx5LiK55qE5LqR,size_20,color_FFFFFF,t_70,g_se,x_16)

通过弱密码tomcat:tomcat登录进后台页面。http://192.200.30.72:8080/manager/html

![在这里插入图片描述](https://img-blog.csdnimg.cn/60efe3b0e57b48d08d3de2a5114b4d40.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5Lmm5bGx5LiK55qE5LqR,size_20,color_FFFFFF,t_70,g_se,x_16)

**.将一个冰蝎jsp马添加到压缩包中，更改压缩后缀为.war，完成后上传。**

![在这里插入图片描述](https://img-blog.csdnimg.cn/4a67185ef0994c3f918ee47cccd13b60.png)

![在这里插入图片描述](https://img-blog.csdnimg.cn/ed23e1bf3ea443aea56137b1f94bc36d.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5Lmm5bGx5LiK55qE5LqR,size_18,color_FFFFFF,t_70,g_se,x_16)

文件上传成功。

![在这里插入图片描述](https://img-blog.csdnimg.cn/97d291cb41624592b1bb6ceb1e090e6e.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5Lmm5bGx5LiK55qE5LqR,size_20,color_FFFFFF,t_70,g_se,x_16)

访问shell.jsp,访问成功。

![在这里插入图片描述](https://img-blog.csdnimg.cn/45e27278892448fdbf122479eb19ea59.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5Lmm5bGx5LiK55qE5LqR,size_20,color_FFFFFF,t_70,g_se,x_16)

.通过冰蝎连接成功，命令执行成功

![在这里插入图片描述](https://img-blog.csdnimg.cn/4348d7c50d814cf49bedc6fa8d4adac4.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5Lmm5bGx5LiK55qE5LqR,size_20,color_FFFFFF,t_70,g_se,x_16)