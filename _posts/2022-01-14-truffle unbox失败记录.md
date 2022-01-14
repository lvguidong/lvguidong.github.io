---
layout: post
title: truffle unbox失败记录
tags: 
- eth
- truffle
date: 2022-01-14 20:12 +0800
---

`truffle` 使用unbox时失败， 在windows和linux上均有这个问题，记录如下：

```shell
dong@DESKTOP-KS722GA:/mnt/g/dong/coin/eth/truffle$ truffle unbox webpack

Starting unbox...
=================

✔ Preparing to download box
✖ Downloading
Unbox failed!
✖ Downloading
Unbox failed!
Error: connect ECONNREFUSED 0.0.0.0:443
    at TCPConnectWrap.afterConnect [as oncomplete] (net.js:1159:16)
Truffle v5.4.28 (core: 5.4.28)
Node v14.18.3

```

解决办法：
linux下修改`hosts`文件,路径： `/etc/hosts`

windows下修改`hosts`文件，路径： `C:\Windows\System32\drivers\etc\hosts`

添加如下：
```shell
185.199.108.133 raw.githubusercontent.com
```
`raw.githubusercontent.com` 具体的ip地址通过 [www.ipaddress.com/](https://www.ipaddress.com/) 查询




