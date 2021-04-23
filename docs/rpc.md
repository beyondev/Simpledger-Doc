## 第3章 RPC接口说明

---------------

##JSON RPC API

-----------
JSON是一种轻量级的数据交换格式。它可以表示数字、字符串、值的有序序列和名称/值对的集合。

JSON-RPC是一种无状态、轻量的远程过程调用（RPC）协议。本规范主要定义了几种数据结构及其处理规则。这是传输不可知论，因为概念可以在同一进程、套接字、HTTP或许多不同的消息传递环境中使用。它使用JSON（RFC4627）作为数据格式。

### JavaScript API

-----------------
要从javascript应用程序内部与simplechain节点通信，请使用web3.js库，它为rpc方法提供了一个方便的接口

### JSON-RPC Endpoint

----------------

Default JSON-RPC endpoints:

|Client| URL                    |
| ---- |        ----            |
|  sipe| http://localhost:9546  |



### sipe
你可以使用--rpc标志启动http json-rpc

```shell
sipe --rpc
```

修改默认端口（9546）和列表地址（localhost）更改为:
```shell
sipe --rpc --rpcaddr <ip> --rpcport <portnumber>
```

如果从浏览器访问RPC，则需要使用适当的域集启用CORS。否则，javascript调用将受到同一源策略的限制，请求将失败:

```shell
sipe --rpc --rpccrosdomain "http://localhost:3000"
```
也可以使用admin.startrpc（addr，port）命令从sipe控制台启动json rpc。


### 十六进制值编码

-----------------------
目前有两种主要的数据类型通过JSON传递：未格式化的字节数组和数量。两者都通过十六进制编码传递，但是对格式的要求不同:

当编码数量（整数，数字）编码为十六进制值，以0x为前缀，最紧凑的表现形式（稍有不一样：0应该表示为0x0）,举一些例子：

- 0x41(65十进制)
- 0x400(1024十进制)
- 错误的示例：0x(应该至少有一个数字，所以为0x0)
- 错误的示例：0x0400(不允许前导零)
- 错误的示例：ff(必须前缀0x)

编码未格式化数据时（字节数组、帐户地址、哈希、字节码数组），编码为十六进制值，以0x为前缀，每两个十六进制数字一个字节，举一些例子：

- 0x41(size 1, "A")
- 0x004200(size 3, "\0B\0")
- 0x(size 0,"")
- 错误的示例：0xf0f0f(必须是偶数位)
- 错误的示例：004200(必须以0x为前缀)

目前go-simpledger通过HTTP和IPC（Windows上的Unix Socket Linux和OSX/命名管道）提供JSON-RPC通信。

### JSON RPC API 参考

--------------------------

#### txpool_status

查看交易池状态

参数：
无

返回： 交易池交易的数量

示例
```text
#请求
curl localhost:8545 -X POST -H "Content-Type:application/json" -d '{"jsonrpc": "2.0", "method":"txpool_status", "params": [],"id":66}'

#结果
{
	"jsonrpc":"2.0",
	"id":66,
	"result":{"pending":"0x1770","queued":"0x0"}
}
```

----------------------

#### txpool_content

查看交易池详情

参数：
1.QUANTITY要查询的交易数量

params: ['2']

返回： 交易池交易的数量

