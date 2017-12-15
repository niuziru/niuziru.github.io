---
layout: post
title: 在分布式服务中，如何防止同一个请求操作
categories:
- Technology
tags:
- 分布式
---

# 问题

无状态分布式服务，通过LB进行负载均衡，但是，有一种场景会引起同一请求的重复提交。

<img src="/images/distributed-service.png" alt="分布式系统" width="100%" height="100%" />


# 分析

## 数据库

    发起请求--> 收到请求
            --> 锁定数据库
            --> 查询本次请求ID是否已经写入
              --> 如果ID已经存在，返回EXIST，终止本次请求
              --> 如果ID不存在，执行写入操作
            --> 解锁
            --> 其他流程

## 配置中心


