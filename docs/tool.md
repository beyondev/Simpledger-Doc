## 第7章 实用工具

### Simpledger节点客户端sipe
`sipe`是Simpledger官方提供的节点客户端，sipe的启动参数如下：
```shell
COMMANDS:
   account                            Manage accounts
   attach                             Start an interactive JavaScript environment (connect to node)
   console                            Start an interactive JavaScript environment
   dumpconfig                         Show configuration values
   export-preimages                   Export the preimage database into an RLP stream
   import-preimages                   Import the preimage database from an RLP stream
   init                               Bootstrap and initialize a new genesis block
   js                                 Execute the specified JavaScript files
   license                            Display license information
   removedb                           Remove blockchain and state databases
   retesteth                          Launches geth in retesteth mode
   version                            Print version numbers
   wallet                             Manage Simpledger presale wallets
   help, h                            Shows a list of commands or help for one command

SIMPLECHAIN OPTIONS:
  --config value                      TOML configuration file
  --datadir value                     Data directory for the databases and keystore (default: "/Users/yuanchao/Library/Simplechain")
  --datadir.ancient value             Data directory for ancient chain segments (default = inside chaindata)
  --keystore value                    Directory for the keystore (default = inside the datadir)
  --nousb                             Disables monitoring for and managing USB hardware wallets
  --pcscdpath value                   Path to the smartcard daemon (pcscd) socket file
  --networkid value                   Network identifier (integer, 1=Mainnet, 3=Testnet (default: 1)
  --testnet                           Ropsten network: pre-configured proof-of-work test network
  --syncmode value                    Blockchain sync mode ("fast", "full", or "light") (default: full)
  --exitwhensynced                    Exits after block synchronisation completes
  --gcmode value                      Blockchain garbage collection mode ("full", "archive") (default: "full")
  --ethstats value                    Reporting URL of a ethstats service (nodename:secret@host:port)
  --identity value                    Custom node name
  --whitelist value                   Comma separated block number-to-hash mappings to enforce (<number>=<hash>)

TRANSACTION POOL OPTIONS:
  --txpool.locals value               Comma separated accounts to treat as locals (no flush, priority inclusion)
  --txpool.nolocals                   Disables price exemptions for locally submitted transactions
  --txpool.journal value              Disk journal for local transaction to survive node restarts
  --txpool.rejournal value            Time interval to regenerate the local transaction journal (default: 1h0m0s)
  --txpool.pricelimit value           Minimum gas price limit to enforce for acceptance into the pool (default: 1)
  --txpool.globalslots value          Maximum number of executable transaction slots for all accounts (default: 65536)

PERFORMANCE TUNING OPTIONS:
  --cache value                       Megabytes of memory allocated to internal caching (default = 4096 mainnet full node, 128 light mode) (default: 1024)
  --cache.database value              Percentage of cache memory allowance to use for database io (default: 50)
  --cache.trie value                  Percentage of cache memory allowance to use for trie caching (default = 25% full mode, 50% archive mode) (default: 25)
  --cache.gc value                    Percentage of cache memory allowance to use for trie pruning (default = 25% full mode, 0% archive mode) (default: 25)
  --cache.noprefetch                  Disable heuristic state prefetch during block import (less CPU and disk IO, more time waiting for data)
  --storage.cachesize value           Size of storage key-value cache (default: 4096)
  --storage.nocache                   Disables storage cache

ACCOUNT OPTIONS:
  --unlock value                      Comma separated list of accounts to unlock
  --password value                    Password file to use for non-interactive password input
  --signer value                      External signer (url or path to ipc file)
  --allow-insecure-unlock             Allow insecure account unlocking when account-related RPCs are exposed by http

API AND CONSOLE OPTIONS:
  --ipcdisable                        Disable the IPC-RPC server
  --ipcpath value                     Filename for IPC socket/pipe within the datadir (explicit paths escape it)
  --rpc                               Enable the HTTP-RPC server
  --rpcaddr value                     HTTP-RPC server listening interface (default: "localhost")
  --rpcport value                     HTTP-RPC server listening port (default: 8545)
  --rpcapi value                      API's offered over the HTTP-RPC interface
  --rpc.gascap value                  Sets a cap on gas that can be used in eth_call/estimateGas (default: 0)
  --rpccorsdomain value               Comma separated list of domains from which to accept cross origin requests (browser enforced)
  --rpcvhosts value                   Comma separated list of virtual hostnames from which to accept requests (server enforced). Accepts '*' wildcard. (default: "localhost")
  --ws                                Enable the WS-RPC server
  --wsaddr value                      WS-RPC server listening interface (default: "localhost")
  --wsport value                      WS-RPC server listening port (default: 8546)
  --wsapi value                       API's offered over the WS-RPC interface
  --wsorigins value                   Origins from which to accept websockets requests
  --jspath loadScript                 JavaScript root path for loadScript (default: ".")
  --exec value                        Execute JavaScript statement
  --preload value                     Comma separated list of JavaScript files to preload into the console

NETWORKING OPTIONS:
  --bootnodes value                   Comma separated enode URLs for P2P discovery bootstrap (set v4+v5 instead for light servers)
  --bootnodesv4 value                 Comma separated enode URLs for P2P v4 discovery bootstrap (light server, full nodes)
  --bootnodesv5 value                 Comma separated enode URLs for P2P v5 discovery bootstrap (light server, light nodes)
  --port value                        Network listening port (default: 30312)
  --maxpeers value                    Maximum number of network peers (network disabled if set to 0) (default: 25)
  --maxpendpeers value                Maximum number of pending connection attempts (defaults used if set to 0) (default: 0)
  --nat value                         NAT port mapping mechanism (any|none|upnp|pmp|extip:<IP>) (default: "any")
  --discover                          Enables the peer discovery mechanism (manual peer addition)
  --v5disc                            Enables the experimental RLPx V5 (Topic Discovery) mechanism
  --netrestrict value                 Restricts network communication to the given IP networks (CIDR masks)
  --nodekey value                     P2P node key file
  --nodekeyhex value                  P2P node key as hex (for testing)
  --nopermission                      Disables the peer permission checking

MINER OPTIONS:
  --mine                              Enable mining
  --miner.threads value               Number of CPU threads to use for mining (default: 0)
  --miner.notify value                Comma separated HTTP URL list to notify of new work packages
  --miner.gasprice value              Minimum gas price for mining a transaction (default: 1000000000)
  --miner.gaslimit value              Target gas ceiling for mined blocks (default: 8000000)
  --miner.etherbase value             Public address for block mining rewards (default = first account) (default: "0")
  --miner.extradata value             Block extra data set by the miner (default = client version)
  --miner.recommit value              Time interval to recreate the block being mined (default: 3s)
  --miner.noverify                    Disable remote sealing verification

GAS PRICE ORACLE OPTIONS:
  --gpoblocks value                   Number of recent blocks to check for gas prices (default: 20)
  --gpopercentile value               Suggested gas price is the given percentile of a set of recent transaction gas prices (default: 60)

VIRTUAL MACHINE OPTIONS:
  --vmdebug                           Record information useful for VM and contract debugging
  --vm.evm value                      External EVM configuration (default = built-in interpreter)
  --vm.ewasm value                    External ewasm configuration (default = built-in interpreter)

LOGGING AND DEBUGGING OPTIONS:
  --fakepow                           Disables proof-of-work verification
  --nocompaction                      Disables db compaction after import
  --verbosity value                   Logging verbosity: 0=silent, 1=error, 2=warn, 3=info, 4=debug, 5=detail (default: 3)
  --vmodule value                     Per-module verbosity: comma-separated list of <pattern>=<level> (e.g. eth/*=5,p2p=4)
  --backtrace value                   Request a stack trace at a specific logging statement (e.g. "block.go:271")
  --debug                             Prepends log messages with call-site location (file and line number)
  --pprof                             Enable the pprof HTTP server
  --pprofaddr value                   pprof HTTP server listening interface (default: "127.0.0.1")
  --pprofport value                   pprof HTTP server listening port (default: 6060)
  --memprofilerate value              Turn on memory profiling with the given rate (default: 524288)
  --blockprofilerate value            Turn on block profiling with the given rate (default: 0)
  --cpuprofile value                  Write CPU profile to the given file
  --trace value                       Write execution trace to the given file

METRICS AND STATS OPTIONS:
  --metrics                           Enable metrics collection and reporting
  --metrics.expensive                 Enable expensive metrics collection and reporting
  --metrics.influxdb                  Enable metrics export/push to an external InfluxDB database
  --metrics.influxdb.endpoint value   InfluxDB API endpoint to report metrics to (default: "http://localhost:8086")
  --metrics.influxdb.database value   InfluxDB database name to push reported metrics to (default: "geth")
  --metrics.influxdb.username value   Username to authorize access to the database (default: "test")
  --metrics.influxdb.password value   Password to authorize access to the database (default: "test")
  --metrics.influxdb.tags value       Comma-separated InfluxDB tags (key/values) attached to all measurements (default: "host=localhost")

WHISPER (EXPERIMENTAL) OPTIONS:
  --shh                               Enable Whisper
  --shh.maxmessagesize value          Max message size accepted (default: 1048576)
  --shh.pow value                     Minimum POW accepted (default: 0.2)
  --shh.restrict-light                Restrict connection between two whisper light clients

PBFT OPTIONS:
  --pbft.requesttimeout value         Timeout for each pbft round in milliseconds (default: 5000)
  --pbft.blockperiod value            Default minimum difference between two consecutive block's timestamps in seconds (default: 1)
  --pbft.light                        Enable send and receive light block
  --pbft.maxblocktxs value            Default max txs one block can seal (default: 1000)

MISC OPTIONS:
  --override.singularity value        Manually specify singularity fork-block, overriding the bundled setting (default: 0)
  --permissioned                      If enabled, the node will allow only a defined list of nodes to connect
  --help, -h                          show help
```
### 压力测试工具stress