示例
```text
#请求
curl localhost:8545 -X POST -H "Content-Type:application/json" -d '{"jsonrpc": "2.0", "method":"txpool_content", "params": ['2'],"id":66}'

#结果
{
    "jsonrpc": "2.0",
    "id": 66,
    "result": {
        "pending": {
            "0xAe68AE6fD612b1bf54c5719Be57DeB9BF08ab0aA": {
                "10000000000": {
                    "blockHash": null,
                    "blockNumber": null,
                    "from": "0xae68ae6fd612b1bf54c5719be57deb9bf08ab0aa",
                    "gas": "0x186a0",
                    "gasPrice": "0x3b9aca00",
                    "hash": "0x12ffc98f76dfb1f2dfc27aa30f700dfdca6c327cfd713687392943c8ea6bd6c7",
                    "input": "0xbae00c981d81f1152bd8083bb8c96e2e880ec9f68e50d9c024323e57e59b4ed7",
                    "nonce": "0x2540be400",
                    "to": "0xfffffffffffffffffffffffffffffffffffffff0",
                    "transactionIndex": null,
                    "value": "0x1",
                    "v": "0x514c",
                    "r": "0xc21ce8018f94614ea48cfea0688398a38bb9e7da5da8f1feb371b17db2028ee1",
                    "s": "0x3aca89604b53a8c1be1a87c214b495c5a4836744f141d2959d68311a42872748"
                },
                "10005000000": {
                    "blockHash": null,
                    "blockNumber": null,
                    "from": "0xae68ae6fd612b1bf54c5719be57deb9bf08ab0aa",
                    "gas": "0x186a0",
                    "gasPrice": "0x3b9aca00",
                    "hash": "0x6b9a5d1208871a005c23ea7ef875c734b0d678f36e2682c58b0e387511ef200d",
                    "input": "0xbae00c981d81f1152bd8083bb8c96e2e880ec9f68e50d9c024323e57e59b4ed7",
                    "nonce": "0x254582f40",
                    "to": "0xfffffffffffffffffffffffffffffffffffffff0",
                    "transactionIndex": null,
                    "value": "0x1",
                    "v": "0x514b",
                    "r": "0x5937bfb81e05e39cd9a8d0b61d8dbf42a0c554e4445a96c09a072ba3d7e99346",
                    "s": "0x7d10825da35b94d615b11719677b10c844f38159eaf632606010097ab8009f13"
                }
            }
        },
        "queued": {}
    }
}
```

----------------------

#### permission_addNewNodeApply

申请新节点为观察节点

参数：

- enode: 节点enode字符串
  
- nodeName： 字符串，节点别名
  
- from：20字节，交易发起者的地址
```text
params: [
    'enode://0922743d9409959e788a8bcdde1fbde9336709d72abdd9e681979551e6e171631f2f9459330dcb18922c14eacb5b507aba74a86a81832389c8618ca533019147@192.168.111.111:21001?discport=0',
    'test',
   '0xc0560d2e856d12102d31dc2250fe1c7dbcfa8013'
]
```

返回： 执行交易后返回的交易hash，错误信息

示例
```text
#请求
curl localhost:9546 -X POST -H "Content-Type:application/json" -d '{"jsonrpc": "2.0", "method":"permission_addNewNodeApply", "params": ["enode://0922743d9409959e788a8bcdde1fbde9336709d72abdd9e681979551e6e171631f2f9459330dcb18922c14eacb5b507aba74a86a81832389c8618ca533019147@192.168.111.111:21001?discport=0","name","0xc0560d2e856d12102d31dc2250fe1c7dbcfa8013"],"id":66}'

#结果
{"jsonrpc":"2.0","id":66,"result":"0xf54ea2e12935f1f47b53d347152a388d0419217e947a6f7654e0dd4d35eb13d0"}
```

----------------------

### permission_addNodeToBlackVerify

添加节点至黑名单

参数：

- enode： 字符串，节点enode
  
- from： 20字节，交易发起者的地址

示例:

```text
params: ["enode://0922743d9409959e788a8bcdde1fbde9336709d72abdd9e681979551e6e171631f2f9459330dcb18922c14eacb5b507aba74a86a81832389c8618ca533019147@192.168.111.111:21001?discport=0","0xc0560d2e856d12102d31dc2250fe1c7dbcfa8013"]
```

返回： 交易执行完成后的hash

示例：

