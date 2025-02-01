---
title: 转移中的故事
description: 那些逆天的博客框架
date: '2024-12-11'
aliases:
  - about-us
  - about-hugo
  - contact
license: CC BY-NC-ND
lastmod: '2024-12-11'
menu:
    main: 
        weight: -90
        params:
            icon: user
---

## 2024.12.11

确切来讲，这个博客的转移拖得太久太久了。最开始我用的Hexo框架弄，结果Hexo在20年之后的几乎所有issue都在痛骂这个框架，帮助信息也是没几个正经的。我年中弄这个框架的时候就受不了两点，一是你一个博客框架在我电脑开性能模式下还敢狂转风扇你也是没谁了，二是这玩意儿的定制化程度低得可怕。我想把Hexo主页最上方的地球图片换了，结果前脚改完配置文件后脚一生成发布版就给我改回去了。牛的。

大概半年后就是今年十二月，我尝试了Hugo，怎么说呢，槽点还是很多。主要集中在你在Ubuntu上apt装的Hugo出了一堆在官网issue上头都找不到的离奇bug（点名批评server后白屏），最后把Hugo卸了，去snap上头安装一个，问题居然解决了。哈，你甚至没法界定到底是Ubuntu的锅还是Hugo的锅。

Hugo另外一个和Hexo逻辑不太一样的地方在于Hugo的自动化部署比较麻烦，需要借助脚本还得分两个repo存放。因为Hexo是不区分public和dev的而Hugo在每次发布的时候都会生成一个新的public。

有一篇关于Turing Complete的指令集速查表在这次博客迁移的过程中丢失了，剩下的文章都从我的大机械盘中找了回来。总之，盖新家是真折腾人啊，但愿Hugo别突然爆发什么奇怪的Breaking Change，能让我的博客活久一点儿。如果Hugo爆发Breaking Change，大概率我就只能转战W3C了（笑）

接下来就是找时间部署在Cloudflare上头然后尝试着把评论和Tag加上。

## 2024.12.15

ok，逆天，事实证明坑只会多不会少。当你发现你的工作流寄了的时候你的第一反应是什么？看log。然而log的醒目之处只是告诉了你github要把服务器换成ubuntu-24.04 LTS，尝试将工作流中的ubuntu-latest更改为ubuntu-24.04 LTS，无果，仍然失败，后来我猜测是不是DNS签名的问题，删除CNAME，无果。最后，发现居然是hugo推送的latest在snap上头下载不到，你得在工作流中把hugo的版本换了才能部署。又是一个不知道是该骂ubuntu还是该骂hugo的一天啊......

## 2024.12.22

域名转移居然要注册满60天，啊？！？

## 2025.1.17

记一次WSL-Git故障修复  

从2025年1月16日下午开始机器莫名其妙无法进行push与clone，尝试查找原因，关闭代理再开启，无效，重启wsl，无效，重启本机，无效  
在本机安装的其他Linux Distro上尝试进行push，无效，切换为powershell进行push，无效，下载git bash进行push，无效  
推测可能是网络镜像问题，在用户目录下建立.wslconfig文件输入如下内容：
```toml
[wsl2]
memory=32GB
processors=10
swap=8GB
debugConsole=true
networkingMode=mirrored
```
无效。  
在Google上搜索"WSL git slow"，出现的结果都是让你从WSL1升级至WSL2，无效  
Google搜索443error，出现的结果为：
```shell
git config --global http.proxy 127.0.0.1:8087
```
无效。  
```shell
git config --global --unset http.proxy
```
第二个回答，仍然无效  
将代理转为高可用，无效；将代理从海外代理改为全局代理，无效且ping不到google  
转战国内网站，最高点击的一个回答中有如下显示：
```shell
sudo rm /etc/resolv.conf
sudo bash -c 'echo "nameserver 8.8.8.8" > /etc/resolv.conf'
sudo bash -c 'echo "[network]" > /etc/wsl.conf'
sudo bash -c 'echo "generateResolvConf = false" >> /etc/wsl.conf'
```
无效  
再查证后，使用：
```shell
git config --global --unset http.proxy
git config --global --unset https.proxy
```
push成功！！！  

## 2025.1.17(2)

<a href="https://askubuntu.com/questions/1192347/temporary-failure-in-name-resolution-on-wsl/1401317#1401317?newreg=fc95a6947d13420fb5f39420bf285388">关于github主机无法解析的解决方法</a>  

## 2025.2.1

部署了一个Notion页面，够无脑，但是有太多的功能是付费才能使用的  

总之，很高兴认识你，各种意义上的。愿你在这儿找到你想要的东西！