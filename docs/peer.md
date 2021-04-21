## 第6章 节点管理

### 搭建多机区块链网络

#### 第一步.生成区块链节点配置

本教程中，初始化共识节点ip为`192.168.4.242`，其他节点分别为`192.168.4.189` `192.168.4.66` `192.168.4.185`，net端口为`21001~21004`

1.在`go-simpleger/cmd/consensus`目录下，通过`init_pbft.sh`脚本生成区块链节点文件

```shell
./init_pbft.sh --numNodes 4 --ip 192.168.4.242 192.168.4.189 192.168.4.66 192.168.4.185
```

#### 第二步.拷贝区块链节点文件

生成区块链节点配置后，需要将每个节点配置拷贝到对应机器上，可通过scp命令执行拷贝，具体如下：

```shell
# 为每台机器创建操作目录~/simple
# (注: 这里使用ubuntu用户进行操作; 实际操作时，可使用自己的账户进行类似操作, IP也需要替换成自己的机器IP)
ssh ubuntu@192.168.4.242 "mkdir -p ~/simple"
ssh ubuntu@192.168.4.189 "mkdir -p ~/simple"
ssh ubuntu@192.168.4.66 "mkdir -p ~/simple"
ssh ubuntu@192.168.4.185 "mkdir -p ~/simple"

# 拷贝节点配置，当前目录为go-simpledger/cmd/consensus
# 拷贝节点配置到192.168.4.242的~/simple路径
scp -r pbftdata/dd1/ ubuntu@192.168.4.242:~/simple/dd1
# 拷贝节点配置到192.168.4.189的~/simple路径
scp -r pbftdata/dd2/ ubuntu@192.168.4.189:~/simple/dd2
# 拷贝节点配置到192.168.4.66的~/simple路径
scp -r pbftdata/dd3/ ubuntu@192.168.4.66:~/simple/dd3
# 拷贝节点配置到192.168.4.185的~/simple路径
scp -r pbftdata/dd4/ ubuntu@192.168.4.185:~/simple/dd4
```

#### 第三步.启动节点
- 启动节点前，请确保已将simpledger的可执行文件添加到环境变量中。节点启动参数可查看[sipe介绍](tool.md)
- 使用`nopermission`模式启动节点(节点之前连接不需要经过节点准入合约控制)
```shell
# (注: 这里使用ubuntu用户进行操作; 实际操作时，可使用自己的账户进行类似操作, IP也需要替换成自己的机器IP)
# 登录192.168.4.242并启动区块链节点
ssh ubuntu@192.168.4.242
cd simple
sipe --datadir=dd1 --port=21001 --rpc --ws --pbft.light --pbft.maxblocktxs=20000 --allow-insecure-unlock --mine --nopermission

# 登录192.168.4.189并启动区块链节点
ssh ubuntu@192.168.4.189
cd simple
sipe --datadir=dd2 --port=21002 --rpc --ws --pbft.light --pbft.maxblocktxs=20000 --allow-insecure-unlock --mine --nopermission

# 登录192.168.4.66并启动区块链节点
ssh ubuntu@192.168.4.66
cd simple
sipe --datadir=dd3 --port=21003 --rpc --ws --pbft.light --pbft.maxblocktxs=20000 --allow-insecure-unlock --mine --nopermission

# 登录192.168.4.185并启动区块链节点
ssh ubuntu@192.168.4.185
cd simple
sipe --datadir=dd4 --port=21004 --rpc --ws --pbft.light --pbft.maxblocktxs=20000 --allow-insecure-unlock --mine --nopermission
```

### 节点准入配置

#### 第一步.节点信息配置

1.在`go-simpledger/cmd/permission`目录下修改`perm_script.sh`中的节点参数

- `commonTLSCrtDir`的值修改为`dd1 `
  
- `superSipeAddresses`的值修改为`(192.168.4.242:8545)`
  
- `obSipeAddresses`的值修改为`(192.168.4.189:8545 192.168.4.66:8545 192.168.4.185:8545)`

修改后的结果如下：
```shell
#!/bin/bash
waitTime=5
commonTLSCrtDir=dd1/
superSipeAddresses=(192.168.4.242:8545)
obSipeAddresses=(192.168.4.189:8545 192.168.4.66:8545 192.168.4.185:8545)
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
1.进入所有服务器并重启节点，重启时去除`nopermission`启动参数
```shell
# (注: 这里使用ubuntu用户进行操作; 实际操作时，可使用自己的账户进行类似操作, IP也需要替换成自己的机器IP)
# 登录192.168.4.242并重启区块链节点
ssh ubuntu@192.168.4.242
cd simple
# kill掉之前已经启动的节点后重新启动
sipe --datadir=dd1 --port=21001 --rpc --ws --pbft.light --pbft.maxblocktxs=20000 --allow-insecure-unlock --mine 

# 登录192.168.4.189并启动区块链节点
ssh ubuntu@192.168.4.189
cd simple
# kill掉之前已经启动的节点后重新启动
sipe --datadir=dd2 --port=21002 --rpc --ws --pbft.light --pbft.maxblocktxs=20000 --allow-insecure-unlock --mine 

# 登录192.168.4.66并启动区块链节点
ssh ubuntu@192.168.4.66
cd simple
# kill掉之前已经启动的节点后重新启动
sipe --datadir=dd3 --port=21003 --rpc --ws --pbft.light --pbft.maxblocktxs=20000 --allow-insecure-unlock --mine 

# 登录192.168.4.185并启动区块链节点
ssh ubuntu@192.168.4.185
cd simple
# kill掉之前已经启动的节点后重新启动
sipe --datadir=dd4 --port=21004 --rpc --ws --pbft.light --pbft.maxblocktxs=20000 --allow-insecure-unlock --mine 
```
2.查看各个节点是否与其余节点连接成功
```shell
# 查看192.168.4.242
sipe attach http://192.168.4.242:8545 --exec 'admin.peers'
# 查看192.168.4.189
sipe attach http://192.168.4.242:8545 --exec 'admin.peers'
# 查看192.168.4.66
sipe attach http://192.168.4.242:8545 --exec 'admin.peers'
# 查看192.168.4.185
sipe attach http://192.168.4.242:8545 --exec 'admin.peers'
```

### 加入新的节点
