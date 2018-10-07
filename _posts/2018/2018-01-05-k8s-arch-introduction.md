---
layout: post
title: Kubernetes架构简述
categories:
- Technology
tags:
- K8S
---

# 概述

## 基础架构

下图是k8s(kubernetes, k与s之间恰好由8个字符组成,故社区简称k8s)的架构图,它由:

|角色  |组件  |备注  |
|:---|:---|:---|
|master|||
||kube-apiserver||
||kube-scheduler||
||kube-controller-manager||
||etcd||
|Slave|||
||kubelet||
||kube-proxy||
||||


<img src="/images/kubernetes-arch.png" alt="Kubernetes架构图" width="100%" height="100%" />

## 集群架构

无论k8s的master组件是容器化/非容器化的，在集群架构下，均是采用etcd的集群模式实现高可用和选主的，其组件基本如下：

|角色  |组件  |备注  |
|:---|:---|:---|
|master|||
||kube-apiserver||
||kube-scheduler||
||kube-controller-manager||
||etcd||
||LoadBalancer|比如: nginx/haproxy等|
||[keepalived](http://blog.51cto.com/zephiruswt/1235852)||
|Slave|||
||kubelet||
||kube-proxy||
||||

### 1、容器化模式


<img src="/images/kubernetes-arch-ha.svg" alt="Kubernetes容器化架构图" width="100%" height="100%" />


### 2、非容器化模式

<img src="/images/kubernetes-arch-ha2.png" alt="Kubernetes非容器化架构图" width="100%" height="100%" />

### 3、生产环境模式

在下图中可以看出，管理节点共有5个服务器组成，其中：

* k8s master以双实例模式部署在2台服务器上
* etcd以3节点集群模式部署在3台服务器上

<img src="/images/kubernetes-arch-ha3.jpeg" alt="Kubernetes生产环境架构图" width="100%" height="100%" />

注意：

其实在某些小型化的生产环境中，可以让k8s master组件和etcd进行资源合社，这样就可以从5台服务器减少到2台。

# 组件介绍

## kube-apiserver

## kube-scheduler

## kube-controller-manager

## kubelet

## kube-proxy

## etcd


## LoadBalancer

LoadBalancer 可以将来自客户端的请求分发到不同的服务器，通过将一系列的请求转发到不同的服务器可以提高服务器的性能，并可以自动地寻找最优的服务器转发请求，这样不仅提高了系统性能，同时达到了负载均衡的目的，满足了用户需求。

因此 LoadBalancer 在应用场景中一般处于 web 服务器的前端，用来均衡发到 web 服务器的请求量，均衡负载，提高系统性能。

LoadBalancer 可以连接多个 web server，从而将多个 web server 组成一个集群（cluster），集群中负载的分配通过 loadBalancer 进行控制和管理。

当然为了更好地进行负载均衡，LoadBalancer 也可以构建为一个集群，使用 LoadBalancer 构建的 web server cluster。

<img src="/images/Elasticsearch_Cluster_August_2014.png" alt="Wikipedia用户请求的LB架构图" width="100%" height="100%" />


## Keepalived

Keepalived是一款高可用软件，它的功能主要包括两方面：

* 1）通过IP漂移，实现服务的高可用：服务器集群共享一个虚拟IP，同一时间只有一个服务器占有虚拟IP并对外提供服务，若该服务器不可用，则虚拟IP漂移至另一台服务器并对外提供服务；
* 2）对LVS应用服务层的应用服务器集群进行状态监控：若应用服务器不可用，则keepalived将其从集群中摘除，若应用服务器恢复，则keepalived将其重新加入集群中。

<img src="/images/keep-alived.png" alt="keepalived工作图" width="100%" height="100%" />

在kubernetes集群模式中，我们主要用它的`IP漂移`功能。


# 参考

* [kubernetes handbook 集群部署](https://kubernetes.feisky.xyz/deploy/cluster.html)
* [Kubernetes集群高可用方案](http://geek.csdn.net/news/detail/65555)
* [在阿里云上部署生产级别Kubernetes集群](https://yq.aliyun.com/articles/71037)