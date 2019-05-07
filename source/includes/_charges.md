# Charges

## Charge对象概述

> 对象示例

```json
{
  "id": "ifDOu5uTSTy9i",
  "object": "charge",
  "createdAt": "2019-02-23T16:13:12.278Z",
  "paid": false,
  "status": "pending",
  "amount": 100,
  "totalCost": 676,
  "price": 38737.7,
  "fee": 0,
  "baseAmount": 0.017451,
  "currency": "USD",
  "quoteCurrency": "CNY",
  "baseCurrency": "BTC",
  "externalOrderId": "DTSifOuTy95ui",
  "paymentPageUrl": "https://qbitpay.blockscape.co/?zh#/otc/payment?tradeId=uTifOuTy95DTSifOy95",
  "cbUrl": "https://api.blockscape.co",
  "redirectUrl": "https://qbitpay.blockscape.co",
  "paymentMethod": {
    "object": "paymentMethod",
    "alipayQRCodeUrl": "https://alipay.net.com/qrcode",
    "type": "Alipay",
    "currency": "CNY",
    "status": "active",
    "typeLabel": {
      "en": "Alipay",
      "zh": "支付宝"
    }
  }
}
```

你可以创建一个 charge 对象向用户收款。charge 是一个支付凭据对象，所有和支付相关的要素信息都存储在这个对象中，你的服务端可以通过发起支付请求来创建一个新的 charge 对象，也可以随时查询一个或者多个 charge 对象的状态。每个 charge 对象都拥有一个标识 id，该 id 在 Qbit Pay 系统内唯一。

属性	    |     描述
--------  | -----------
id *string* | 由 Qbit Pay 生成的支付对象 ID。
object *string* | 值为 "charge"。
createdAt *string* | 支付创建时的时间戳, 时区为UTC。
paid *boolean* | 是否已付款。
status *string* | 支付状态，有pending, buyerPaid, sellerConfirmed, closed, cancelled, dispute。
amount *number* | 以相应currency计算的支付金额。
currency *string* | 货币代码，大写字母。
price *number* | 兑换数字货币的价格，以quoteCurrency计价
fee *number* | 手续费
baseAmount *number* | 所换取的数字货币的数量
baseCurrency *string* | 要换取的数字货币
totalCost *number* | 用户直接支付，用于兑换数字货币的金额
quoteCurrency *string* | 用户用于换取baseCurrency的货币
externalOrderId *string* | 商户系统内部订单号。
paymentPageUrl *string* | 调起的H5支付页面的URL。
cbUrl *string* | Webhook 回调的接口地址
redirectUrl *string* | 完成支付以后，跳转的页面
paymentMethod *object* | 支付方式详情，详见PaymentMethods

## 创建Charge对象

> 请求示例

```shell
curl "https://matrix-content-s.ailingual.cn/api/v1/charges"
  -u "my_api_key:"
  -d "amount=100"
  -d "currency=USD"
  -d "externalOrderId=DTSifOuTy95ui"
  -d "cbUrl=https://api.blockscape.co"
  -d "redirectUrl=https://qbitpay.blockscape.co"
```

> 返回数据

```json
{
  "id": "ifDOu5uTSTy9i",
  "object": "charge",
  "createdAt": "2019-02-23T16:13:12.278Z",
  "paid": false,
  "status": "pending",
  "amount": 100,
  "currency": "USD",
  "externalOrderId": "DTSifOuTy95ui",
  "paymentPageUrl": "https://qbitpay.blockscape.co/?zh#/otc/payment?tradeId=uTifOuTy95DTSifOy95",
  "cbUrl": "https://api.blockscape.co",
  "redirectUrl": "https://qbitpay.blockscape.co"
}
```

发起一次支付请求时需要创建一个新的 charge 对象。数据放在请求body里面，data type是json。当支付成功后，Qbit Pay 会发送 Webhooks 通知。

请求参数	|   描述
-------- | --------
amount *REQUIRED number* | 支付金额，单位是美元或元，取决于currency
currency *REQUIRED string* | 结算货币代码，可填USD，USDT
externalOrderId *REQUIRED string* | 商户系统内部订单号
cbUrl *REQUIRED string* | Webhook 回调的接口地址
redirectUrl *REQUIRED string* | 完成支付以后，跳转的页面
baseCurrency *OPTIONAL string* | 默认BTC，要换取的数字货币，可填BTC, USDT
quoteCurrency *OPTIONAL string* | 默认CNY，用户用于换取baseCurrency的货币，可填CNY，USD, PHP。近期将会支持更多货币。
paymentType *OPTIONAL string* | 指定一种支付方式，CNY可选Alipay, Yunshanfu, bankAccount, USD可选PayPal, bankAccount, PHP可选Coins.ph, GCash

### 返回

返回一个支付凭据 charge 对象。

<aside class="notice">
当用paymentPageUrl调起支付H5页面的时候，必须把 accessToken 拼接到 url 的参数中，以`&accessToken=${accessToken}`的形式。
</aside>

## 查询Charge对象

> 请求示例

```shell
curl "https://matrix-content-s.ailingual.cn/api/v1/charges/ifDOu5uTSTy9i"
  -u "my_api_key:"
```

> 返回数据