```text
#请求
curl localhost:9546 -X POST -H "Content-Type:application/json" -d '{"jsonrpc":"2.0","method":"permission_addNodeToBlackVerify","params": ["enode://0922743d9409959e788a8bcdde1fbde9336709d72abdd9e681979551e6e171631f2f9459330dcb18922c14eacb5b507aba74a86a81832389c8618ca533019147@192.168.111.111:21001?discport=0","0xc0560d2e856d12102d31dc2250fe1c7dbcfa8013"],"id":1}'

#结果
{"jsonrpc":"2.0","id":1,"result":["0x3d9d9349dca94094720dee9c0677413b9092fc4c2cbb493a80d2c4c2c260a208",""]}
```

----------------------

###  permission_applyByAdmin

通过管理节点添加观察节点的申请名单

参数：

- enode： 字符串，节点enode

- opCode： int，操作码

- from： 20字节地址，交易发起者的地址

```text
params: ["enode://0922743d9409959e788a8bcdde1fbde9336709d72abdd9e681979551e6e171631f2f9459330dcb18922c14eacb5b507aba74a86a81832389c8618ca533019147@192.168.111.111:21001?discport=0",1,"0xc0560d2e856d12102d31dc2250fe1c7dbcfa8013"]
```

返回： 交易执行完成后的hash

示例：

```text
#请求
curl localhost:9546 -X POST -H "Content-Type:application/json" -d '{"jsonrpc":"2.0","method":"permission_applyByAdmin","params": ["enode://0922743d9409959e788a8bcdde1fbde9336709d72abdd9e681979551e6e171631f2f9459330dcb18922c14eacb5b507aba74a86a81832389c8618ca533019147@192.168.111.111:21001?discport=0",1,"0xc0560d2e856d12102d31dc2250fe1c7dbcfa8013"],"id":1}'

#结果
{"jsonrpc":"2.0","id":1,"result":"0x910286dd59935195ad68591b885acd76fd6253f1dbddb290d8de18efe4b4beb0"}
```

----------------

### permission_disagree

拒绝投票申请

参数：

- enode： 字符串，节点enode

- opCode： int，操作码

- from： 20字节地址，交易发起者的地址

```text
params: ["enode://0922743d9409959e788a8bcdde1fbde9336709d72abdd9e681979551e6e171631f2f9459330dcb18922c14eacb5b507aba74a86a81832389c8618ca533019147@192.168.111.111:21001?discport=0",1,"0xc0560d2e856d12102d31dc2250fe1c7dbcfa8013"]
```

返回： 执行交易后的hash

示例：

```text
#请求
curl localhost:9546 -X POST -H "Content-Type:application/json" -d '{"jsonrpc":"2.0","method":"permission_disagree","params": ["enode://0922743d9409959e788a8bcdde1fbde9336709d72abdd9e681979551e6e171631f2f9459330dcb18922c14eacb5b507aba74a86a81832389c8618ca533019147@192.168.111.111:21001?discport=0",1,"0xc0560d2e856d12102d31dc2250fe1c7dbcfa8013"],"id":1}'

#结果
{"jsonrpc":"2.0","id":1,"result":"0x8a8f3e3ce956b0bea255f4749ad475178da59bf659b25b181b7a83377c624291"}
```

---------------

### permission_findNotVoteRecordForMe

查找该节点是否为本次投过票

参数：

- from： 20字节，交易发起者地址

- stateKey： 字符串，申请者enodeId+opCode

```text
"param": ["0xc0560d2e856d12102d31dc2250fe1c7dbcfa8013","enode://0922743d9409959e788a8bcdde1fbde9336709d72abdd9e681979551e6e171631f2f9459330dcb18922c14eacb5b507aba74a86a81832389c8618ca533019147@192.168.111.111:21001?discport=01"]
```

返回： 本节点是否投过票

示例：

```text
#请求
curl localhost:9546 -X POST -H "Content-Type:application/json" -d '{"jsonrpc":"2.0","method": "permission_findNotVoteRecordForMe","param": ["0xc0560d2e856d12102d31dc2250fe1c7dbcfa8013","enode://0922743d9409959e788a8bcdde1fbde9336709d72abdd9e681979551e6e171631f2f9459330dcb18922c14eacb5b507aba74a86a81832389c8618ca533019147@192.168.111.111:21001?discport=01"],"id":1}'

#结果
{"jsonrpc":"2.0","id":1,"result":[false,""]}
```

