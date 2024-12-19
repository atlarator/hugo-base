---
title: "Metasploit"
date: 2024-12-19T21:25:05+08:00
draft: false
---

### Metasploit
```bash
msfdb init && msfconsole
```
启动MSF
```bash
search [vul_name]
use [module_name]
show options
set RHOSTS [IP]
run
```
检测靶机是否存在漏洞
```bash
use [module_name]
set RHOST [dest_IP]
set LHOST [src_IP]
set payload [PAYLOAD_NAME]
set RPORT 445
set target 0 
run    //执行
```
进行漏洞利用