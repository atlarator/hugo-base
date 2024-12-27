---
title: "密码学相关工具"
date: 2024-12-26T21:33:00+08:00
draft: false
---

## Hashcat

-a 0 字典破解; -a 1 组合破解; -a 3 掩码暴力破解;   
-a 6 字典+掩码破解; -a 7 掩码+字典破解  
-m 规定hash类型  
掩码字符集：  
?l 小写字母; ?u 大写字母; ?d 十进制数字; ?h小写十六进制数字;   
?H大写十六进制数字; ?s 特殊字符; ?a 键盘上的可见字符; ?b 0x00-0xff  

## hydra
```shell
hydra -l li -P /usr/password.txt ftp://[IP]  
```
对靶机FTP登录用户名li进行密码字典攻击  

## crunch
(TODO)