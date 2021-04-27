## 第6章 节点管理

### 搭建多机区块链网络

#### 第一步.生成区块链节点配置

本教程中，初始化共识节点ip为`192.168.4.242`，其他节点分别为`192.168.4.189` `192.168.4.66`，net端口为`21001~21003`，rpc端口为默认的`8545`，请保证端口未被占用

1.在`go-simpleger/cmd/consensus`目录下，通过`init_pbft.sh`脚本生成区块链节点文件

```shell
./init_pbft.sh --numNodes 3 --ip 192.168.4.242 192.168.4.189 192.168.4.66 
```

#### 第二步.拷贝区块链节点文件

生成区块链节点配置后，需要将每个节点配置拷贝到对应机器上，可通过scp命令执行拷贝，具体如下：

```shell
# 为每台机器创建操作目录~/simple
# (注: 这里使用ubuntu用户进行操作; 实际操作时，可使用自己的账户进行类似操作, IP也需要替换成自己的机器IP)
ssh ubuntu@192.168.4.242 "mkdir -p ~/simple"
ssh ubuntu@192.168.4.189 "mkdir -p ~/simple"
ssh ubuntu@192.168.4.66 "mkdir -p ~/simple"

# 拷贝节点配置，当前目录为go-simpledger/cmd/consensus
# 拷贝节点配置到192.168.4.242的~/simple路径
scp -r pbftdata/dd1/ ubuntu@192.168.4.242:~/simple/dd1
# 拷贝节点配置到192.168.4.189的~/simple路径
scp -r pbftdata/dd2/ ubuntu@192.168.4.189:~/simple/dd2
# 拷贝节点配置到192.168.4.66的~/simple路径
scp -r pbftdata/dd3/ ubuntu@192.168.4.66:~/simple/dd3
```

#### 第三步.启动节点
- 启动节点前，请确保已将simpledger的可执行文件添加到环境变量中。节点启动参数可查看[sipe介绍](tool.md)
- 使用`--nopermission`模式启动节点(节点之前连接不需要经过节点准入合约控制)
```shell
# (注: 这里使用ubuntu用户进行操作; 实际操作时，可使用自己的账户进行类似操作, IP也需要替换成自己的机器IP)
# 登录192.168.4.242并启动区块链节点
ssh ubuntu@192.168.4.242
cd simple
sipe --datadir=dd1 --port=21001 --rpc --rpcaddr="0.0.0.0" --rpcapi="eth,personal,permission,admin" --pbft.light --allow-insecure-unlock --mine --miner.gaslimit=20000000 --nopermission

# 登录192.168.4.189并启动区块链节点
ssh ubuntu@192.168.4.189
cd simple
sipe --datadir=dd2 --port=21002 --rpc --rpcaddr="0.0.0.0" --rpcapi="eth,personal,permission,admin" --pbft.light --allow-insecure-unlock --mine --miner.gaslimit=20000000 --nopermission

# 登录192.168.4.66并启动区块链节点
ssh ubuntu@192.168.4.66
cd simple
# kill掉之前已经启动的节点后重新启动
sipe --datadir=dd3 --port=21003 --rpc --rpcaddr="0.0.0.0" --rpcapi="eth,personal,permission,admin" --pbft.light --allow-insecure-unlock --mine --miner.gaslimit=20000000 --nopermission
```

### 节点准入配置

#### 第一步.节点信息配置

1.在`go-simpledger/cmd/permission`目录下修改`perm_script.sh`中的节点参数

- `commonTLSCrtDir`的值修改为`dd1 `
  
- `superSipeAddresses`的值修改为`(192.168.4.242:8545)`
  
- `obSipeAddresses`的值修改为`(192.168.4.189:8545 192.168.4.66:8545)`

修改后的结果如下：
```shell
#!/bin/bash
waitTime=5
commonTLSCrtDir=dd1/
superSipeAddresses=(192.168.4.242:8545)
obSipeAddresses=(192.168.4.189:8545 192.168.4.66:8545)
...
```

