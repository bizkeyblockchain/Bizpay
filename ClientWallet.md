# BizPay 用户端接口（消费者钱包）

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

> 使用流程
```
1 用户扫码获取二维码信息（store_id / order_sn）
2 如果是订单二维码
  2.1 调用“读取订单详情接口”读取订单详情
  2.2 根据订单金额及币种让用户支付
  2.3 调用“订单支付通知接口”提交交易hash -> 交易完毕。
3 如果是商户收款二维码
  3.1 调用“读取店铺收款信息详情”接口获取商户支持的币种。
  3.2 用户给指定商户地址付款
  3.3 调用“转账支付通知接口”提交交易hash -> 交易完毕。

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



### 2、二维码规则
> 目前有2种二维码，一种是订单二维码（由商户在Bizpay上输入金额选好币种生成，带有订单号），一种是商户收款二维码（每个商户固定，带有商户ID）；两种二维码都是URL的形式，如果普通APP扫码识别可以直接进入H5版数字钱包。（URL可能会改变，请不要以URL作为识别的关键，参数中app=bizpay才是关键字，包含store_id参数则为商户收款二维码，包含order_sn参数则为订单二维码）

1. 订单二维码
```
URL参数中包含 app=bizpay&order_sn=xxxx

如：https://trade-api.bizkey.io/bizwallet/index.html#/?app=bizpay&order_sn=201811291225231234567

```
2. 商户收款二维码
```
URL参数中包含 app=bizpay&store_id=xxxx

如：https://trade-api.bizkey.io/bizwallet/index.html#/?app=bizpay&store_id=bzky_5b9252216a0fb5b9252216a13c

```

### 3、读取订单详情接口

参数 | 必填 | 说明
  ---|---|---
c | Y | bizpay
m | Y | order_info
order_sn | Y |  订单号


- 返回示例
```
{
    "status": true,
    "code": 0,
    "data": {
        "order_sn": "201811291225231234567",
        "asset_id": "6cfe566e-4aad-470b-8c9a-2fd35b49c68d",  //mixin上的token asset_id
        "contract": "0xd28cFec79dB8d0A225767D06140aee280718AB7E", //合约地址（如果有的话）
        "amount ": "1.345", //数字货币数量
        "address": "0x69C8925CD6BD2CA0EFd0a5D426a9a825eC89702b", //收款地址
        "currency_code": "SGD",    //收款法币
        "currency_amount": 74.3337 //法币金额
    }
} 
```



### 4、订单支付通知接口

参数 | 必填 | 说明
  ---|---|---
c | Y | bizpay
m | Y | order_payed
order_sn | Y |  订单号
tx_hash | Y |  从公链转账时的交易Hash


- 返回示例
```
{
    "status": true,
    "code": 0
} 

```

### 5、读取店铺收款信息详情

参数 | 必填 | 说明
  ---|---|---
c | Y | bizpay
m | Y | store_info
store_id | Y |  店铺id（从二维码读出）


- 返回示例
```
{
    "status": true,
    "code": 0,
    "data": {
        "store_id": "bzky_5b9252216a0fb5b9252216a13c",
        "account_name":"五号收款", //商户名称
        "legal_currency":"CNY", //商户收款法币币种
        "currencies": [    //商户支持的币种列表
            {
                "code": "BZKY", //数字货币简称
                "asset_id": "6cfe566e-4aad-470b-8c9a-2fd35b49c68d",
                "contract": "0xd28cFec79dB8d0A225767D06140aee280718AB7E",
                "address": "0x69C8925CD6BD2CA0EFd0a5D426a9a825eC89702b" //转账地址
            },
            {
                "code": "ETH", //数字货币简称
                "asset_id": "2204c1ee-0ea2-4add-bb9a-b3719cfff93a",
                "contract": "",
                "address": "0x69C8925CD6BD2CA0EFd0a5D426a9a825eC89702b" //转账地址
            }
        ]
    }
}

```


### 6、转账支付通知接口

参数 | 必填 | 说明
  ---|---|---
c | Y | bizpay
m | Y | order_payed
store_id | Y |  收款店铺id
tx_hash | Y |  从公链转账时的交易Hash


- 返回示例
```
{
    "status": true,
    "code": 0
} 

```




### 附：

币种 | 简称 | asset_id
---|---|---
Bizkey | BZKY | de7d29ba-432d-377d-95cd-e7ad81986bf2
Bitcoin | BTC| c6d0c728-2624-429b-8e0d-d9d19b6592fa
Bitcoin Cash | BCH| fd11b6e3-0b87-41f1-a41f-f0e9b49e5bf0
Dash | DASH| 6472e7e3-75fd-48b6-b1dc-28d294ee1476
Dogecoin | DOGE| 6770a1e5-6086-44d5-b60f-545f9d9e8ffd
EOS | EOS| 6cfe566e-4aad-470b-8c9a-2fd35b49c68d
Ethereum | ETH| 43d61dcd-e413-450d-80b8-101d5e903357
ETC | ETC| 2204c1ee-0ea2-4add-bb9a-b3719cfff93a
Litecoin | LTC| 76c802a2-7c88-447f-a93e-c29c9e5dd9c8
NEM | XEM| 27921032-f73e-434e-955f-43d55672ee31
Ripple | XRP| 23dfb5a5-5d7b-48b6-905f-3970e3176e27
Siacoin | SC| 990c4c29-57e9-48f6-9819-7d986ea44985
Zcash | ZEC| c996abc9-d94e-4494-b1cf-2a3fd3ac5714


