# PaymentMethods

## PaymentMethod

> Object example

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

The payment method when the user makes the payment or receives withdraw/payout. The fields are shown as below and may vary based on the payment type.

field	    |     desc
--------  | -----------
object *string* | value is 'paymentMethod'
type *string* | payment method type， available values: Alipay, PayPal, Yunshanfu, bankAccount, Coins.ph, GCash, wallet
currency *string* | the currency to pay, withdraw or payout
status *string* | status
typeLabel *object* | Chinese and English label indicating the payment method type
alipayQRCodeUrl *string* | when the payment method type is Alipay, use this to draw the QR code
qrCodeImgUrl *string* | when the payment method type is PayPal, Yunshanfu, it shows the QR code image
bankName *string* | bank name
accountNum *string* | account name, for bank or other payment methods
bankRoutingNum *string* | bank's routing number
bankDetailName *string* | bank branch name
bankAddr *string* | bank address
city *string* | recipient's city
state *string* | recipient's state / province
country *string* | recipient's country
postalCode *string* | postal code
addr *string* | when type is wallet， it is wallet address; when type is bankAccount，it is recipient's address
swift *string* | SWIFT code