2.拷贝节点准入合约管理脚本
```shell
# 拷贝节点配置，当前目录为go-simpledger/cmd
# 拷贝节点配置到192.168.4.242的~/simple路径
scp -r permission ubuntu@192.168.4.242:~/simple/
```

3.为脚本添加执行权限
```shell
ssh ubuntu@192.168.4.242
# 进入permission目录
cd simple/permission
# 添加可执行权限
chmod u+x perm_script.sh 
chmod u+x perm_process.sh 
```

#### 第二步.部署节点准入合约

1.使用perm_script部署合约

在192.168.4.242节点部署合约，初始化的管理节点为192.168.4.242节点

```shell
# 当前服务器目录为ubuntu@192.168.4.242 ~/simple/permission
./perm_script.sh -m deployAccessContract -p ./code -g 20000000

# 出现以下内容表示合约部署成功
+ sipe attach http://192.168.4.242:8545 --datadir dd1/ --exec 'eth.getTransactionReceipt("0xf4f1f8462ed5ff946c638bb249142069d9b98e1ed4c6d955858a3bec06152b4b")'
{blockHash:"0x40147dc7c05c32efbef51ca0057706abb513b8bb6063d27bb17645ca0e3eabf0",blockNumber:1034,contractAddress:"0x81b1ea3e6da7bb6c06511b96cc4a1b3165cc0759",cumulativeGasUsed:13837678,from:"0x7295a4d8422b33fa2abb82059d281d49387aa6b5",gasUsed:13837678,logs:[],logsBloom:"0x00000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000",status:"0x1",to:null,transactionHash:"0xf4f1f8462ed5ff946c638bb249142069d9b98e1ed4c6d955858a3bec06152b4b",transactionIndex:0}
search success from chain
```
2.管理节点设置节点准入合约地址
在上文中，返回的节点准入合约地址为`0x81b1ea3e6da7bb6c06511b96cc4a1b3165cc0759`
```shell
# 当前服务器目录为ubuntu@192.168.4.242 ~/simple/permission
./perm_script.sh -m setContractAddress -c 0x81b1ea3e6da7bb6c06511b96cc4a1b3165cc0759
```

3.在合约中添加准入的节点
```shell
# 当前服务器目录为ubuntu@192.168.4.242 ~/simple/permission
./perm_script.sh -m addInitPeers
```

4.为非管理节点设置节点准入合约地址
```shell
# 当前服务器目录为ubuntu@192.168.4.242 ~/simple/permission
./perm_script.sh -m addPeersToSuperNode
```

#### 第三步.以节点准入模式重启所有节点
1.进入所有服务器并重启节点，重启时去除`--nopermission`启动参数
```shell
# (注: 这里使用ubuntu用户进行操作; 实际操作时，可使用自己的账户进行类似操作, IP也需要替换成自己的机器IP)
# 登录192.168.4.242并重启区块链节点
ssh ubuntu@192.168.4.242
cd simple
# kill掉之前已经启动的节点后重新启动
sipe --datadir=dd1 --port=21001 --rpc --rpcaddr="0.0.0.0" --rpcapi="eth,personal,permission,admin" --pbft.light --pbft.maxblocktxs=20000 --allow-insecure-unlock --mine 

# 登录192.168.4.189并重启区块链节点
ssh ubuntu@192.168.4.189
cd simple
# kill掉之前已经启动的节点后重新启动
sipe --datadir=dd2 --port=21002 --rpc --rpcaddr="0.0.0.0" --rpcapi="eth,personal,permission,admin" --pbft.light --pbft.maxblocktxs=20000 --allow-insecure-unlock --mine 

# 登录192.168.4.66并重启区块链节点
ssh ubuntu@192.168.4.66
cd simple
# kill掉之前已经启动的节点后重新启动
sipe --datadir=dd3 --port=21003 --rpc --rpcaddr="0.0.0.0" --rpcapi="eth,personal,permission,admin" --pbft.light --pbft.maxblocktxs=20000 --allow-insecure-unlock --mine 
```
2.查看各个节点是否与其余节点连接成功
```shell
# 查看192.168.4.242
sipe attach http://192.168.4.242:8545 --exec 'admin.peers'
# 查看192.168.4.189
sipe attach http://192.168.4.242:8545 --exec 'admin.peers'
# 查看192.168.4.66
sipe attach http://192.168.4.242:8545 --exec 'admin.peers'
```

