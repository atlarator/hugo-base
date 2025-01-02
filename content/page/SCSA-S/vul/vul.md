---
title: "漏洞扫描"
date: 2024-12-28T07:53:00+08:00
draft: false
tags: ["SECURITY"]
---

## Nikto
```shell
nikto -host http://[IP] -c /sqli-labs -o /usr/result.html -F htm
```
对靶机的sqli-labs进行扫描，并将结果保存至result.html中  
```shell
nikto -host http://[IP] -mutate 6 -mutate-option /usr/sub.txt
```
根据sub.txt字典中的子域名选项进行子域名扫描  

## owasp-zap
进入后点击no，设置代理于8091端口，打开FoxyProxy代理  
进入靶机，进入后开始抓包  
也可以选择quick start，输入URL并点击attack，对产生的报警信息可以双击进入进行查看