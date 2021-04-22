## 第3章 API接口说明

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