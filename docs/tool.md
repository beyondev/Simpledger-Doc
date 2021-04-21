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
  --nodiscover                        Disables the peer discovery mechanism (manual peer addition)
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
  --pbft.maxblocktxs value            Default max txs one block can seal (default: 0)

MISC OPTIONS:
  --override.singularity value        Manually specify singularity fork-block, overriding the bundled setting (default: 0)
  --permissioned                      If enabled, the node will allow only a defined list of nodes to connect
  --help, -h                          show help

```
### Simpledger共识管理工具consensus

### 压力测试工具stress