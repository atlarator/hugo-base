---
title: "信息搜集"
date: 2024-12-21T08:33:05+08:00
draft: false
---

### 信息查询

#### 子域名查询
域名反查工具：<a href="https://tool.chinaz.com/">站长之家</a>
域名查询指令
```shell
whois [URL]
```
DNS解析指令
```shell
dig [URL]
```
查找负责解析的DNS主机
```shell
dig [URL] @m.n.p.q
```
查找NS记录
```shell
dig [URL] NS
```
查找TXT记录
```shell
dig [URL] TXT
```
多地ping
<a href="http://ping.chinaz.com">查看域名真实IP</a>

#### 子域名爆破

##### subDomainsBrute
```shell
python3 subDomainsBrute.py --full -t 10 [URL] -w
```
其中--full为全部扫描，-t为使用线程数，-w为扩展扫描

搜索引擎中使用site:[URL]即可搜索子域名，使用-www去掉以www开头的结果

##### theHarvester
theHarvester: -d指定域名，-g指定google搜索，-s使用shodan搜索，-b为指定搜索引擎或使用全部
```shell
theHarvester -d [URL] -c
```
-c进行子域名爆破
##### Aquatone
-d,指定目标域名，--nameservers指定使用的名字服务器，-t指定查询线程数

--disable-collectors+搜索引擎名可以将对应的搜集器排除

扩展查询
```shell
aquatone-discover --set-key [engine-name] [key]
```
减少扫描目标
```shell
aquatone-scan -d [URL] -p [PORTS] -t [THREADS] -s [SLEEP_TIME]
```
-d指定域名，-p指定端口，使用逗号隔开，-t指定使用线程数目，-s指定发送请求的间隔时间

#### C段扫描
```shell
masscan –p 80 ip/24 --rate 10000 -oL output.txt
```
-p为设定输出端口，--rate为设定发包速率，-oL为设定输出路径

#### 资产服务指纹查询
```shell
wafw00f [IP]
```
使用waf00f探测waf设备

#### 目录扫描

```shell
dirb http://IP [dict_path]
```
使用dirb，借助字典暴力探测web目录

#### 域传输漏洞
```shell
dig [DNS_server] -t axfr [request_URL]
```

-t 为域传输类型请求