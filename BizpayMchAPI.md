# BizPay 商户端接口（中心化版本）

### 1、接口调用方式及流程
> 接口方法：HTTP POST

> 接口地址：https://trade-api.bizkey.io/index.php

> 公共参数（必须提交的）

参数 | 说明
---|---
timestamp | 请求时间戳
signature | 本次请求的数据签名
appid | 从Bizkey申请的appid凭据

> 签名（signature参数）的说明：
```
将全部GET参数按照参数名ASCII码从小到大排序（signature除外），
所有键值对使用逗号拼接（即key1=value1,key2=value2…）拼接成字符串
然后在末尾拼接appsecret，再进行MD5
signature = md5('key1=value1,key2=value2,key3=value3…appsecret');
```

> 错误码

错误码 | 说明
---|---
10000 | 缺失字段(时间)
10001 | 超时(时间相差30秒以上)
10002 | 缺失字段(签名字段)
10003 | 签名不正确
10004 | 系统错误
10005 | 字段缺失(主要是appid)
10006 | APPID不存在
10007 | 该APPID已禁用
10010 | 字段缺失(业务字段)
---


### 2、注册用户

参数 | 必填 | 说明
  ---|---|---
m | Y | reg


- 返回示例
```
{
    "status": true,
    "code": 0,
    "data": {
        "user_id": "bzky_5b696cccb1b985b696cccb1be7", //有用的是这个
        "bizpay_address": "0x435gg46d1bc7f9bf90edgty55gsjkzh64dw3h2da", //以太坊公链版本的Bizpay用到的，中心化版本不要管
        "eth_wallet": "0x83cc9e141bc7f9bf90e52babc939e95f57dc1caa" //用户的数字钱包，这个也不用管
    }
}

```

### 3、开通收款功能接口

参数 | 必填 | 说明
  ---|---|---
c | Y |  bizpay
m | Y | enable
user_id | Y | 用户的ID
account_name | Y | 账户名称/店名
country | Y | 国家(SGP CHN)
currency  | Y | 币种（SGD、CNY）
phone | Y |  手机号码
password  | Y | 资金密码（可选）


- 返回示例
```
{
    "status": true,
    "code": 0
} 

```

### 4、获取店铺收款二维码链接

参数 | 必填 | 说明
  ---|---|---
c | Y |  bizpay
m | Y | store_qrcode
store_id  | Y | 收款商户的用户ID



- 返回示例
```
{
    "status": true,
    "code": 0,
    "data": {
        "store_id":"bzky_5b9252216a0fb5b9252216a13c",
        "qrcode_content": "https://trade-api.bizkey.io/bizwallet/index.html#/?app=bizpay&store_id=bzky_5b9252216a0fb5b9252216a13c"  //收款二维码内容
    }
} 

```


### 5、读取店铺收款信息（支持的数字货币币种、法币）

参数 | 必填 | 说明
  ---|---|---
c | Y |  bizpay
m | Y | store_info
store_id  | Y | 收款商户的用户ID


- 返回示例
```
{
    "status": true,
    "code": 0,
    "data": {
        "store_id": "bzky_5b9252216a0fb5b9252216a13c",
        "account_name":"五号收款",
        "legal_currency":"CNY",
        "currencies": [ \\支持的数字货币列表
            {
                "code": "BZKY",
                "asset_id": "6cfe566e-4aad-470b-8c9a-2fd35b49c68d",
                "contract": "0xd28cFec79dB8d0A225767D06140aee280718AB7E",
                "address": "0x69C8925CD6BD2CA0EFd0a5D426a9a825eC89702b"
            },
            {
                "code": "ETH",
                "asset_id": "2204c1ee-0ea2-4add-bb9a-b3719cfff93a",
                "contract": "",
                "address": "0x69C8925CD6BD2CA0EFd0a5D426a9a825eC89702b"
            }
        ]
    }
} 

```

### 6、下单生成收款二维码链接接口

参数 | 必填 | 说明
  ---|---|---
c | Y |  bizpay
m | Y | new_order
user_id  | Y | 收款商户的用户ID
asset_code  | Y | 支持的币种简称（ETH、BZKY）
amount | Y | 数字货币数量

- 返回示例
```
{
    "status": true,
    "code": 0,
    "data": {
        "order_sn":"201811291225231234567",
        "qrcode_content": "https://trade-api.bizkey.io/bizwallet/index.html#/?app=bizpay&order_sn=201811291225231234567" //二维码内容
    }
} 

```

### 7、查询商户可提现余额接口

参数 | 必填 | 说明
  ---|---|---
c | Y |  bizpay
m | Y | balance
user_id  | Y | 收款商户的用户ID

- 返回示例
```
{
    "status": true,
    "code": 0,
    "data": {
        "user_id": "bzky_232fsdgt54yvDngu54t3vdfhyr",
        "balance": "59.52",
        "currency": "CNY"
    }
} 

```

### 8、交易记录查询接口

参数 | 必填 | 说明
  ---|---|---
c | Y |  bizpay
m | Y | transfer_log
user_id  | Y | 收款商户的用户ID
start_time |	N | 查询开始时间[可选]
end_time | N | 查询结束时间[可选]
page | N | 页码（默认1）
pagesize | N | 每页记录数（默认20）

