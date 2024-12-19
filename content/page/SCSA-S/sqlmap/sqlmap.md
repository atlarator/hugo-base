---
title: "sqlmap"
date: 2024-12-19T21:25:05+08:00
draft: false
---

```bash
python sqlmap.py -h
```
查看帮助
```bash
python sqlmap.py -u "URL"
```
寻找注入点
```bash
python sqlmap.py -u "URL" --dbs
```
获取数据库库名
```bash
python sqlmap.py -u "URL" --current-db
```
获取当前数据库库名
```bash
python sqlmap.py -u "URL" -D security --tables
```
获取security库下的所有表名
```bash
python sqlmap.py -u "URL" -D security -T users --columns
```
获取字段名
```bash
python sqlmap.py -u "URL" -D security -T users -C id,username,password --dump
```
获取对应字段的全部值

特殊地，使用burpsuite截获的数据包时，参数改为
```bash
python sqlmap.py -r [path_to_the_pack]
```