# Balances

## Balance对象概述

> 对象示例

```json
{
  "id": "5c808t8d55ba126aa38aeb0d",
  "object": "balance",
  "currency": "USD",
  "available": 1000.5,
  "pending": 100.5
}
```

Balance对象表示用户账户中的余额，分可结算部分和处理中部分。

属性	    |     描述
--------  | -----------
id *string* | 由 Qbit Pay 生成的对象 ID。
object *string* | 值为 "balance"。
available *number* | 可结算提现的账户余额
pending *number* | 在处理中，还不能结算提现的账户余额。
currency *string* | 结算的币种

## 查询Balance对象

> 请求示例

```shell
curl "https://matrix-content-s.ailingual.cn/api/v1/balances/show"
  -u "my_api_key:"
```

> 返回数据

```json
{
  "id": "5c808t8d55ba126aa38aeb0d",
  "object": "balance",
  "currency": "USD",
  "available": 1000.5,
  "pending": 100.5
}
```

### 返回

返回当前账户的 balance 对象或者一个错误，详见 错误。