- 返回示例
```
{
    "status": true,
    "code": 0,
    "data": [
        {
            "id": "1",
            "user_id": "bzky_2fgy5grvafe564dgvdmdzb",
            "hash": "0x62d5d64e9a2938c4d98eb3165638b63bb29dbbab2323cc56b8926246a7c00c56",
            "asset_code": "BZKY",
            "asset_amount": "39242.43963",
            "from_address": "0x6efb20f61b80f6a7ebe7a107bace58288a51fb34",
            "to_address": "0xa0c4c42ebfd8668d20cc784dccdb21c7a780114b",
            "currency": "CNY",
            "money": "3991.24",
            "transfer_time": "2018-10-16 16:20:03",
            "status": "Success",
            "add_time": "2018-10-16 16:20:10"
        },
        {
            "id": "2",
            "user_id": "bzky_2fgy5grvafe564dgvdmdzb",
            "hash": "0xa409f17e0b4c5dd4631387ca09e7a361436806d0230a2aafadda02d0d8fb5a67",
            "asset_code": "BZKY",
            "asset_amount": "39242.43963",
            "from_address": "0x6efb20f61b80f6a7ebe7a107bace58288a51fb34",
            "to_address": "0xa0c4c42ebfd8668d20cc784dccdb21c7a780114b",
            "currency": "CNY",
            "money": "3991.24",
            "transfer_time": "2018-10-16 16:25:13",
            "status": "Success",
            "add_time": "2018-10-16 16:25:13"
        }
    ]
} 

```


### 9、检查是否设置资金提现密码

参数 | 必填 | 说明
  ---|---|---
c | Y |  bizpay
m | Y | isset_pwd
user_id  | Y | 收款商户的用户ID

- 返回示例
```
{
    "status": true,
    "code": 0,
    "data": {
        "isset": true
    }
} 

```

### 10、设置资金提现密码接口

参数 | 必填 | 说明
  ---|---|---
c | Y |  bizpay
m | Y | set_pwd
user_id  | Y | 收款商户的用户ID
password | Y | 资金密码

- 返回示例
```
{
    "status": true,
    "code": 0,
}  

```

### 11、绑定银行卡接口

参数 | 必填 | 说明
  ---|---|---
c | Y |  bizpay
m | Y | bind_bankcard
user_id  | Y | 收款商户的用户ID
bank_name  | Y | 发卡银行名称
card_no  | Y | 卡号
card_owner  | Y | 持卡人

- 返回示例
```
{
    "status": true,
    "code": 0,
} 

```

### 12、商户银行卡查询接口

参数 | 必填 | 说明
  ---|---|---
c | Y |  bizpay
m | Y | get_bankcard
user_id  | Y | 收款商户的用户ID


- 返回示例
```
{
    "status": true,
    "code": 0,
    "data": {
        "bind_id":"2",  
        "bank_name": "中国工商银行",
        "card_no": "9558803602134435676",
        "card_owner": "朱某人"
    }
} 

```

### 13、修改已绑定的银行卡

参数 | 必填 | 说明
  ---|---|---
c | Y |  bizpay
m | Y | edit_bankcard
user_id  | Y | 收款商户的用户ID
bind_id  | Y | 绑定的银行卡ID（接口12返回的ID）
bank_name  | Y | 发卡银行名称
card_no  | Y | 卡号
card_owner  | Y | 持卡人

- 返回示例
```
{
    "status": true,
    "code": 0,
} 

```

### 14、用户提现接口

参数 | 必填 | 说明
  ---|---|---
c | Y |  bizpay
m | Y | withdraw
user_id  | Y | 收款商户的用户ID
amount  | Y | 提现金额
password   | Y | 提现密码

- 返回示例
```
{
    "status": true,
    "code": 0,
} 

```

### 15、收款消息推送（callback）

> 对后台通知交互时，如果接入方平台收到回调通知的应答不是成功或超时，Bizpay认为通知失败，钱包会通过一定的策略定期重新发起通知，尽可能提高通知的成功率，但不保证通知最终能成功。

> 提醒：接入方系统对于回调通知的内容一定要做签名验证,防止数据泄漏导致出现“假通知”。

> 回调地址：接入方平台提供，开通平台Appid时提供绑定在Bizpay。

> 签名方式：与请求接口一致

> 请求方式：POST

- 回调数据示例
```
POST数据内容：
money=4.95&asset_amount=0.0279700000&msg=数字货币收款成功&msgtype=TRANSACTION&signature=f3f464c1cf48417ddad95a1b345ae184&hash=0x72f682aaf62ea6d21cdfffddae50c8d25c4c0ea135bc225c109bbb08034bdbb4&msg_id=MSG201901012901&trade_id=191&currency=SGD&fee=0.00&transfer_time=1546343430&appid=TS0001&asset_code=ETH&from_address=0x8c5043697c45485d641d022d7b523caee36dda79&status=Success&timestamp=1546343455&user_id=bzky_5bd7d08f3cc0d5bd7d08f3cc49&to_address=0x6bd2cddb3116d754dd4e2c18da6e68107ebbee3f

```