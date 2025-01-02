---
title: "文件包含相关"
date: 2024-12-20T20:45:05+08:00
draft: false
tags: ["SECURITY"]
---

### 文件名后缀检测与绕过
#### 一般思路
.php -> 更换大小写 -> Windows可使用末尾增加.或者空格或者::$DATA进行过滤绕过 -> Apache可使用``SetHandler application/x-httpd-php``进行任意文件绕过 -> MIME绕过 -> 图片马

在GET中可将.php改为.php%00，若为POST请求则可将jieduan字段更改为.%2fupload%2finfo.php%00

#### 图片马
```
JPG：FF D8 FF E0 00 10 4A 46 49 46
GIF：47 49 46 38 39 61(GIF89a)
PNG：89 50 4E 47
```
或者在图片最后直接插入马

使用正常的图片和木马合成图片马
```
copy 1.jpg/b+1.php/a info.jpg
```
#### 条件竞争
```php
<?php fputs(fopen('../shell.php','w'),'<?php phpinfo();?>')?>
```
进入info.php，要快！再进入shell.php

#### IIS6.0
    IIS除了可以解析.asp后缀的脚本以外，还可以解析.cer和.asa后缀的文件
    特殊符号“/”，任意文件夹名.asp目录下的任何文件都会被IIS当作asp脚本执行
    特殊符号“;”，任意文件名.asp;.jpg，后缀是.jpg，可以绕过限制，但是IIS6.0的特殊符号“;”会将该文件当作asp脚本执行

#### 暴力破解

前台验证码不用管，后台验证码检查是否长期有效