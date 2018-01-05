---
layout: post
title: Kubernetes服务发布分析
categories:
- Technology
tags:
- K8S
---

# Kubernetes架构

## 概述
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


如果用户迁移应用到k8s平台,则需要了解应用间的互相访问问题.

## k8s资源对象简介

在引入k8s服务发布之前,先了解下它特有的几个资源对象:

|资源对象  |功能简介  |备注  |
|:---|:---|:---|
|pod|Pod是一组紧密关联的容器集合，它们共享IPC、Network和UTS namespace，是Kubernetes调度的基本单位。|一组相关连的容器的集合|
|rc/rs|ReplicationController（也简称为rc）用来确保容器应用的副本数始终保持在用户定义的副本数，即如果有容器异常退出，会自动创建新的Pod来替代；而异常多出来的容器也会自动回收。ReplicationController的典型应用场景包括确保健康Pod的数量、弹性伸缩、滚动升级以及应用多版本发布跟踪等。<br>在新版本的Kubernetes中建议使用ReplicaSet（也简称为rs）来取代ReplicationController。ReplicaSet跟ReplicationController没有本质的不同，只是名字不一样，并且ReplicaSet支持集合式的selector（ReplicationController仅支持等式）。|rs一般和deployment配合使用|
|deployment|Deployment为Pod和ReplicaSet提供了一个声明式定义(declarative)方法，用来替代以前的ReplicationController来方便的管理应用。||
|statefulset|StatefulSet是为了解决有状态服务的问题（对应Deployments和ReplicaSets是为无状态服务而设计），其应用场景包括：<br>  稳定的持久化存储<br>  稳定的网络标志<br> 有序部署，有序扩展，即Pod是有顺序的，在部署或者扩展的时候要依据定义的顺序依次依序进行（即从0到N-1，在下一个Pod运行之前所有之前的Pod必须都是Running和Ready状态）<br>  有序收缩，有序删除（即从N-1到0）||
|daemonset|DaemonSet保证在每个Node上都运行一个容器副本，常用来部署一些集群的日志、监控或者其他系统管理应用。典型的应用包括：<br>  日志收集<br>  系统监控<br>  系统程序||
|service|Service(简称svc)是对一组提供相同功能的Pods的抽象，并为它们提供一个统一的入口。||
|ingress|Ingress可以给service提供集群外部访问的URL、负载均衡、SSL终止、HTTP路由等。为了配置这些Ingress规则，集群管理员需要部署一个Ingress controller，它监听Ingress和service的变化，并根据规则配置负载均衡并提供访问入口。||
||||

引用[十分钟带你理解Kubernetes核心概念](http://www.dockone.io/article/932)中的几张图更清晰的了解k8s的核心概念.

### 1、k8s cluster

下图可知k8s的集群是由master和node组成.

顾名思义,master是管理面,用户应用的编排、调度、管理(生命周期管理、扩缩容管理等)等；node是数据面，运行用户应用的计算资源。

<img src="/images/k8s-cluster.png" alt="Kubernetes集群图" width="100%" height="100%" />

### 2、pod

Pod是一组紧密关联的容器集合，它们共享IPC、Network和UTS namespace，是Kubernetes调度的基本单位。详细可以阅读[kubernetes handbook pod](https://kubernetes.feisky.xyz/concepts/pod.html)

<img src="/images/k8s-pod.png" alt="Kubernetes集群图" width="100%" height="100%" />


### 3、rc/rs

ReplicationController（也简称为rc）用来确保容器应用的副本数始终保持在用户定义的副本数，即如果有容器异常退出，会自动创建新的Pod来替代；而异常多出来的容器也会自动回收。ReplicationController的典型应用场景包括确保健康Pod的数量、弹性伸缩、滚动升级以及应用多版本发布跟踪等。

在新版本的Kubernetes中建议使用ReplicaSet（也简称为rs）来取代ReplicationController。ReplicaSet跟ReplicationController没有本质的不同，只是名字不一样，并且ReplicaSet支持集合式的selector（ReplicationController仅支持等式）。

详细可以阅读[kubernetes handbook replicaset](https://kubernetes.feisky.xyz/concepts/replicaset.html)

<img src="/images/kubernetes-rc.gif" alt="Kubernetes集群图" width="100%" height="100%" />

### 4、svc

Service(简称svc)是对一组提供相同功能的Pods的抽象，并为它们提供一个统一的入口。借助Service，应用可以方便的实现服务发现与负载均衡，并实现应用的零宕机升级。

<img src="/images/kubernetes-svc.gif" alt="Kubernetes集群图" width="100%" height="100%" />

## k8s中应用的服务发布





# 参考资料

* [细数Kubernetes Service那些事－kubernetes 服务发布以及在eBay的实践](http://blog.csdn.net/ebay/article/details/52798074)
* [Kubernetes Handbook](https://kubernetes.feisky.xyz/)
* [十分钟带你理解Kubernetes核心概念](http://www.dockone.io/article/932)