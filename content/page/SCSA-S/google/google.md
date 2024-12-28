---
title: "Google Hacking"
date: 2024-12-28T08:25:40+08:00
draft: false
---

## Google

\+ 强制搜索后方的单词  
\- 忽略关键词  
~ 同义词  
. 单一通配符  
\* 通配符，可以代表多个字符  
"" 精确查询  
| OR 符合一个就显示  
Intext:key 搜索网页上某个关键字的页面  
Allintext:key 可以键入多个关键字  
intitle:key 搜索网页标题中是否有关键字  
Allintitle:key 同理  
cache:url 查看URL快照  
filetype: 搜索指定类型文件  
info: 相关信息  
inurl: 搜索结果是否在URL中，可与info结合进行后台查询  
site: 搜索域名及其子域名  
related URL: 与这个URL相关的页面  

## Shodan

hostname:"URL" 主机名  
port:[port_id] 端口号  
net:192.168.1.0/24 CIDR格式的IP地址  
city:"Beijing" 城市  
product:"Apache httpd" 产品类型  
version:"2.6.1" 版本号  
os:"Windows7" 操作系统  
asn:"AS9500" 自治系统号  
country:"CN" 国家简写  
org:"Google" 组织  
isp:"China Telecom" ISP服务商  
geo:"36.4281,106.5535" 经纬度  