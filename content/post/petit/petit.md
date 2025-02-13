---
title: "ssh与网络杂项"
date: 2025-02-13T17:11:02+08:00
draft: false
tags: ["MISC"]
---

## pwn.college
先使用``ssh-keygen``进行key的建立：
```shell
ssh-keygen -f key -N ''
```
然后将公钥复制到pwn.college中
想要链接dojo时使用：
```shell
ssh -i key hacker@dojo.pwn.college
```

## 从一次git push失败说起

2月13日，我的git莫名其妙push不到远端了，尝试排查问题，先使用了``ping``，没发现任何问题，后使用``ssh -T git@github.com``，不行，尝试重启``wsl --shutdown``，不行，尝试查看GitHub是否今天有服务中断，进入 https://www.githubstatus.com/ 进行查看，一切正常，尝试unset掉proxy，未解决，进行nslookup，未解决，查看代理端口发现代理端口从1085更改至1087，重新设置``git config``，解决  