### 加入新的节点

#### 第一步.通过已有genesis文件生成节点

本教程中，新节点对应机器的ip为`192.168.4.185`，net端口为`21004`，rpc端口为默认的`8545`，请保证端口未被占用

1.将`go-simpledger/cmd/consensus/pbftdata/nodekey`目录下的`genesis_pbft.json`拷贝到新节点的机器上
```shell
# (注: 这里使用ubuntu用户进行操作; 实际操作时，可使用自己的账户进行类似操作, IP也需要替换成自己的机器IP)
# 在192.168.4.185上创建目录~/simple
ssh ubuntu@192.168.4.185 "mkdir -p ~/simple"
# 进入cmd/consensus/pbftdata/nodekey
cd cmd/consensis/pbftdata/nodekey
# 拷贝genesis_pbft.json 
scp genesis_pbft.json ubuntu@192.168.4.185:~/simple
```

2.登录机器初始化节点
```shell
# (注: 这里使用ubuntu用户进行操作; 实际操作时，可使用自己的账户进行类似操作, IP也需要替换成自己的机器IP)
# 登录192.168.4.185初始化节点
ssh ubuntu@192.168.4.185
cd simple
# 初始化节点到dd4目录
sipe --datadir=dd4 init genesis_pbft.json
```

#### 第二步.以观察节点模式启动节点
1.在192.168.4.185机器中启动dd4节点，以观察节点模式启动（不添加`--mine`参数及pbft共识相关参数）
```shell
sipe --datadir=dd4 --port=21004 --rpc --rpcaddr="0.0.0.0" --rpcapi="eth,personal,permission,admin" --allow-insecure-unlock
```
2.导入节点私钥到keystore（本教程中默认密码为空）
```shell
sipe --datadir=dd4 account import dd4/sipe/nodekey
```

#### 第三步.在节点准入合约中批准此节点加入

1.使用`sipe`查看新节点的node信息
```shell
sipe attach http://192.168.4.185:8545 --exec "admin.nodeInfo"
# 返回结果中
{
  address: "0x4bee77698C54D9119009486B83ae64E7714Adf65",
  enode: "enode://55dbca4836be9df4ee1fcd7ca15dc0ca6bcf454afaf08bef28106818be12ee58c32e66534594e4d18eb7480bcfd609c0649571a034c0dfc5114b9b751dd77066@192.168.4.185:21004",
  enr: "enr:-Je4QKJQItcfKavJ6nvmEuVZT2tcYlOkOuaqXyZL39KD7wD-FoziAcHTrpeF3_CPTQlzeEw-gQO3ginDpdcHTYKpO_oFg2V0aMfGhAr3cm2AgmlkgnY0gmlwhLeBkoWJc2VjcDI1NmsxoQJV28pINr6d9O4fzXyhXcDKa89FSvrwi-8oEGgYvhLuWIN0Y3CCUgyDdWRwglIM",
  id: "e6fece3b2e04675ec88599ac4bee77698c54d9119009486b83ae64e7714adf65",
  ip: "183.129.146.133",
  listenAddr: "[::]:21004",
  name: "Sipe/v1.0.0-candidate/linux-amd64/go1.13.15",
  ports: {
    discovery: 21004,
    listener: 21004
  },
  protocols: {
    simd: {
      config: {
        chainId: 10388,
        pbft: {...},
        singularityBlock: 0,
        stateType: "mpt"
      },
      difficulty: 1,
      genesis: "0x529ff79538722c98ba9e664060fa4834a7b897e0d32391aa29da0f0ffe38f542",
      head: "0x529ff79538722c98ba9e664060fa4834a7b897e0d32391aa29da0f0ffe38f542",
      network: 10388
    }
  }
}
```