```json
{
  "id": "ifDOu5uTSTy9i",
  "object": "charge",
  "createdAt": "2019-02-23T16:13:12.278Z",
  "paid": false,
  "status": "pending",
  "amount": 100,
  "currency": "USD",
  "externalOrderId": "DTSifOuTy95ui",
  "paymentPageUrl": "https://qbitpay.blockscape.co/?zh#/otc/payment?tradeId=uTifOuTy95DTSifOy95",
  "cbUrl": "https://api.blockscape.co",
  "redirectUrl": "https://qbitpay.blockscape.co"
}
```

你可以在后台异步通知之前，通过查询接口确认支付状态。通过charge对象的id查询一个已创建的charge对象。

请求参数	|    描述
---- | --------
CHARGE_ID *REQUIRED string* | 查询的 charge 对象 id。

### 返回

返回一个已存在的 charge 对象或者一个错误，详见 错误。

## 查询Charge列表

> 请求示例

```shell
curl "https://matrix-content-s.ailingual.cn/api/v1/charges"
  -u "my_api_key:"
```

> 返回数据

```json
{
  "object": "list",
  "url": "/api/v1/charges",
  "data": [
    {
      "id": "5uTSifDOuTy9i",
      "object": "charge",
      "createdAt": "2019-02-25T16:13:12.278Z",
      "paid": false,
      "status": "pending",
      "amount": 1000,
      "currency": "USD",
      "externalOrderId": "DTSifOuTy95ui",
      "paymentPageUrl": "https://qbitpay.blockscape.co/?zh#/otc/payment?tradeId=DTSifOuTy95ifOuTy95",
      "cbUrl": "https://api.blockscape.co",
      "redirectUrl": "https://qbitpay.blockscape.co"
    },
    {
      "id": "OuTyTSi9i5ufD",
      "object": "charge",
      "createdAt": "2019-02-24T16:13:12.278Z",
      "paid": false,
      "status": "pending",
      "amount": 900,
      "currency": "USD",
      "externalOrderId": "DTSifOuTy95ui",
      "paymentPageUrl": "https://qbitpay.blockscape.co/?zh#/otc/payment?tradeId=OuTy95DTSififOuTy95",
      "cbUrl": "https://api.blockscape.co",
      "redirectUrl": "https://qbitpay.blockscape.co"
    },
    {
      "id": "ifDOu5uTSTy9i",
      "object": "charge",
      "createdAt": "2019-02-23T16:13:12.278Z",
      "paid": true,
      "status": "closed",
      "amount": 950,
      "currency": "USD",
      "externalOrderId": "DTSifOuTy95ui",
      "paymentPageUrl": "https://qbitpay.blockscape.co/?zh#/otc/payment?tradeId=uTifOuTy95DTSifOy95",
      "cbUrl": "https://api.blockscape.co",
      "redirectUrl": "https://qbitpay.blockscape.co"
    }
  ]
}
```

返回之前创建过 charge 对象的一个列表。列表是按创建时间进行排序，总是将最新的 charge 对象显示在最前。

请求参数 |	描述
------- | -------
limit *optional int* | 限制每页可以返回多少对象，限制范围是从 1~100 项，默认是 10 项。
starting_after *optional string* | 在分页时使用的指针，决定了列表的第一项从何处开始。假设你的一次请求返回列表的最后一项的 id 是 obj_end，你可以使用 starting_after = obj_end 去获取下一页。
ending_before *optional string* | 在分页时使用的指针，决定了列表的最末项在何处结束。假设你的一次请求返回列表的第一项的 id 是 obj_start，你可以使用 ending_before = obj_start 去获取上一页。
externalOrderId *optional string* | 商户系统内部订单号

### 返回

返回一个已存在的 charge 对象列表或者一个错误，详见 错误

## Charge 相关的 Event 类型

> 示例对象

```json
{
  "id": "fDOuTy95uSiTi",
  "type": "charge.succeeded",
  "createdAt": "2019-02-25T16:13:12.278Z",
  "object": "event",
  "pendingWebhooks": 0,
  "livemode": true,
  "data": {
    "id": "ifDOu5uTSTy9i",
    "object": "charge",
    "createdAt": "2019-02-23T16:13:12.278Z",
    "paid": false,
    "status": "pending",
    "amount": 100,
    "totalCost": 676,
    "price": 38737.7,
    "fee": 0,
    "baseAmount": 0.017451,
    "currency": "USD",
    "quoteCurrency": "CNY",
    "baseCurrency": "BTC",
    "externalOrderId": "DTSifOuTy95ui",
    "paymentPageUrl": "https://qbitpay.blockscape.co/?zh#/otc/payment?tradeId=uTifOuTy95DTSifOy95",
    "cbUrl": "https://api.blockscape.co",
    "redirectUrl": "https://qbitpay.blockscape.co",
    "paymentMethod": {
      "object": "paymentMethod",
      "alipayQRCodeUrl": "https://alipay.net.com/qrcode",
      "type": "Alipay",
      "currency": "CNY",
      "status": "active",
      "typeLabel": {
        "en": "Alipay",
        "zh": "支付宝"
      }
    }
  }
}
```

该类型的 Event 对象会在 charge 对象支付成功后触发，以 Webhooks 形式发送至客户配置的 Webhooks URL。

事件类型	| 描述
------- | -------
charge.succeeded	|  支付成功时触发。
charge.expired	|  支付过期时触发。
charge.failed	|  支付失败时触发。
charge.dispute.created	|  支付进入纠纷仲裁时触发。
charge.dispute.closed	|  支付仲裁完成时触发。
