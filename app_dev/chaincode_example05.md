### 社区能源共享
#### 功能描述
本 [合约](chaincode_example05.go) 以纽约实验性的能源微电网为例，作为一个简单的案例进行实现。

>“在总统大道的一边，五户家庭通过太阳能板发电；在街道的另一边的五户家庭可以购买对面家庭不需要的电力。而连接这项交易的就是区块链网络，几乎不需要人员参与就可以管理记录交易。”但是这个想法是非常有潜力的，能够代表未来社区管理能源系统。”

布鲁克林微电网开发商 LO3 创始人 Lawrence Orsini 说：

>“我们正在这条街道上建立一个可再生电力市场，来测试人们对于购买彼此手中的电力是否感兴趣。如果你在很远的地方生产能源，运输途中会有很多损耗，你也得不到这电力价值。但是如果你就在街对面，你就能高效的利用能源。”

在某一块区域内存在一个能源微电网，每一户家庭可能为生产者也可能为消费者。部分家庭拥有太阳能电池板，太阳能电池板的剩余电量为可以售出的电力的值，为了简化，单位为1.需要电力的家庭可以向有足够余额的电力的家庭购买电力。

账户私钥应该由安装在本地的客户端生成，本例中为了简便，使用模拟私钥和公钥。每位用户的私钥为guid+“1”，公钥为guid+“2”。签名方式简化为私钥+"1"

#### 数据结构设计
在该智能合约中暂时只有一种角色，为每一户家庭用户。

- 家庭用户
    - 账户地址
    - 剩余能量 //部分家庭没有太阳能电池板，值为0
    - 账户余额（电子货币）
    - 编号
    - 状态  //0：不可购买， 1：可以购买
    - 账户公钥
    - 账户私钥
- 交易(一笔交易必须同时具有卖方和买方的公钥签名，方能承认这笔交易。公钥签名生成规则，公钥+待创建交易的ID号，在本交易类型中，只要买家有足够的货币，卖家自动会对交易进行签名)
    - 购买方地址
    - 销售方地址
    - 电量销售量
    - 电量交易金额
    - 编号
    - 交易时间

#### function及各自实现的功能
- `init`  初始化操作
- `invoke`   调用合约内部的函数
- `query`   查询相关的信息
- `createUser` 创建新用户，并加入到能源微网中   invoke
- `buyByAddress` 向某一位用户购买一定量的电力   invoke
- `getTransactionById` 通过id获取交易内容  query
- `getTransactions` 获取交易（如果交易数大于10，获取前10个） query
- `getHomes` 获取用户（如果用户数大于10，获取前10个） query
- `getHomeByAddress` 通过地址获取用户 query
- `changeStatus` 某一位用户修改自身的状态  invoke

- `writeUser` 将新用户写入到键值对中  
- `writeTransaction` 记录交易
#### 接口设计
`createUser`

request参数:
```
args[0] 剩余能量值 
args[1] 剩余金额
```
response参数:
```
新建家庭用户的json表示
```

`buyByAddress`

request参数:
```
args[0] 卖家的账户地址
args[1] 买家签名
args[2] 买家的账户地址
args[3] 想要购买的电量数值
```
response参数:
```
购买成功的话返回该transaction的json串。
购买失败返回error
```

`getTransactionById`

request参数:
```
args[0] 交易编号
```
response参数:
``` 
查询结果的transaction 交易表示
```

`getTransactions`

request参数:
```
none
 ```
response参数:
```
获取所有的交易列表（如果交易大于10，则返回前10个）
```

`getHomeByAddress`

request参数
```
args[0] address
```
response参数
```
用户信息的json表示
```

`getHomes`

response参数
```
获取所有的用户列表（如果用户个数大于10，则返回前10个）
```

`changeStatus`

request参数:
```
args[0] 账户地址
args[1]　账户签名
args[2] 对自己的账户进行的操作，0：设置为不可购买  1：设置状态为可购买
```
response参数:
```
修改后的用户信息json表示
```

#### 测试
