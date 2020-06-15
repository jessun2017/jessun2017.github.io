---
title: "五大常见MySQL高可用"
date: "2020-06-08"
draft: true
---

## 第一种：MySQL主从半同步复制

使用双节点数据库，搭建单向或双向的半同步复制。在 5.7 以后的版本中，由于
loseless, replication, logical 多线程复制等新特性，使得 MySQL 原生半同步复制更加
可靠。

常见架构：

@startuml
left to right direction

node VIP
node HaProxy
database master
database slave

VIP --> HaProxy
HaProxy --> master
master <--> slave: 单/双向复制

@enduml

@startuml
left to right direction

node VIP

node HaProxy1 {
    database master
}

node HaProxy2 {
    database slave
}

VIP --> master
master <--> slave: 单/双向复制


@enduml

## 第二种：MySQL主主半同步复制