#### 工具说明
`stress`是Simpledger官方提供的压力测试工具，使用前，请先在节点启动参数中添加`--ws` `--wsaddr=0.0.0.0`，使得节点可以接受websocket连接请求。启动参数如下：
```shell
Usage of stress:
  -callcode
    	enable call contract code
  -chainid uint
    	chainId (default 1)
  -check
    	whether check transaction state
  -rand
    	random signer and receiver tx
  -seed uint
    	hash seed (default 1)
  -sendkey string
    	sender private key。can choose:
    		"5aedb85503128685e4f92b0cc95e9e1185db99339f9b85125c1e2ddc0f7c4c48",
    		"41a6df5663e5f674baaea1a021cdee1751ca28777e352ed0467fff420017978b",
    		"868d8f8b3d50e2a3ebfd5a08b16d84ee015db519d662bb0e5878388f0c15a6e3",
    		"9259787a40ec58154e7e04ae324b965cb4f199b1ef09708319d50ad36fc1cbeb",
    		"a42531bd0a7c1df628ab141f3be6086146ed01f74628a467f9f926b3625e17a0",
    		"2d396fd91b652c687bc6796932a39f190cf7b4aab26e079f8f28baba9939847e",
    		"35daed192142a1b608b60390036e7d3ad11ec6bc2d09182f3192f70ed54d4f2f",
    		"6ce1ddaaa7cd15232fd17838ab65b7beb8b6ad8e43be7d61548535b40a2a89b0"
    		 (default "5aedb85503128685e4f92b0cc95e9e1185db99339f9b85125c1e2ddc0f7c4c48")
  -sendtx
    	enable only send tx
  -threads int
    	the number of sender (default 4)
  -timelimit uint
    	timelimit seconds (default 300)
  -to string
    	tx reception
  -tps int
    	send tps limit, negative is limitless (default -1)
  -url string
    	websocket url (default "ws://127.0.0.1:8546")
```

