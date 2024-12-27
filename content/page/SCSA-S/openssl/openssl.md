---
title: "openssl"
date: 2024-12-19T21:25:05+08:00
draft: false
---

这里只收录openssl中关于密码学的内容

#### rc4加解密
```shell
openssl enc -e -rc4 -in test.txt -out test_rc4.enc
```
加密
```shell
openssl enc -d -rc4 -in test_rc4.enc -out test_rc4.dm
```
解密

#### AES加解密
```shell
openssl enc -e -aes-128-cbc -a -salt -in test.txt -out test_aes128.enc
```
加密
```shell
openssl enc -d -aes-128-cbc -a -salt -in test_aes128.enc -out test_aes128.d
```
解密

#### 3DES加解密
```shell
openssl enc -e -des3 -a -salt -in test.txt -out test_des3.enc
```
加密
```shell
openssl enc -d -des3 -a -salt -in test_des3.enc -out test_des3.d
```
解密

#### RSA加解密
```shell
openssl genrsa -out rsa.key 1024
```
生成密钥对
```shell
openssl rsa -in rsa.key -pubout -out rsa_pub.key
```
导出公钥
```shell
openssl rsautl -encrypt -in test.txt -inkey rsa_pub.key -pubin -out test_rsa.enc
```
使用公钥加密文件
```shell
openssl rsautl -decrypt -in test_rsa.enc -inkey rsa.key -out test_rsa.c
```
使用私钥解密文件

#### md5
```shell
openssl dgst -md5 md.txt
```
生成哈希

#### RSA签名
```shell
openssl genrsa -out rsa.key 1024
```
生成RSA密钥对
```shell
openssl rsa -in rsa.key -pubout -out rsa_pub.key
```
导出公钥
```shell
openssl sha1 -sign rsa.key -out rsasign.bin test.txt
```
使用私钥签名
```shell
openssl sha1 -verify rsa_pub.key -signature rsasign.bin test.txt
```
RSA公钥认证签名

#### DSA签名
```shell
openssl dsaparam -out dsap.pem 1024
```
生成DSA参数
```shell
openssl dsaparam -in dsap.pem -text -noout
```
查看DSA明文
```shell
openssl gendsa -out dsa1.pem dsap.pem
```
生成DSA密钥
```shell
openssl gendsa -out dsaprivatekey.pem -des3 dsap.pem
```
生成私钥并使用DES加密
```shell
openssl dsa -in dsaprivatekey.pem -pubout -out dsapublickey.pem
```
由私钥生成公钥
```shell
openssl dgst -dss1 -sign dsaprivatekey.pem -out dsasign.bin test.txt
```
使用私钥dsaprivatekey.pem签名text.txt，dsasign.bin为签名文件
```shell
openssl dgst -dss1 -verify dsapublickey.pem -signature dsasign.bin test.txt
```
使用公钥验证签名
```shell
openssl dgst -dss1 -prverify dsaprivatekey.pem -signature dsasign.bin test.txt
```
使用私钥验证签名