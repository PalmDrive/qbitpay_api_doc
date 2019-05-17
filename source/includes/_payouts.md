# Payouts

## Payout对象概述

> 对象示例

```json
{
  "id": "d55ba126aa385c808t8aeb0d",
  "object": "payout",
  "createdAt": "2019-02-23T16:13:12.278Z",
  "amount": 100.1,
  "fee": 1.9,
  "currency": "USD",
  "description": "Payout memo",
  "status": "paid",
  "type": "bankAccount",
  "arrivalDate": "2019-02-25T16:13:12.278Z",
  "externalOrderId": "ba126aa385cd5580",
  "destination": {
    "id": "ba126aa385cd55808t8aeb0d",
    "object": "bankAccount",
    "payerName": "user name",
    "bankAccountNum": "444444444444111",
    "bankRoutingNum": "12345678",
    "bankName": "Chase",
    "bankAddr": "505 Michigan ave, NY, 12345",
    "country": "United States",
    "states": "California",
    "city": "San Francisco",
    "addr": "110 market street",
    "type": "bankAccount",
    "typeLabel": {
      "en": "Bank account",
      "zh": "银行转账"
    }
  },
  "trade": {
    "id": "d55ba1t8aeb0d26aa385c808",
    "object": "charge",
    "createdAt": "2019-02-23T16:13:12.278Z",
    "status": "closed",
    "baseCurrency": "BTC",
    "quoteCurrency": "USD",
    "baseAmount": 0.001234,
    "price": 5000.5,
    "totalCost": 6.17,
    "fee": 0,
  }
}
```

Payout对象记录下发的数据。

属性	    |     描述
--------  | -----------
id *string* | 由 Qbit Pay 生成的对象 ID。
object *string* | 值为 "payout"。
createdAt *string* | 支付创建时的时间戳, 时区为UTC。
amount *number* | 用户收到的扣除了手续费的实际金额
fee *number* | 手续费
currency *string* | 币种，和商户设置的结算币种相同
description *string* | 备注描述
status *string* | 交易状态，值可以是pending(下发请求发起), inTransit（在处理中）, paid（下发完成）, cancelled（取消）, failed (下发失败)
type *string* | 下发的支付方式的类型，对应destination.type
arrivalDate *string* | 下发完成，到账的时间
externalOrderId *string* | 商户内部的订单号
destination *object* | 支付方式，详见paymentMethod
trade *object* | 相关联的交易，只有当创建payout时传的币种和结算币种不一致，才会有这个字段。其包含字段详见charge

## 创建Payout对象

> 请求示例

```shell
curl "https://matrix-content-s.ailingual.cn/api/v1/payouts"
  -u "my_api_key:"
  -d "amount=100"
  -d "currency=USD"
  -d "description=Payout memo"
  -d "externalOrderId=ba126aa385cd5580"
  -d "destination={...}"
```

> 返回数据

```json
{
  "id": "d55ba126aa385c808t8aeb0d",
  "object": "payout",
  "createdAt": "2019-02-23T16:13:12.278Z",
  "amount": 98.1,
  "fee": 1.9,
  "currency": "USD",
  "description": "Payout memo",
  "status": "pending",
  "type": "bankAccount",
  "arrivalDate": "2019-02-25T16:13:12.278Z",
  "externalOrderId": "ba126aa385cd5580",
  "destination": {
    "id": "ba126aa385cd55808t8aeb0d",
    "object": "bankAccount",
    "payerName": "user name",
    "bankAccountNum": "444444444444111",
    "bankRoutingNum": "12345678",
    "bankName": "Chase",
    "bankAddr": "505 Michigan ave, NY, 12345",
    "country": "United States",
    "states": "California",
    "city": "San Francisco",
    "addr": "110 market street",
    "type": "bankAccount",
    "typeLabel": {
      "en": "Bank account",
      "zh": "银行转账"
    }
  }
}
```

发起一次新的下发请求。数据放在请求body里面，data type是json。Payout对象的状态变化（包括最初的创建），Qbit Pay 都会发送 Webhooks 通知。

当传入的currency不同于结算币种时，系统会自动交易来获得需要下发的币种。如商户的结算币种是USDT，传入currency是USD，系统自动兑换USDT到USD，再完成下发。此时返回的数据中将带有trade字段。若currency和商户结算币种相同，则在返回数据中无trade字段。

请求参数	|   描述
-------- | --------
amount *REQUIRED number* | 下发总金额，单位取决于currency。该金额没有扣除手续费。用户实际到账金额是扣除手续费的金额，可能会比该金额小。
currency *REQUIRED string* | 币种，是用户实际收到的金额币种
destination *REQUIRED object* | 下发到的账户。详见paymentMethod的参数
description *OPTIONAL string* | 备注描述
externalOrderId *OPTIONAL string* | 商户内部的订单号

不同的destination type, 需要的字段如下:

type	|   必填字段
-------- | --------
bankAccount | payerName, bankAccountNum, bankName, type
wallet | payerName, addr, type
Alipay | payerName, bankAccountNum, type
PayPal | payerName, bankAccountNum, type
Coins.ph | payerName, bankAccountNum, type
GCash | payerName, bankAccountNum, type

### 返回

返回一个 payout 对象。

## 查询Payout对象

```shell
curl "https://matrix-content-s.ailingual.cn/api/v1/payouts/d55ba126aa385c808t8aeb0d"
  -u "my_api_key:"
```

