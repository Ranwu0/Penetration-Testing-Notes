# PHP反序列化漏洞

**序列化与反序列化：通俗点就越是讲对象转换成字符串**

![img](https://cdn.nlark.com/yuque/0/2021/png/2476579/1629513452361-1d1b230d-7fad-4387-ae68-76a3da18a08c.png?x-oss-process=image%2Fresize%2Cw_750%2Climit_0)

![img](https://cdn.nlark.com/yuque/0/2021/png/2476579/1629513462778-724f3c0d-a1f6-412c-8cfa-4f4cac6d4586.png?x-oss-process=image%2Fresize%2Cw_750%2Climit_0)

# 原理

**未对用户输入的序列化字符串进行检测，导致攻击者可以控制反序列化过程，从而导致代码执行，SQL注入，目录遍历等不可控后果**

**在反序列化的过程中自动触发了某些魔术方法**



- **serialize()**         //将一个对象转换成一个字符串
- **unserialize()**      //将字符串转换成对象



![在这里插入图片描述](https://img-blog.csdnimg.cn/20200826194508389.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQxNjc5MzU4,size_16,color_FFFFFF,t_70#pic_center)



# 触发条件

**unserialize函数的变量可控，php文件中存在可利用的类，类中有魔术方法**







# PHP魔术方法

```
__construct()    #类的构造函数
__destruct()    #类的析构函数,在对象被销毁时执行该函数
__call()    #在对象中调用一个不可访问方法时调用
__callStatic()    #用静态方式中调用一个不可访问方法时调用
__get()    #获得一个类的成员变量时调用
__set()    #设置一个类的成员变量时调用
__isset()    #当对不可访问属性调用isset()或empty()时调用
__unset()    #当对不可访问属性调用unset()时被调用。
__sleep()    #执行serialize()时，先会调用这个函数
__wakeup()    #执行unserialize()时，先会调用这个函数
__toString()    #类被当成字符串时的回应方法
__invoke()    #调用函数的方式调用一个对象时的回应方法
__set_state()    #调用var_export()导出类时，此静态方法会被调用。
__clone()    #当对象复制完成时调用
__autoload()    #尝试加载未定义的类
__debugInfo()    #打印所需调试信息
```

[魔术方法传送门](https://www.cnblogs.com/20175211lyz/p/11403397.html)





# 无类测试

- serialize



![image.png](https://cdn.nlark.com/yuque/0/2021/png/2476579/1629702598464-5fc46fbc-165e-4fe4-94ac-903265291b3f.png)



- unserialize



![image.png](https://cdn.nlark.com/yuque/0/2021/png/2476579/1629702950773-dd685602-5640-4632-9b1b-ad2eb8b95701.png)