2.在初始化的管理节点对应的192.168.4.242机器上批准信节点加入
```shell
# (注: 这里使用ubuntu用户进行操作; 实际操作时，可使用自己的账户进行类似操作, IP也需要替换成自己的机器IP)
# 登录192.168.4.242
ssh ubuntu@192.168.4.242
cd simple
# 解锁管理员账户
sipe attach http://192.168.4.242:8545 --exec "personal.unlockAccount(eth.accounts[0], \"\", 600)"
# 添加新加入节点信息
sipe attach http://192.168.4.242:8545 --exec "permission.addNewNodeApply(\"enode://55dbca4836be9df4ee1fcd7ca15dc0ca6bcf454afaf08bef28106818be12ee58c32e66534594e4d18eb7480bcfd609c0649571a034c0dfc5114b9b751dd77066@192.168.4.185:21004\",\"node4\",eth.accounts[0])"
# 查看是否添加成功
sipe attach http://192.168.4.242:8545 --exec "permission.getAllNode(1,eth.accounts[0])"
# 成功的返回结果
"enode://55dbca4836be9df4ee1fcd7ca15dc0ca6bcf454afaf08bef28106818be12ee58c32e66534594e4d18eb7480bcfd609c0649571a034c0dfc5114b9b751dd77066,"
# 投票确认新加入的节点信息
sipe attach http://192.168.4.242:8545 --exec "permission.verifyAddNodeApply(\"enode://55dbca4836be9df4ee1fcd7ca15dc0ca6bcf454afaf08bef28106818be12ee58c32e66534594e4d18eb7480bcfd609c0649571a034c0dfc5114b9b751dd77066@192.168.4.185:21004\",eth.accounts[0])"
# 查看节点通过验证，成为观察节点
sipe attach http://192.168.4.242:8545 --exec "permission.getAllNode(1,eth.accounts[0])"
# 成功的返回结果
""
```

#### 第四步.在新节点上设置合约地址并向其他节点同步区块
```shell
# (注: 这里使用ubuntu用户进行操作; 实际操作时，可使用自己的账户进行类似操作, IP也需要替换成自己的机器IP)
# 登录192.168.4.185
ssh ubuntu@192.168.4.185
cd simple
# 设置节点准入合约地址
sipe attach http://192.168.4.185:8545 --exec "permission.setContractAddress(\"0x38d11DBeE2cbf39BC6F7654085E7a2D4ccfA3093\")"
# 查看确认设置成功
sipe attach http://192.168.4.185:8545 --exec "permission.getContractAddress()"
# 连接其他节点
# 连接192.168.4.242
sipe attach http://192.168.4.185:8545 --exec "admin.addPeer(\"enode://7abc413fce75c301a7536457db0bdbde5001ad4b6b2cb89e98657294b5c6fdb74c7a5426c94d80e4529d6c3f80f8d5314b89fb4039678f10a92fa5084f221e1b@192.168.4.242:21001\")"
# 连接192.168.4.189
sipe attach http://192.168.4.185:8545 --exec "admin.addPeer(\"enode://aea0c32fb3669089a941a519ab7aaca641b34e63e4ff5918c0a6092736e24008b9739c31f1aae528b7ac53028b130b4e8cb1661122f2a8c70e24b977034e4a11@192.168.4.189:21002\")"
# 连接192.168.4.66
sipe attach http://192.168.4.185:8545 --exec "admin.addPeer(\"enode://d91a99f05a139b32e196b10ef63a97526d6a1c546d35f19a8c3d6b764ba99e5c7eb0bfe3b43eaa56e75b073cc65f585af651c04b6b08d9c8c78c9cc39fc9ab3f@192.168.4.66:21003\")"
```

