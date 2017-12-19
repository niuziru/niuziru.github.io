---
layout: post
title: ETCD REST API示例
categories:
- Technology
tags:
- ETCD
---
学习自segmentfault[etcd rest api基本操作](https://segmentfault.com/a/1190000005649865)
# ETCD示例
* 通过浏览器/curl访问地址：

http://discovery.etcd.io/e77afb997af5a84983baa98fd42cf12f

```sh
zero@zero ~> curl http://discovery.etcd.io/e77afb997af5a84983baa98fd42cf12f
{"action":"get","node":{"key":"/_etcd/registry/e77afb997af5a84983baa98fd42cf12f","dir":true,"nodes":[{"key":"/_etcd/registry/e77afb997af5a84983baa98fd42cf12f/f84fe4a4e816e778","value":"etcd1=http://192.168.99.102:2380","modifiedIndex":1113261726,"createdIndex":1113261726},{"key":"/_etcd/registry/e77afb997af5a84983baa98fd42cf12f/4eb2dcba58da982f","value":"etcd0=http://192.168.99.101:2380","modifiedIndex":1113260929,"createdIndex":1113260929},{"key":"/_etcd/registry/e77afb997af5a84983baa98fd42cf12f/aa5569b385caf33b","value":"etcd2=http://192.168.99.103:2380","modifiedIndex":1113261715,"createdIndex":1113261715}],"modifiedIndex":1113260647,"createdIndex":1113260647}}
```
* 返回值：

```json
{
    "action": "get",
    "node": {
        "key": "/_etcd/registry/e77afb997af5a84983baa98fd42cf12f",
        "dir": true,
        "nodes": [
            {
                "key": "/_etcd/registry/e77afb997af5a84983baa98fd42cf12f/4eb2dcba58da982f",
                "value": "etcd0=http://192.168.99.101:2380",
                "modifiedIndex": 1113260929,
                "createdIndex": 1113260929
            },
            {
                "key": "/_etcd/registry/e77afb997af5a84983baa98fd42cf12f/aa5569b385caf33b",
                "value": "etcd2=http://192.168.99.103:2380",
                "modifiedIndex": 1113261715,
                "createdIndex": 1113261715
            },
            {
                "key": "/_etcd/registry/e77afb997af5a84983baa98fd42cf12f/f84fe4a4e816e778",
                "value": "etcd1=http://192.168.99.102:2380",
                "modifiedIndex": 1113261726,
                "createdIndex": 1113261726
            }
        ],
        "modifiedIndex": 1113260647,
        "createdIndex": 1113260647
    }
}
```

# 下载启动ETCD

## 下载ETCD

* `sudo docker pull quay.io/coreos/etcd`

* `sudo docker pull quay.io/coreos/etcd:v3.1.9`


## `sudo docker run -d -p 2379:2379 -p 2380:2380 -name etcd quay.io/coreos/etcd:v3.2.0`


* V2版本端口: `3379`
* V3版本端口: `2379`

# 查看版本

## V2

```sh
zero@zero ~> curl http://127.0.0.1:3379/version
etcd 2.0.10⏎
```

## V3

```sh
zero@zero ~> curl http://127.0.0.1:2379/version
{"etcdserver":"3.2.11","etcdcluster":"3.2.0"}⏎
```

# 查看键

## V2

```sh
zero@zero ~> curl http://127.0.0.1:3379/v2/keys
{"action":"get","node":{"dir":true}}
```

## V3

```sh
zero@zero ~> curl http://127.0.0.1:2379/v2/keys
{"action":"get","node":{"dir":true}}
```

# 创建键值

## V2 & V3

```sh
zero@zero ~> curl -X PUT http://127.0.0.1:2379/v2/keys/hello -d value="world"
{"action":"set","node":{"key":"/hello","value":"world","modifiedIndex":13,"createdIndex":13}}
```

格式化返回值：

```json
{
    "action": "set",
    "node": {
        "key": "/hello",
        "value": "world",
        "modifiedIndex": 13,
        "createdIndex": 13
    }
}
```

# 创建目录

## V2 & V3

```sh
zero@zero ~> curl -X PUT http://127.0.0.1:2379/v2/keys/zhongguo -d dir=true
{"action":"set","node":{"key":"/zhongguo","dir":true,"modifiedIndex":14,"createdIndex":14}}
```

格式化应答：

```json
{
    "action": "set",
    "node": {
        "key": "/zhongguo",
        "dir": true,
        "modifiedIndex": 14,
        "createdIndex": 14
    }
}
```

# 查看键

## V2 & V3

```sh
zero@zero ~> curl -X GET http://127.0.0.1:2379/v2/keys/
{"action":"get","node":{"dir":true,"nodes":[{"key":"/hello","value":"world","modifiedIndex":13,"createdIndex":13},{"key":"/zhongguo","dir":true,"modifiedIndex":14,"createdIndex":14}]}}
```

格式化应答：

```json
{
    "action": "get",
    "node": {
        "dir": true,
        "nodes": [
            {
                "key": "/hello",
                "value": "world",
                "modifiedIndex": 13,
                "createdIndex": 13
            },
            {
                "key": "/zhongguo",
                "dir": true,
                "modifiedIndex": 14,
                "createdIndex": 14
            }
        ]
    }
}
```

# 创建带TTL的键值

```sh
zero@zero ~> curl -X PUT http://127.0.0.1:2379/v2/keys/ttlvar -d value="ttl_value" -d ttl=5
{"action":"set","node":{"key":"/ttlvar","value":"ttl_value","expiration":"2017-12-19T00:42:25.850309128Z","ttl":5,"modifiedIndex":15,"createdIndex":15}}
```

* 格式化应答值：

```json
{
    "action": "set",
    "node": {
        "key": "/ttlvar",
        "value": "ttl_value",
        "expiration": "2017-12-19T00:42:25.850309128Z",
        "ttl": 5,
        "modifiedIndex": 15,
        "createdIndex": 15
    }
}
```


* 5秒后查询,键值自动删除

```sh
zero@zero ~> curl -X GET http://127.0.0.1:2379/v2/keys/ttlvar
{"errorCode":100,"message":"Key not found","cause":"/ttlvar","index":16}
```

# 创建有序键值

```sh
zero@zero ~> curl -X POST http://127.0.0.1:2379/v2/keys/seqvar -d value="seq1"
{"action":"create","node":{"key":"/seqvar/00000000000000000017","value":"seq1","modifiedIndex":17,"createdIndex":17}}
zero@zero ~> curl -X POST http://127.0.0.1:2379/v2/keys/seqvar -d value="seq1"
{"action":"create","node":{"key":"/seqvar/00000000000000000018","value":"seq1","modifiedIndex":18,"createdIndex":18}}
zero@zero ~> curl -X POST http://127.0.0.1:2379/v2/keys/seqvar -d value="seq1"
{"action":"create","node":{"key":"/seqvar/00000000000000000019","value":"seq1","modifiedIndex":19,"createdIndex":19}}
zero@zero ~> curl -X POST http://127.0.0.1:2379/v2/keys/seqvar -d value="seq1"
{"action":"create","node":{"key":"/seqvar/00000000000000000020","value":"seq1","modifiedIndex":20,"createdIndex":20}}
```

## 查询结果

```sh
curl -X GET http://127.0.0.1:2379/v2/keys/seqvar
```

* 格式化应答

```json
{
    "action": "create",
    "node": {
        "key": "/seqvar/00000000000000000017",
        "value": "seq1",
        "modifiedIndex": 17,
        "createdIndex": 17
    }
}zero@zero~>curl-XPOSThttp: //127.0.0.1: 2379/v2/keys/seqvar-dvalue="seq1"{
    "action": "create",
    "node": {
        "key": "/seqvar/00000000000000000018",
        "value": "seq1",
        "modifiedIndex": 18,
        "createdIndex": 18
    }
}zero@zero~>curl-XPOSThttp: //127.0.0.1: 2379/v2/keys/seqvar-dvalue="seq1"{
    "action": "create",
    "node": {
        "key": "/seqvar/00000000000000000019",
        "value": "seq1",
        "modifiedIndex": 19,
        "createdIndex": 19
    }
}zero@zero~>curl-XPOSThttp: //127.0.0.1: 2379/v2/keys/seqvar-dvalue="seq1"{
    "action": "create",
    "node": {
        "key": "/seqvar/00000000000000000020",
        "value": "seq1",
        "modifiedIndex": 20,
        "createdIndex": 20
    }
}
```

# 删除制定的键

```sh
zero@zero ~> curl -X DELETE http://127.0.0.1:2379/v2/keys/seqvar/00000000000000000017
{"action":"delete","node":{"key":"/seqvar/00000000000000000017","modifiedIndex":21,"createdIndex":17},"prevNode":{"key":"/seqvar/00000000000000000017","value":"seq1","modifiedIndex":17,"createdIndex":17}}
```

* 格式化应答值

```json
{
    "action": "delete",
    "node": {
        "key": "/seqvar/00000000000000000017",
        "modifiedIndex": 21,
        "createdIndex": 17
    },
    "prevNode": {
        "key": "/seqvar/00000000000000000017",
        "value": "seq1",
        "modifiedIndex": 17,
        "createdIndex": 17
    }
}
```

# 成员管理

```sh
zero@zero ~> curl -X GET http://127.0.0.1:2379/v2/members
{"members":[{"id":"8e9e05c52164694d","name":"default","peerURLs":["http://localhost:2380"],"clientURLs":["http://localhost:2379"]}]}
```

* 格式化应答

```json
{
    "members": [
        {
            "id": "8e9e05c52164694d",
            "name": "default",
            "peerURLs": [
                "http://localhost:2380"
            ],
            "clientURLs": [
                "http://localhost:2379"
            ]
        }
    ]
}
```

# 统计信息

## 查看Leader

```sh
curl http://192.168.99.101:2379/v2/stats/leader
```

* 格式化应答信息

```json
{
    "leader": "4eb2dcba58da982f",
    "followers": {
        "aa5569b385caf33b": {
            "latency": {
                "current": 0.001687,
                "average": 0.0026333315088053265,
                "standardDeviation": 0.0082522530707236,
                "minimum": 0.000508,
                "maximum": 0.184366
            },
            "counts": {
                "fail": 0,
                "success": 8404
            }
        },
        "f84fe4a4e816e778": {
            "latency": {
                "current": 0.001158,
                "average": 0.017216567181926247,
                "standardDeviation": 1.236027691414708,
                "minimum": 0.000493,
                "maximum": 113.333953
            },
            "counts": {
                "fail": 0,
                "success": 8410
            }
        }
    }
}
```

# 节点自身信息

```sh
curl http://192.168.99.101:2379/v2/stats/self
```

* 格式化应答信息

```json
{
    "name": "etcd0",
    "id": "4eb2dcba58da982f",
    "state": "StateLeader",
    "startTime": "2016-06-04T12:51:22.901345036Z",
    "leaderInfo": {
        "leader": "4eb2dcba58da982f",
        "uptime": "28m29.401994375s",
        "startTime": "2016-06-04T12:51:23.406751734Z"
    },
    "recvAppendRequestCnt": 0,
    "sendAppendRequestCnt": 17544,
    "sendPkgRate": 10.52589669646476,
    "sendBandwidthRate": 746.7071116472099
}
```

# 查看集群运行状态

```sh
curl http://192.168.99.101:2379/v2/stats/store
```

* 格式化应答

```json
{
    "getsSuccess": 7,
    "getsFail": 16,
    "setsSuccess": 8,
    "setsFail": 0,
    "deleteSuccess": 1,
    "deleteFail": 0,
    "updateSuccess": 0,
    "updateFail": 0,
    "createSuccess": 6,
    "createFail": 0,
    "compareAndSwapSuccess": 0,
    "compareAndSwapFail": 0,
    "compareAndDeleteSuccess": 0,
    "compareAndDeleteFail": 0,
    "expireCount": 1,
    "watchers": 0
}
```