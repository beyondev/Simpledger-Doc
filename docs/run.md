## 第5章 快速开始

### Docker镜像搭建

#### 第一步.获取镜像


#### 第二步.启动节点

#### 注意：dd1 ob1 ob2 已升级为共识节点。

```shell
docker run --name sipe  -v /var/sipe/ob1/:/pbftdata/ob1/log/ -v /var/sipe/ob2/:/pbftdata/ob2/log/ -v /var/sipe/ob3/:/pbftdata/ob3/log/ -p 9546:9546 -p 6541:6541 -p 6542:6542 -p 6543:6543 sipe:1.0
```

#### 第三步，查看日志

#### 注意： dd1 ob1 ob2 已升级为共识节点
dd1节点：
```shell
docker logs -f sipe
```

ob1节点：
```shell
tail -f /var/sipe/ob1/ob.log
```

ob2节点：
```shell
tail -f /var/sipe/ob2/ob.log
```

ob3节点：
```shell
tail -f /var/sipe/ob3/ob.log
```

#### 第四步.开启控制台

#### 注意： dd1 ob1 ob2 已升级为共识节点
dd1节点：
```shell
sipe attach http://localhost:9546
```

ob1节点：
```shell
sipe attach http://localhost:6541
```

ob2节点：

```shell
sipe attach http://localhost:6542
```

ob3节点：
```shell
sipe attach http://localhost:6543
```


### 源码搭建

#### 第一步.下载源码
通过 git 将项目 clone 到本地
```shell
git clone http://git.dataqin.com/simpledger/go-simpledger.git
```

#### 第二步. 编译
1.进入go-simpledger根目录
```shell
  cd go-simpledger
```

2.使用make工具编译安装
```shell
  make all
```


#### 第三步. 添加环境变量
将go-simpledger根目录下`build/bin`目录添加到系统环境变量中，并核对sipe版本是否正确
```shell
sipe version
```

### 本地搭建4节点联盟链
在go-simpledger下`cmd/consensus`目录执行下面的指令，生成一条4节点的联盟链。请确保机器的`9546` `21001` `6541~6543`以及`31041~31043`端口没有被其他程序占用。

#### 第一步.生成节点文件
1.在go-simpledger下根目录进入`cmd/consensus`目录
```shell
cd cmd/consensus
```
2.使用`init_pbft_fast.sh`生成节点文件
```shell
./init_pbft_fast.sh --numNodes 1 --numObNodes 3
```
3.通过`ps`指令查看sipe进程是否全部启动
```shell
ps -ef | grep sipe
```
- 看到4个sipe进程则说明节点均启动成功
   

#### 第二步.部署节点准入合约
1.在go-simpledger下根目录进入`cmd/permission`目录
```shell
cd cmd/permission
```
2.部署合约
```shell 
./perm_script.sh -m deployAccessContract -p  code -g 20000000
```
3.设置合约地址
```shell
./perm_script.sh -m setContractAddress -c <上一步生成的合约地址> 
```

#### 第三步.准入节点
1.添加节点信息，并将游离节点选举为观察节点
```shell
./perm_script.sh -m addInitPeers
```
2.观察节点连接共识节点并同步账本
```shell
./perm_script.sh -m addPeersToSuperNode
```

#### 第四步.查看日志信息
在`go-simpledger/cmd/consensus`目录下查看日志

- 查看共识节点
```shell
tail -f pbftdata/dd1/log/super.log
```
- 查看观察节点ob1
```shell
tail -f pbftdata/ob1/log/ob.log
```
- 查看观察节点ob2
```shell
tail -f pbftdata/ob2/log/ob.log
```
- 查看观察节点ob3
```shell
tail -f pbftdata/ob3/log/ob.log
```