#### 第五步.为新节点添加余额
1.查看新节点地址
```shell
# 登录192.168.4.185
sipe attach http://192.168.4.185:8545 --exec "admin.nodeInfo.address"
# 返回结果
"0x4bee77698C54D9119009486B83ae64E7714Adf65"
```
2.通过原有节点向新节点转账
```shell
# 登录192.168.4.242
ssh ubuntu@192.168.4.242
cd simple
# 解锁账户
sipe attach dd1/sipe.ipc --exec="personal.unlockAccount(eth.accounts[0],'')"
# 发起转账交易
sipe attach dd1/sipe.ipc --exec "eth.sendTransaction(from:eth.accounts[0],to:\"0x4bee77698C54D9119009486B83ae64E7714Adf65\",value:1e20)"
```

### 共识节点管理

#### 选举新的共识节点

本教程中，将上文新加入的节点即ip为`192.168.4.185`的观察节点选举为新的共识节点

1.共识节点参与选举

分别在`192.168.4.242` `192.168.4.189`机器上选举新节点为共识节点
```shell
# (注: 这里使用ubuntu用户进行操作; 实际操作时，可使用自己的账户进行类似操作, IP也需要替换成自己的机器IP)
# 登录192.168.4.242
ssh ubuntu@192.168.4.242
cd simple
# 通过enodeid选举
sipe attach dd1/sipe.ipc --exec "pbft.proposeEnode('55dbca4836be9df4ee1fcd7ca15dc0ca6bcf454afaf08bef28106818be12ee58c32e66534594e4d18eb7480bcfd609c0649571a034c0dfc5114b9b751dd77066',true)"
# 登录192.168.4.189
ssh ubuntu@192.168.4.189
cd simple
# 通过enodeid选举
sipe attach dd2/sipe.ipc --exec "pbft.proposeEnode('55dbca4836be9df4ee1fcd7ca15dc0ca6bcf454afaf08bef28106818be12ee58c32e66534594e4d18eb7480bcfd609c0649571a034c0dfc5114b9b751dd77066',true)"
# 此时已有2/3节点参与选举，新节点已成为共识节点
```

2.通过共识模式重启`192.168.4.185`的节点
```shell
# (注: 这里使用ubuntu用户进行操作; 实际操作时，可使用自己的账户进行类似操作, IP也需要替换成自己的机器IP)
# 登录192.168.4.185并重启区块链节点
ssh ubuntu@192.168.4.185
cd simple
# kill掉之前已经启动的节点后重新启动
sipe --datadir=dd4 --port=21004 --rpc --rpcaddr="0.0.0.0" --rpcapi="eth,personal,permission,admin" --pbft.light --pbft.maxblocktxs=20000 --allow-insecure-unlock --mine 
```

#### 投票剔除共识节点

本教程中，将ip为`192.168.4.66`的共识节点剔除为观察节点

1.共识节点投票
分别在`192.168.4.242` `192.168.4.189` `192.168.4.185`机器上进行投票
```shell
# (注: 这里使用ubuntu用户进行操作; 实际操作时，可使用自己的账户进行类似操作, IP也需要替换成自己的机器IP)
# 登录192.168.4.242
ssh ubuntu@192.168.4.242
cd simple
# 通过enodeid投票剔除
sipe attach dd1/sipe.ipc --exec "pbft.proposeEnode('d91a99f05a139b32e196b10ef63a97526d6a1c546d35f19a8c3d6b764ba99e5c7eb0bfe3b43eaa56e75b073cc65f585af651c04b6b08d9c8c78c9cc39fc9ab3f',false)"
# 登录192.168.4.189
ssh ubuntu@192.168.4.189
cd simple
# 通过enodeid投票剔除
sipe attach dd2/sipe.ipc --exec "pbft.proposeEnode('d91a99f05a139b32e196b10ef63a97526d6a1c546d35f19a8c3d6b764ba99e5c7eb0bfe3b43eaa56e75b073cc65f585af651c04b6b08d9c8c78c9cc39fc9ab3f',false)"
# 登录192.168.4.185
ssh ubuntu@192.168.4.185
cd simple
# 通过enodeid投票剔除
sipe attach dd4/sipe.ipc --exec "pbft.proposeEnode('d91a99f05a139b32e196b10ef63a97526d6a1c546d35f19a8c3d6b764ba99e5c7eb0bfe3b43eaa56e75b073cc65f585af651c04b6b08d9c8c78c9cc39fc9ab3f',false)"
# 此时已有2/3节点参与选举，共识节点已被剔除
```

