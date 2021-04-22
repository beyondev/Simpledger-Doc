

### JSON RPC

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
|  sipe| http://localhost:8546  |



### sipe
你可以使用--rpc标志启动http json-rpc

```shell
sipe --rpc
```

修改默认端口（8546）和列表地址（localhost）更改为:
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

#### permission_addNewNodeApply

申请新节点为观察节点

参数：

- enode: 节点enode字符串
  
- nodeName： 字符串，无意义
  
- from：20字节，交易发起者的地址
```text
params: [
    'enode://0922743d9409959e788a8bcdde1fbde9336709d72abdd9e681979551e6e171631f2f9459330dcb18922c14eacb5b507aba74a86a81832389c8618ca533019147@192.168.111.111:21001?discport=0',
    'test',
   '0x51e766a7f073955c8061073bbba60b10bf12d48a'
]
```

返回： 执行交易后返回的交易hash，错误信息

示例
```text
#请求
curl localhost:8546 -X POST -H "Content-Type:application/json" -d '{"jsonrpc": "2.0", "method":"permission_addNewNodeApply", "params": ["enode://0922743d9409959e788a8bcdde1fbde9336709d72abdd9e681979551e6e171631f2f9459330dcb18922c14eacb5b507aba74a86a81832389c8618ca533019147@192.168.111.111:21001?discport=0","name","0x51e766a7f073955c8061073bbba60b10bf12d48a"],"id":66}''

#结果
{
	"jsonrpc":"2.0",
	"id":66,
	"result":["0xd79b8287a827e1387e6f0ff6d300fc663e10f592",""]
}
```

----------------------

### permission_addNodeToBlackVerify

添加节点至黑名单

参数：

- enode： 字符串，节点enode
  
- from： 20字节，交易发起者的地址

示例:

```text
params: ["enode://0922743d9409959e788a8bcdde1fbde9336709d72abdd9e681979551e6e171631f2f9459330dcb18922c14eacb5b507aba74a86a81832389c8618ca533019147@192.168.111.111:21001?discport=0","0x51e766a7f073955c8061073bbba60b10bf12d48a"]
```

返回： 交易执行完成后的hash

示例：

```text
#请求
curl localhost:8546 -X POST -H "Content-Type:application/json" -d '{"jsonrpc":"2.0","method":"permission_addNodeToBlackVerify","params": ["enode://0922743d9409959e788a8bcdde1fbde9336709d72abdd9e681979551e6e171631f2f9459330dcb18922c14eacb5b507aba74a86a81832389c8618ca533019147@192.168.111.111:21001?discport=0","0x51e766a7f073955c8061073bbba60b10bf12d48a"], "latest"],"id":1}'

#结果
{"jsonrpc":"2.0","id":1,"result":["0xd79b8287a827e1387e6f0ff6d300fc663e10f592",""]}
```

----------------------

###  permission_applyByAdmin

通过管理节点添加观察节点的申请名单

参数：

- enode： 字符串，节点enode

- opCode： int，操作码

- from： 20字节地址，交易发起者的地址

```text
params: ["enode://0922743d9409959e788a8bcdde1fbde9336709d72abdd9e681979551e6e171631f2f9459330dcb18922c14eacb5b507aba74a86a81832389c8618ca533019147@192.168.111.111:21001?discport=0",1,"0x51e766a7f073955c8061073bbba60b10bf12d48a"]
```

返回： 交易执行完成后的hash

示例：

```text
#请求
curl localhost:8546 -X POST -H "Content-Type:application/json" -d '{"jsonrpc":"2.0","method":"permission_applyByAdmin","params": ["enode://0922743d9409959e788a8bcdde1fbde9336709d72abdd9e681979551e6e171631f2f9459330dcb18922c14eacb5b507aba74a86a81832389c8618ca533019147@192.168.111.111:21001?discport=0",1,"0x51e766a7f073955c8061073bbba60b10bf12d48a"],"id":1}'

#结果
{"jsonrpc":"2.0","id":1,"result":["0xd79b8287a827e1387e6f0ff6d300fc663e10f592",""]}
```

----------------

### permission_disagree

拒绝投票申请

参数：

- enode： 字符串，节点enode

- opCode： int，操作码

- from： 20字节地址，交易发起者的地址