---------------

### permission_getAllNode

请求不同身份的节点：0=normal,1=seprate,2=admin,3=normal and admin

参数：

- nodeType: int,节点类型

- from: 20字节，交易发起者地址

```text
"param": [3,"0xc0560d2e856d12102d31dc2250fe1c7dbcfa8013"]
```

返回： 对应类型的所有节点的enode

示例：

```text
#请求
curl localhost:9546 -X POST -H "Content-Type:application/json" -d '{"jsonrpc":"2.0", "method": "permission_getAllNode", "params": [3,"0xc0560d2e856d12102d31dc2250fe1c7dbcfa8013"],"id":1}'

#结果
{"jsonrpc":"2.0","id":1,"result":"enode://9cae78c7c2344f7d52a568bcfc2eff90cc4f7339efe28dc2f29cc5150b78b7ecf844008f5db47cece8c9931f2218ce659229158feb31cb9abbcf32fe09bbac6b,"}
```

----------------

### permission_getAllStatingRecord

查询所有投票未完成的记录

参数：

- from： 20字节，交易发起者地址

```text
"param": "0xc0560d2e856d12102d31dc2250fe1c7dbcfa8013"
```

返回： 返回字符串（enodeId+opType），以逗号分割

示例：

```text
#请求
curl localhost:9546 -X POST -H "Content-Type:application/json" -d '{"jsonrpc":"2.0", "method": "permission_getAllStatingRecord","params": ["0xc0560d2e856d12102d31dc2250fe1c7dbcfa8013"] ,"id":1}'

#结果
{"jsonrpc":"2.0","id":1,"result":",enode://0922743d9409959e788a8bcdde1fbde9336709d72abdd9e681979551e6e171631f2f9459330dcb18922c14eacb5b507aba74a86a81832389c8618ca5330191470"}
```

### permission_getContractAddress

返回当前正在使用的合约地址

参数： 空

返回：返回当前正在使用的合约地址

示例：

```text
#请求
curl localhost:9546 -X POST -H "Content-Type:application/json" -d '{"jsonrpc":"2.0", "method": "permission_getContractAddress","params":[] ,"id":1}'

#结果
{"jsonrpc":"2.0","id":1,"result":"0xc904510CD1C53655fDf541aEdd467C44A187daF9"}
```

[comment]: <> (------------)