> 返回数据

```json
{
  "id": "d55ba126aa385c808t8aeb0d",
  "object": "payout",
  "createdAt": "2019-02-23T16:13:12.278Z",
  "amount": 98.1,
  "fee": 1.9,
  "currency": "USD",
  "description": "Payout memo",
  "status": "pending",
  "type": "bankAccount",
  "arrivalDate": "2019-02-25T16:13:12.278Z",
  "externalOrderId": "ba126aa385cd5580",
  "destination": {
    "id": "ba126aa385cd55808t8aeb0d",
    "object": "bankAccount",
    "payerName": "user name",
    "bankAccountNum": "444444444444111",
    "bankRoutingNum": "12345678",
    "bankName": "Chase",
    "bankAddr": "505 Michigan ave, NY, 12345",
    "country": "United States",
    "states": "California",
    "city": "San Francisco",
    "addr": "110 market street",
    "type": "bankAccount",
    "typeLabel": {
      "en": "Bank account",
      "zh": "银行转账"
    }
  }
}
```

你可以在异步通知之前，通过查询接口确认payout的状态。通过payout对象的id查询一个已创建的payout对象。

请求参数	|    描述
---- | --------
PAYOUT_ID *REQUIRED string* | 查询的 payout 对象 id。

### 返回

返回一个已存在的 charge 对象或者一个错误，详见 错误。

## 查询Payout列表

> 请求示例

```shell
curl "https://matrix-content-s.ailingual.cn/api/v1/payouts"
  -u "my_api_key:"
```

> 返回数据

```json
{
  "object": "list",
  "url": "/api/v1/payouts",
  "data": [
    {
      "id": "d55ba126aa385c808t8aeb0d",
      "object": "payout",
      "createdAt": "2019-02-23T16:13:12.278Z",
      "amount": 98.1,
      "fee": 1.9,
      "currency": "USD",
      "description": "Payout memo",
      "status": "pending",
      "type": "bankAccount",
      "arrivalDate": "2019-02-25T16:13:12.278Z",
      "externalOrderId": "ba126aa385cd5580",
      "destination": {
        "id": "ba126aa385cd55808t8aeb0d",
        "object": "bankAccount",
        "payerName": "user name",
        "bankAccountNum": "444444444444111",
        "bankRoutingNum": "12345678",
        "bankName": "Chase",
        "bankAddr": "505 Michigan ave, NY, 12345",
        "country": "United States",
        "states": "California",
        "city": "San Francisco",
        "addr": "110 market street",
        "type": "bankAccount",
        "typeLabel": {
          "en": "Bank account",
          "zh": "银行转账"
        }
      }
    }
  ]
}
```

返回之前创建过 payout 对象的一个列表。列表是按创建时间进行排序，总是将最新的 payout 对象显示在最前。

请求参数 |	描述
------- | -------
limit *optional int* | 限制每页可以返回多少对象，限制范围是从 1~100 项，默认是 10 项。
starting_after *optional string* | 在分页时使用的指针，决定了列表的第一项从何处开始。假设你的一次请求返回列表的最后一项的 id 是 obj_end，你可以使用 starting_after = obj_end 去获取下一页。
ending_before *optional string* | 在分页时使用的指针，决定了列表的最末项在何处结束。假设你的一次请求返回列表的第一项的 id 是 obj_start，你可以使用 ending_before = obj_start 去获取上一页。
destination *optional string* | 关联支付方式的id
status *optional string* | 状态，值可以是pending, inTransit, paid, failed, cancelled


### 返回

返回一个已存在的 payout 对象列表或者一个错误，详见 错误

## Payout 相关的 Event 类型

> 示例对象

```json
{
  "id": "fDOuTy95uSiTi",
  "type": "payout.paid",
  "createdAt": "2019-02-25T16:13:12.278Z",
  "object": "event",
  "pendingWebhooks": 0,
  "livemode": true,
  "data": {
    "id": "d55ba126aa385c808t8aeb0d",
    "object": "payout",
    "createdAt": "2019-02-23T16:13:12.278Z",
    "amount": 98.1,
    "fee": 1.9,
    "currency": "USD",
    "description": "Payout memo",
    "status": "pending",
    "type": "bankAccount",
    "arrivalDate": "2019-02-25T16:13:12.278Z",
    "externalOrderId": "ba126aa385cd5580",
    "destination": {
      "id": "ba126aa385cd55808t8aeb0d",
      "object": "bankAccount",
      "payerName": "user name",
      "bankAccountNum": "444444444444111",
      "bankRoutingNum": "12345678",
      "bankName": "Chase",
      "bankAddr": "505 Michigan ave, NY, 12345",
      "country": "United States",
      "states": "California",
      "city": "San Francisco",
      "addr": "110 market street",
      "type": "bankAccount",
      "typeLabel": {
        "en": "Bank account",
        "zh": "银行转账"
      }
    },
  }
}
```

该类型的 Event 对象会在 payout 对象状态变化时触发（包括最初时的创建），以 Webhooks 形式发送至客户配置的 Webhooks URL。

事件类型	| 描述
------- | -------
payout.pending	|  请求发起时触发。。
payout.inTransition	|  进入处理中时触发。
payout.paid	|  处理完成时触发。
payout.failed	| 失败时触发。
payout.cancelled	| 取消时触发。
