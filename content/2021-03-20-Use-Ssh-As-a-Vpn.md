+++
title = "使用ssh服务和公网服务器搭建VPN"
draft = true


[taxonomies]
tags = ["ssh"]
categories = [""]
+++

## 问题背景

通常公司的内部服务入gitlab、confluence等无法直接通过外网直接访问的。一般情况下，可以通过向公司申请VPN服务来解决。
另外一个方式就是使用SSH连接，将SSH连接转化为VPN来作为备用。

## 必要条件

- 内网服务器：可以使用SSH连接来连接外网服务器
- 外网服务器：具有公网IP，可以被SSH访问。这里假设ip为 x.x.x.x，ssh访问端口为22

## 具体操作

1. 内网服务器：
```shell
// 生成 ssh 密钥
$ ssh-keygen

// 为外网机器添加内网SSH公钥
$ ssh-copy-id -i ~/.ssh/id_rsa.pub -p 22 root@x.x.x.x

// 需要提前安装autossh
autossh -M 55556 -v -CNR x.x.x.x:55555:127.0.0.1:22 root@x.x.x.x
```

2. 外网服务器：

```shell
// 需要提前安装 sshuttle
sshuttle -r root@127.0.0.1:55555 127.0.0.1/0
```
意思是建立55555端口的VPN连接，并且转换为VPN。
这个时候，也可以通过连接外网机器的55555端口访问机器