2.观察节点停止出块

原共识节点被投票剔除后已没有出块权限，提交新区块时会出现以下错误日志
```shell
INFO [04-25|07:28:22.314] Commit new byzantium work                number=13636 sealhash=f82b8d…492b83 elapsed=132.557µs    maxTxsCanSeal=250
WARN [04-25|07:28:22.314] Block sealing failed                     err=unauthorized
```

因此，停止该节点的出块操作
```text
# 登录192.168.4.66
ssh ubuntu@192.168.4.66
cd simple
# 停止出块
sipe attach dd3/sipe.ipc --exec "miner.stop()"
```

当此节点重新启动时，以观察模式启动，不添加`--mine`启动参数
```shell
sipe --datadir=dd3 --port=21003 --rpc --rpcaddr="0.0.0.0" --rpcapi="eth,personal,permission,admin" --ws --allow-insecure-unlock
```

### 节点权限管理

#### 节点升级为管理节点
本教程中，将上文新加入的节点即ip为`192.168.4.185`的节点选举为新的管理节点

1.管理节点提议升级新节点
```shell
# (注: 这里使用ubuntu用户进行操作; 实际操作时，可使用自己的账户进行类似操作, IP也需要替换成自己的机器IP)
# 登录192.168.4.242
ssh ubuntu@192.168.4.242
# 进入permission目录
cd simple/permission
# 发起提议交易
./perm_script.sh -m applyByAdmin  -u "http://192.168.4.242:8545" -e "enode://55dbca4836be9df4ee1fcd7ca15dc0ca6bcf454afaf08bef28106818be12ee58c32e66534594e4d18eb7480bcfd609c0649571a034c0dfc5114b9b751dd77066@192.168.4.185:21004" -r 2
```

2.管理节点对节点升级提案进行投票
```shell
./perm_script.sh -m nodeUpgradeVerify -u "http://192.168.4.242:8545" -e "enode://55dbca4836be9df4ee1fcd7ca15dc0ca6bcf454afaf08bef28106818be12ee58c32e66534594e4d18eb7480bcfd609c0649571a034c0dfc5114b9b751dd77066@192.168.4.185:21004"
```

3.查看确认节点升级成功
```shell
# 进入simple目录
cd ~/simple
sipe attach dd1/sipe.ipc --exec "permission.getNodeMap(\"enode://55dbca4836be9df4ee1fcd7ca15dc0ca6bcf454afaf08bef28106818be12ee58c32e66534594e4d18eb7480bcfd609c0649571a034c0dfc5114b9b751dd77066\", eth.accounts[0])"
# 返回结果中role为2则表示该节点成功升级为管理节点
"role:2, url:enode://55dbca4836be9df4ee1fcd7ca15dc0ca6bcf454afaf08bef28106818be12ee58c32e66534594e4d18eb7480bcfd609c0649571a034c0dfc5114b9b751dd77066@192.168.4.185:21004"
```

#### 节点降级为普通节点
本教程中，将ip为`192.168.4.185`的节点降级为普通节点

1.管理节点提议降级节点
```shell
# (注: 这里使用ubuntu用户进行操作; 实际操作时，可使用自己的账户进行类似操作, IP也需要替换成自己的机器IP)
# 登录192.168.4.242
ssh ubuntu@192.168.4.242
# 进入permission目录
cd simple/permission
# 发起提议交易
./perm_script.sh -m applyByAdmin -u "http://192.168.4.242:8545" -e "enode://55dbca4836be9df4ee1fcd7ca15dc0ca6bcf454afaf08bef28106818be12ee58c32e66534594e4d18eb7480bcfd609c0649571a034c0dfc5114b9b751dd77066@192.168.4.185:21004" -r 3
```