[comment]: <> (### permission_getNodeMap)

[comment]: <> (获得节点的角色)

[comment]: <> (参数： )

[comment]: <> (- enode: 字符串，节点enode)

[comment]: <> (- from： 20字节，交易发起者地址)

[comment]: <> (```text)

[comment]: <> ("param": ["enode://0922743d9409959e788a8bcdde1fbde9336709d72abdd9e681979551e6e171631f2f9459330dcb18922c14eacb5b507aba74a86a81832389c8618ca533019147@192.168.111.111:21001?discport=0","0xc0560d2e856d12102d31dc2250fe1c7dbcfa8013"])

[comment]: <> (```)

[comment]: <> (返回： 对应节点角色)

[comment]: <> (示例)

[comment]: <> (```text)

[comment]: <> (#请求)

[comment]: <> (curl localhost:9546 -X POST -H "Content-Type:application" -d '{"jsonrpc":"2.0", "method": "permission_getNodeMap", ,"id":1}')

[comment]: <> (#结果)

[comment]: <> (```)

[comment]: <> (-----------------------)

[comment]: <> (### permission_getStorageAt)


---------------

### permission_initFinish

执行此方法后，不允许修改管理员

参数：

- from: 20字节，发起交易者地址

```text
"params": ["0xc0560d2e856d12102d31dc2250fe1c7dbcfa8013"]
```

返回： 交易hash和错误

示例：

```text
#请求
curl localhost:9546 -X POST -H "Content-Type:application/json" -d '{"jsonrpc":"2.0", "method": "permission_initFinish", "params": ["0xc0560d2e856d12102d31dc2250fe1c7dbcfa8013"],"id":1}'

#结果
{"jsonrpc":"2.0","id":1,"result":"0x2886d0b3546d0a17ccb7f075d4abd52ef3fd2c55a656d782fbc08a9e8e4e5358"}
```
-----------

### permission_isAdmin

判断指定节点是否为管理节点

参数：

- enode： 字符串，节点enode

- from： 20字节，交易发起者地址

```text
"params": ["enode://0922743d9409959e788a8bcdde1fbde9336709d72abdd9e681979551e6e171631f2f9459330dcb18922c14eacb5b507aba74a86a81832389c8618ca533019147@192.168.111.111:21001?discport=0","0xc0560d2e856d12102d31dc2250fe1c7dbcfa8013"]
```

返回： 指定节点是否为管理节点

```text
#请求
curl localhost:9546 -X POST -H "Content-Type:application/json" -d '{"jsonrpc":"2.0", "method": "permission_isAdmin", "params": ["enode://0922743d9409959e788a8bcdde1fbde9336709d72abdd9e681979551e6e171631f2f9459330dcb18922c14eacb5b507aba74a86a81832389c8618ca533019147@192.168.111.111:21001?discport=0","0xc0560d2e856d12102d31dc2250fe1c7dbcfa8013"],"id":1}'

#结果
{"jsonrpc":"2.0","id":1,"result":false}
```

------------------------

### permission_nodeDowngradeVerify

节点降级投票

参数：

- enode: 字符串， 节点enode

- from： 20字节， 交易发起者地址

```text
"params": ["enode://0922743d9409959e788a8bcdde1fbde9336709d72abdd9e681979551e6e171631f2f9459330dcb18922c14eacb5b507aba74a86a81832389c8618ca533019147@192.168.111.111:21001?discport=0","0xc0560d2e856d12102d31dc2250fe1c7dbcfa8013"]
```

返回： 交易hash，错误

示例：

```text
#请求
curl localhost:9546 -X POST -H "Content-Type:application/json" -d '{"jsonrpc":"2.0", "method": "permission_nodeDowngradeVerify", "params": ["enode://0922743d9409959e788a8bcdde1fbde9336709d72abdd9e681979551e6e171631f2f9459330dcb18922c14eacb5b507aba74a86a81832389c8618ca533019147@192.168.111.111:21001?discport=0","0xc0560d2e856d12102d31dc2250fe1c7dbcfa8013"],"id":1}'

#结果
{"jsonrpc": "2.0", "id":1, "result":["0x3d9d9349dca94094720dee9c0677413b9092fc4c2cbb493a80d2c4c2c260a208",""]}
```

----------------------

### permission_nodeUpgradeVerify

节点升级投票

参数：

- enode: 字符串， 节点enode

- from： 20字节， 交易发起者地址

```text
"params": ["enode://0922743d9409959e788a8bcdde1fbde9336709d72abdd9e681979551e6e171631f2f9459330dcb18922c14eacb5b507aba74a86a81832389c8618ca533019147@192.168.111.111:21001?discport=0","0xc0560d2e856d12102d31dc2250fe1c7dbcfa8013"]
```

返回： 交易hash，错误

示例：

```text
#请求
curl localhost:9546 -X POST -H "Content-Type:application/json" -d '{"jsonrpc":"2.0", "method": "permission_nodeUpgradeVerify", "params": ["enode://0922743d9409959e788a8bcdde1fbde9336709d72abdd9e681979551e6e171631f2f9459330dcb18922c14eacb5b507aba74a86a81832389c8618ca533019147@192.168.111.111:21001?discport=0","0xc0560d2e856d12102d31dc2250fe1c7dbcfa8013"],"id":1}'

#结果
{"jsonrpc": "2.0", "id":1, "result":["0x3d9d9349dca94094720dee9c0677413b9092fc4c2cbb493a80d2c4c2c260a208",""]}
```

--------------

### permission_setAdminNode

添加超级节点信息

参数：

- enode: 字符串， 节点enode

- nodeName: 字符串，节点别名

- from： 20字节， 交易发起者地址

```text
"params": ["enode://0922743d9409959e788a8bcdde1fbde9336709d72abdd9e681979551e6e171631f2f9459330dcb18922c14eacb5b507aba74a86a81832389c8618ca533019147@192.168.111.111:21001?discport=0","wuff","0xc0560d2e856d12102d31dc2250fe1c7dbcfa8013"]
```

返回： 交易hash，错误

示例：

```text
#请求
curl localhost:9546 -X POST -H "Content-Type:application/json" -d '{"jsonrpc":"2.0", "method": "permission_setAdminNode", "params": ["enode://0922743d9409959e788a8bcdde1fbde9336709d72abdd9e681979551e6e171631f2f9459330dcb18922c14eacb5b507aba74a86a81832389c8618ca533019147@192.168.111.111:21001?discport=0","wuff","0xc0560d2e856d12102d31dc2250fe1c7dbcfa8013"],"id":1}'

#结果
{"jsonrpc":"2.0","id":1,"result":"0xe27788f0548ebc055d61e147c1ed7adce3060800bba999932d4afe797807185a"}
```

--------------------

### permission_setContractAddress

设置管理节点身份的合约地址

参数：

- addr： 20字节地址，合约地址

```text
"params":["0xc0560d2e856d12102d31dc2250fe1c7dbcfa8013"]
```

返回： "success",err

示例：

```text
#请求
curl localhost:9546 -X POST -H "Content-Type:application/json" -d '{"jsonrpc":"2.0", "method": "permission_setContractAddress", "params":["0xc0560d2e856d12102d31dc2250fe1c7dbcfa8013"],"id":1}'

#结果
{"jsonrpc":"2.0","id":1,"result":"success"}
```

---------------
### permission_updateNodeInfo

更新自身的节点信息

参数：

- ip： 字符串，ip
- port： 字符串，端口
- from： 20字节，交易发起者地址

```text
"params":["192.168.6.66","666","0xc0560d2e856d12102d31dc2250fe1c7dbcfa8013"]
```

返回： 交易hash，错误

示例：
```text
#请求
curl localhost:9546 -X POST -H "Content-Type:application/json" -d '{"jsonrpc":"2.0", "method": "permission_updateNodeInfo", "params":["192.168.6.66","666","0xc0560d2e856d12102d31dc2250fe1c7dbcfa8013"],"id":1}'

#结果
{"jsonrpc":"2.0","id":1,"result":"0x1e63892a5df025819d2e70ba9cfe420990ad2256f84edef7ce28a7bd1cd54b39"}
```

---------------------
### permission_verifyAddNodeApply
验证节点申请

参数：

- enode： 字符串，节点enode
- from： 20字节，交易发起者地址

```text
"params": ["enode://0922743d9409959e788a8bcdde1fbde9336709d72abdd9e681979551e6e171631f2f9459330dcb18922c14eacb5b507aba74a86a81832389c8618ca533019147@192.168.111.111:21001?discport=0","0xc0560d2e856d12102d31dc2250fe1c7dbcfa8013"]
```

返回： 交易hash，错误

示例：
```text
#请求
curl localhost:9546 -X POST -H "Content-Type:application/json" -d '{"jsonrpc":"2.0", "method": "permission_verifyAddNodeApply", "params": ["enode://0922743d9409959e788a8bcdde1fbde9336709d72abdd9e681979551e6e171631f2f9459330dcb18922c14eacb5b507aba74a86a81832389c8618ca533019147@192.168.111.111:21001?discport=0","0xc0560d2e856d12102d31dc2250fe1c7dbcfa8013"],"id":1}'

#结果
{"jsonrpc": "2.0", "id":1, "result": ["0x3d9d9349dca94094720dee9c0677413b9092fc4c2cbb493a80d2c4c2c260a208",""]}
```
