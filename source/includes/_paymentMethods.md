# PaymentMethods

## PaymentMethod对象概述

> 对象示例

```json
{
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
```

用户付款时的支付方式。具体字段如下所示。并不是所有的字段都一定会存在。根据支付方式类型的不同，而有不同的字段。

属性	    |     描述
--------  | -----------
object *string* | 值为paymentMethod
type *string* | 支付方式类型，可以是Alipay, PayPal, Yunshanfu, bankAccount, Coins.ph, GCash, wallet
currency *string* | 支付货币
status *string* | 状态
typeLabel *object* | 支付类型的中英文显示名称
alipayQRCodeUrl *string* | 支付类型是Alipay时的二维码链接，渲染成二维码后可让支付宝APP扫码
qrCodeImgUrl *string* | 二维码的图片链接，如支付方式是PayPal, Yunshanfu时，会有该字段
bankName *string* | 银行名称
bankAccountNum *string* | 账号名称，可以是银行账户账号，也可以是其他支付方式的账号
bankRoutingNum *string* | 银行的routing number
bankDetailName *string* | 银行支行的具体名称
bankAddr *string* | 银行地址
city *string* | 收款人所在城市
state *string* | 收款人所在省/州
country *string* | 收款人所在国家
postalCode *string* | 收款人所在地邮编
addr *string* | 当type是wallet时，表示钱包地址；type是bankAccount时，表示收款人所在的街道地址
swift *string* | SWIFT代码