2.管理节点对节点降级提案进行投票
```shell
# 192.168.4.242节点投票
./perm_script.sh -m nodeDowngradeVerify -u "http://192.168.4.242:8545" -e "enode://55dbca4836be9df4ee1fcd7ca15dc0ca6bcf454afaf08bef28106818be12ee58c32e66534594e4d18eb7480bcfd609c0649571a034c0dfc5114b9b751dd77066@192.168.4.185:21004"

# 192.168.4.185节点投票
./perm_script.sh -m nodeDowngradeVerify -u "http://192.168.4.185:8545" -e "enode://55dbca4836be9df4ee1fcd7ca15dc0ca6bcf454afaf08bef28106818be12ee58c32e66534594e4d18eb7480bcfd609c0649571a034c0dfc5114b9b751dd77066@192.168.4.185:21004"
```

3.查看确认节点降级成功
```shell
# 进入simple目录
cd ~/simple
sipe attach dd1/sipe.ipc --exec "permission.getNodeMap(\"enode://55dbca4836be9df4ee1fcd7ca15dc0ca6bcf454afaf08bef28106818be12ee58c32e66534594e4d18eb7480bcfd609c0649571a034c0dfc5114b9b751dd77066\", eth.accounts[0])"
# 返回结果中role0则表示该节点降级成功
"role:0, url:enode://55dbca4836be9df4ee1fcd7ca15dc0ca6bcf454afaf08bef28106818be12ee58c32e66534594e4d18eb7480bcfd609c0649571a034c0dfc5114b9b751dd77066@192.168.4.185:21004"
```

#### 节点退出系统
本教程中，将将ip为`192.168.4.66`的节点降级为普通节点（注：节点在离开网络前，必须降级为非管理节点）

1.管理节点提议剔除节点
```shell
# (注: 这里使用ubuntu用户进行操作; 实际操作时，可使用自己的账户进行类似操作, IP也需要替换成自己的机器IP)
# 登录192.168.4.242
ssh ubuntu@192.168.4.242
# 进入permission目录
cd simple/permission
# 发起提议交易
./perm_script.sh -m applyByAdmin -u "http://192.168.4.242:8545" -e "enode://d91a99f05a139b32e196b10ef63a97526d6a1c546d35f19a8c3d6b764ba99e5c7eb0bfe3b43eaa56e75b073cc65f585af651c04b6b08d9c8c78c9cc39fc9ab3f@192.168.4.66:21003" -r 1
```


2.管理节点对节点剔除提案进行投票
```shell
./perm_script.sh -m addNodeToBlackVerify -u "http://192.168.4.242:8545" -e "enode://d91a99f05a139b32e196b10ef63a97526d6a1c546d35f19a8c3d6b764ba99e5c7eb0bfe3b43eaa56e75b073cc65f585af651c04b6b08d9c8c78c9cc39fc9ab3f@192.168.4.66:21003"
```

3.查看确认节点降级成功
```shell
# 进入simple目录
cd ~/simple
sipe attach dd1/sipe.ipc --exec "permission.getNodeMap(\"enode://d91a99f05a139b32e196b10ef63a97526d6a1c546d35f19a8c3d6b764ba99e5c7eb0bfe3b43eaa56e75b073cc65f585af651c04b6b08d9c8c78c9cc39fc9ab3f\", eth.accounts[0])"
# 返回结果中role1则表示该节点剔除成功
"role:1, url:enode://d91a99f05a139b32e196b10ef63a97526d6a1c546d35f19a8c3d6b764ba99e5c7eb0bfe3b43eaa56e75b073cc65f585af651c04b6b08d9c8c78c9cc39fc9ab3f@192.168.4.66:21003"
```