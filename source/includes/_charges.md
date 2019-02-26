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
  "currency": "USD",
  "externalOrderId": "DTSifOuTy95ui",
  "paymentPageUrl": "https://qbitpay.blockscape.co/?zh#/otc/payment?tradeId=uTifOuTy95DTSifOy95"
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
amount *float* | 支付金额，单位是元(美元)。
currency *string* | 货币代码，大写字母。
externalOrderId *string* | 商户系统内部订单号。
paymentPageUrl *string* | 调起的H5支付页面的URL。

## 创建Charge对象

> 请求示例

```shell
curl "https://matrix-content-s.ailingual.cn/api/v1/charges"
  -u "my_api_key:"
  -d "amount=100"
  -d "currency=USD"
  -d "externalOrderId=DTSifOuTy95ui"
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
  "paymentPageUrl": "https://qbitpay.blockscape.co/?zh#/otc/payment?tradeId=uTifOuTy95DTSifOy95"
}
```

发起一次支付请求时需要创建一个新的 charge 对象。当支付成功后，Qbit Pay 会发送 Webhooks 通知。

请求参数	|   描述
-------- | --------
amount *REQUIRED string* | 支付金额，单位是美元或元，取决于currency
currency *REQUIRED string* | 货币代码，目前填USD
externalOrderId *REQUIRED string* | 商户系统内部订单号

### 返回

返回一个支付凭据 charge 对象。

<aside class="notice">
当用paymentPageUrl调起支付H5页面的时候，必须传带有`Bearer ${accessToken}`的Authorization header。
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
  "paymentPageUrl": "https://qbitpay.blockscape.co/?zh#/otc/payment?tradeId=uTifOuTy95DTSifOy95"
}
```

你可以在后台异步通知之前，通过查询接口确认支付状态。通过charge对象的id查询一个已创建的charge对象。

请求参数	|    描述
---- | --------
CHARGE_ID *REQUIRED string* | 查询的 charge 对象 id，该参数仅需要填写在请求地址内。

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
      "paymentPageUrl": "https://qbitpay.blockscape.co/?zh#/otc/payment?tradeId=DTSifOuTy95ifOuTy95"
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
      "paymentPageUrl": "https://qbitpay.blockscape.co/?zh#/otc/payment?tradeId=OuTy95DTSififOuTy95"
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
      "paymentPageUrl": "https://qbitpay.blockscape.co/?zh#/otc/payment?tradeId=uTifOuTy95DTSifOy95"
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
    "id": "5uTSifDOuTy9i",
    "object": "charge",
    "createdAt": "2019-02-25T16:13:12.278Z",
    "paid": false,
    "status": "pending",
    "amount": 1000,
    "currency": "USD",
    "externalOrderId": "DTSifOuTy95ui",
    "paymentPageUrl": "https://qbitpay.blockscape.co/?zh#/otc/payment?tradeId=DTSifOuTy95ifOuTy95"
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
