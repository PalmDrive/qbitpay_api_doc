# BalanceTransactions

## BalanceTransaction对象概述

> 对象示例

```json
{
  "id": "55ba126aa38a5c808t8deb0d",
  "object": "balanceTransaction",
  "currency": "USD",
  "amount": 1000.5,
  "availableOn": "2019-02-23T16:13:12.278Z",
  "createdAt": "2019-02-22T16:13:12.278Z",
  "type": "charge",
  "source": "55ba12b0d6aa38a5c808t8de",
  "status": "available",
  "description": "This is a memo",
  "fee": 1.1,
  "net": 999.4
}
```

BalanceTransaction对象表示用户账户变化的历史记录。

属性	    |     描述
--------  | -----------
id *string* | 由 Qbit Pay 生成的对象 ID。
object *string* | 值为 "balance"。
createdAt *string* | 创建时的时间戳, 时区为UTC。
available *string* | 状态变成可结算提现的时间戳, 时区为UTC。
status *string* | 值可以是pending, available
type *string* | 类型，可以是charge, payout, payout_cancel, payout_failure等
source *string* | 这个记录所对应的Qbit Pay对象，如charge对象，和type相对应。
currency *string* | 币种
amount *number* | 不包含手续费的金额数
fee *number* | 手续费
net *number* | 净金额数，包含了手续费
description *string* | 备注描述

## 查询BalanceTransaction对象

> 请求示例

```shell
curl "https://matrix-content-s.ailingual.cn/api/v1/balanceTransactions/55ba126aa38a5c808t8deb0d"
  -u "my_api_key:"
```

> 返回数据

```json
{
  "id": "55ba126aa38a5c808t8deb0d",
  "object": "balanceTransaction",
  "currency": "USD",
  "amount": 1000.5,
  "availableOn": "2019-02-23T16:13:12.278Z",
  "createdAt": "2019-02-22T16:13:12.278Z",
  "type": "charge",
  "source": "55ba12b0d6aa38a5c808t8de",
  "status": "available",
  "description": "This is a memo",
  "fee": 1.1,
  "net": 999.4
}
```

通过BalanceTransaction对象的id查询对象。

请求参数	|    描述
---- | --------
BALANCE_TRANSACTION_ID *REQUIRED string* | 查询的 balanceTransaction 对象 id。

### 返回

返回一个已存在的 balanceTransaction 对象或者一个错误，详见 错误。

## 查询BalanceTransaction列表

> 请求示例

```shell
curl "https://matrix-content-s.ailingual.cn/api/v1/balanceTransactions"
  -u "my_api_key:"
```

> 返回数据

```json
{
  "object": "list",
  "url": "/api/v1/balanceTransactions",
  "data": [
    {
      "id": "55ba126aa38a5c808t8deb0d",
      "object": "balanceTransaction",
      "currency": "USD",
      "amount": 1000.5,
      "availableOn": "2019-02-23T16:13:12.278Z",
      "createdAt": "2019-02-22T16:13:12.278Z",
      "type": "charge",
      "source": "55ba12b0d6aa38a5c808t8de",
      "status": "available",
      "description": "This is a memo",
      "fee": 1.1,
      "net": 999.4
    }
  ]
}
```

返回 balanceTransaction 对象的一个列表。列表是按创建时间进行排序，总是将最新的对象显示在最前。

请求参数 |	描述
------- | -------
limit *optional int* | 限制每页可以返回多少对象，限制范围是从 1~100 项，默认是 10 项。
starting_after *optional string* | 在分页时使用的指针，决定了列表的第一项从何处开始。假设你的一次请求返回列表的最后一项的 id 是 obj_end，你可以使用 starting_after = obj_end 去获取下一页。
ending_before *optional string* | 在分页时使用的指针，决定了列表的最末项在何处结束。假设你的一次请求返回列表的第一项的 id 是 obj_start，你可以使用 ending_before = obj_start 去获取上一页。
source *optional string* | 和记录相关的对象的id，source存在时type也必须要同时存在
type *optional string* | 记录类型，值可以是charge，payout
status *optional* string | 状态，值可以是pending，available

### 返回

返回一个 balanceTransaction 对象列表或者一个错误，详见 错误