#### 测试用例

- 测试随机存证交易
```shell
# 向192.168.4.242节点发送交易，6线程并发，每个线程的发送速率为1000TPS，测试300秒
stress --sendtx --chainid=10388 --url='ws://192.168.4.242:8546' --sendkey='868d8f8b3d50e2a3ebfd5a08b16d84ee015db519d662bb0e5878388f0c15a6e3' --threads=6 --tps=1000 --timelimit=300
```

- 测试随机转账交易
```shell
# 向192.168.4.242节点发送交易，6线程并发，每个线程的发送速率为1000TPS，测试300秒
stress --sendtx --chainid=10388 --url='ws://192.168.4.242:8546' --sendkey='868d8f8b3d50e2a3ebfd5a08b16d84ee015db519d662bb0e5878388f0c15a6e3' --threads=6 --tps=1000 --timelimit=300 --rand
```

- 测试合约内转账交易
COMING SOON
  

#### 特别说明

在使用`stress`压力测试时，需要在多台机器上同时使用`stress`客户端向多个共识节点发送交易，以达到负载均衡的效果。在16核心，32G内存的3共识节点+1观察节点的内网环境下，使用3个`stress`客户端均衡的发送存证交易，全网TPS可达到20000左右


### 共识管理工具consensus
`consensus`是Simpledger官方提供的共识管理工具，目前仅支持pbft共识，启动参数如下：
```shell
NAME:
   consensus pbft - pbft consenses

USAGE:
   consensus pbft command [command options] [arguments...]

COMMANDS:
     generate  generate pbft init file & static-nodes
     extra     calculate pbft header.extra by validators

OPTIONS:
   --help, -h  show help
```