```text
params: ["enode://0922743d9409959e788a8bcdde1fbde9336709d72abdd9e681979551e6e171631f2f9459330dcb18922c14eacb5b507aba74a86a81832389c8618ca533019147@192.168.111.111:21001?discport=0",1,"0x51e766a7f073955c8061073bbba60b10bf12d48a"]
```

返回： 执行交易后的hash

示例：

```text
#请求
curl localhost:8546 -X POST -H "Content-Type:application/json" -d '{"jsonrpc":"2.0","method":"permission_disagree","params": ["enode://0922743d9409959e788a8bcdde1fbde9336709d72abdd9e681979551e6e171631f2f9459330dcb18922c14eacb5b507aba74a86a81832389c8618ca533019147@192.168.111.111:21001?discport=0",1,"0x51e766a7f073955c8061073bbba60b10bf12d48a"],"id":1}'

#结果
{"jsonrpc":"2.0","id":1,"result":["0xd79b8287a827e1387e6f0ff6d300fc663e10f592",""]}
```

---------------

### permission_findNotVoteRecordForMe

查找该节点是否为本次投过票

参数：

- from： 20字节，交易发起者地址

- stateKey： 字符串，申请者enodeId+opCode

```text
"param": ["0x51e766a7f073955c8061073bbba60b10bf12d48a","enode://0922743d9409959e788a8bcdde1fbde9336709d72abdd9e681979551e6e171631f2f9459330dcb18922c14eacb5b507aba74a86a81832389c8618ca533019147@192.168.111.111:21001?discport=01"]
```

返回： 本节点是否投过票

示例：

```text
#请求
curl localhost:8546 -X POST -H "Content-Type:application/json" -d '{"jsonrpc":"2.0","method": "permission_findNotVoteRecordForMe","param": ["0x51e766a7f073955c8061073bbba60b10bf12d48a","enode://0922743d9409959e788a8bcdde1fbde9336709d72abdd9e681979551e6e171631f2f9459330dcb18922c14eacb5b507aba74a86a81832389c8618ca533019147@192.168.111.111:21001?discport=01"],"id",1}'

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
"param": [3,"0x51e766a7f073955c8061073bbba60b10bf12d48a"]
```

返回： 对应类型的所有节点的enode

示例：

```text
#请求
curl localhost:8546 -X POST -H "Content-Type:application" -d '{"jsonrpc":"2.0", "method": "permission_getAllNode", "param": [3,"0x51e766a7f073955c8061073bbba60b10bf12d48a"],"id":1}'

#结果
{"jsonrpc": "2.0", "id":1, "result":["enode://0922743d9409959e788a8bcdde1fbde9336709d72abdd9e681979551e6e171631f2f9459330dcb18922c14eacb5b507aba74a86a81832389c8618ca533019147,enode://0922743d9409959e788a8bcdde1fbde9336709d72abdd9e681979551e6e171631f2f9459330dcb18922c14eacb5b507aba74a86a81832389c8618ca533019147",""]}
```

----------------

### permission_getAllStatingRecord

查询所有投票未完成的记录

参数：

- from： 20字节，交易发起者地址

```text
"param": "0x51e766a7f073955c8061073bbba60b10bf12d48a"
```

返回： 返回字符串（enodeId+opType），以逗号分割

示例：

```text
#请求
curl localhost:8546 -X POST -H "Content-Type:application" -d '{"jsonrpc":"2.0", "method": "permission_getAllStatingRecord","param": "0x51e766a7f073955c8061073bbba60b10bf12d48a" ,"id":1}'

#结果
{"jsonrpc": "2.0", "id":1, "result": "enode://0922743d9409959e788a8bcdde1fbde9336709d72abdd9e681979551e6e171631f2f9459330dcb18922c14eacb5b507aba74a86a81832389c8618ca5330191471，enode://0922743d9409959e788a8bcdde1fbde9336709d72abdd9e681979551e6e171631f2f9459330dcb18922c14eacb5b507aba74a86a81832389c8618ca5330191471",""}
```

### permission_getContractAddress

返回当前正在使用的合约地址

参数： 空

返回：返回当前正在使用的合约地址

示例：

```text
#请求
curl localhost:8546 -X POST -H "Content-Type:application" -d '{"jsonrpc":"2.0", "method": "permission_getContractAddress","params":[] ,"id":1}'

#结果
{"jsonrpc": "2.0", "id":1, "result": ["0x51e766a7f073955c8061073bbba60b10bf12d48a",""]}
```

------------

### permission_getNodeMap

获得节点的角色

