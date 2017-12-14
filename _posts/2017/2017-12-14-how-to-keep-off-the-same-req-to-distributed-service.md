---
layout: post
title: 在分布式服务中，如何防止同一个请求操作
categories:
- Technology
tags:
- 分布式
---

# 问题

无状态分布式服务，通过LB进行负载均衡，但是，有一种场景会引起同意请求的重复提交。

