# Webhooks

## Webhooks概述

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

为了便于客户系统或者第三方系统处理客户的交易信息，Qbit Pay 系统支持 Webhooks 功能，可以按照客户要求把特定的事件结果推送到指定的地址以便于客户做后续处理。目前支持的事件包括支付结果(其他的还处于开发中)。 以下是关于接收 Webhooks 通知的说明：

- Webhooks 通知是以 POST 形式发送的 JSON ，放在请求的 body 里，内容是 Event 对象。
- 你需要监听并接收 Webhooks 通知，接收到 Webhooks 后需要返回服务器状态码 2xx 表示接收成功，否则请返回状态码 500。
- 若返回的服务器状态码不是 2xx，Qbit Pay 服务器会不断重发通知，最多 再发 5 次。Webhooks 首次是即时推送，重试通知时间间隔为 15min。直到你正确回复状态 2xx 或者超过最大重发次数，Qbit Pay 将不再发送。

其中 Event 对象属性定义如下。

属性	    |     描述
--------  | -----------
id *string* | 由 Qbit Pay 生成的支付对象 ID。
type *string* | 事件类型。
object *string* | 值为 "event"。
createdAt *string* | 支付创建时的时间戳, 时区为UTC。
pendingWebhooks *int* | 推送未成功的 webhooks 数量。
livemode *boolean* | 事件是否发生在生产环境。
data *hash* | 绑定在事件上的数据对象。

## 签名验证

商户系统对于事件推送一定要做签名验证，防止数据泄漏导致出现“假通知”，造成资金损失。Qbit Pay会将签名放在`QbitPay-Signature` 的请求header里。

### 签名算法

> 数据示例

```json
{
  "id": "fDOuTy95uSiTi",
  "type": "charge.succeeded",
  "createdAt": "2019-02-25T16:13:12.278Z",
  "object": "event",
  "pendingWebhooks": 0,
  "livemode": true,
  "data": {
    "id": "5",
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
  }
}
```

> 示例中的数据变成的字符串是：createdAt=2019-02-25T16:13:12.278Z&data={"id":"5","o
bject":"charge","createdAt":"2019-02-25T16:13:12.278Z","paid":false,"status":"pending","amount":1000,"currency":"
USD","externalOrderId":"DTSifOuTy95ui","paymentPageUrl":"https://qbitpay.blockscape.co/?zh#/otc/payment?tradeId=D
TSifOuTy95ifOuTy95","cbUrl":"https://api.blockscape.co","redirectUrl":"https://qbitpay.blockscape.co"}&id=fDOuTy9
5uSiTi&livemode=true&object=event&pendingWebhooks=0&type=charge.succeeded

> 如果用api key "T9uTy95uSifOOuTy" 签名，获得 "EE53810FF1341779F2FF25989A67DCFC"

> 签名生成第二步示例

```javascript
stringSignTemp=stringA+"&key=T9uTy95uSifOOuTy"
//注：key为商户平台设置的api key

sign=MD5(stringSignTemp).toUpperCase()
="9A0A8659F005D6984697E2CA0A9CF3B7"
//注：MD5签名方式

sign=hash_hmac("sha256", stringSignTemp, key).toUpperCase()
="6A9AE1657590FD6257D693A078E1C3E4BB6BA4DC30B23E0EE2496E54170DACD6"
//注：HMAC-SHA256签名方式
```

第一步，设所有发送或者接收到的数据为集合M，将集合M内非空参数值的参数按照参数名ASCII码从小到大排序（字典序），使用URL键值对的格式（即key1=value1&key2=value2…）拼接成字符串stringA。

特别注意以下重要规则：

- 参数名ASCII码从小到大排序（字典序）；
- 如果参数的值为空不参与签名；
- 参数名区分大小写；
- 参数值是hash则会被json stringify；

第二步，在stringA最后拼接上api key得到stringSignTemp字符串，并对stringSignTemp进行MD5运算，再将得到的字符串所有字符转换为大写，得到sign值signValue。

## 调试

> 请求示例

```shell
curl "https://matrix-content-s.ailingual.cn/api/v1/events/test?cbUrl=https://api.blockscape.co",
  -u "my_api_key:"
```

> 推送数据

```json
{
  "id": "fDOuTy95uSiTi",
  "type": "charge.succeeded",
  "createdAt": "2019-02-25T16:13:12.278Z",
  "object": "event",
  "pendingWebhooks": 0,
  "livemode": false,
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

Qbit Pay 提供 API 供调试 Webhooks。 请求`https://matrix-content-s.ailingual.cn/api/v1/events/test` 即可获得一个和正常回调一样的推送事件， 除了 livemode 是 false，方便用于商户的接口调试。该事件推送是一次性的，如果返回不是2xx也不会重试。回调地址 cbUrl 可以以参数的形式传入，如请求示例。

该事件会即时以POST方式发送到商户设定的回调地址上，并会用商户的api key签名。
