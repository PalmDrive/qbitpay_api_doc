# Accounts

## Account对象概述

> 对象示例

```json
{
  "feeRates": {
    "payout": 0.005,
    "pay": 0,
    "withdrawToken": 0,
    "trade": 0
  },
  "limit": {
    "pay": {
        "currency": "USDT",
        "min": 5,
        "max": 10000
    },
    "payout": {
        "currency": "USDT",
        "min": 200
    },
    "withdrawToken": {
        "currency": "USDT",
        "min": 200
    }
  }
}
```

账户的费率和额度限制。有feeRates和limit字段。

feeRates字段的值是一个对象，详情如下：

属性	    |     描述
--------  | -----------
payout *number* | 分发费率
pay *number* | 入金收单的费率
trade *number* | 交易费率
withdrawToken *number* | 提币费率

limit字段的值是一个对象，详情如下：

属性	    |     描述
--------  | -----------
payout *object* | 分发的额度限制 { currency <string>, min <number> }
pay *object* | 入金收单的额度限制 { currency <string>, min <number>, max <number> }
withdrawToken *object* | 提币的额度限制 { currency <string>, min <number> }

## 查询Account对象

> 请求示例

```shell
curl "https://matrix-content-s.ailingual.cn/api/v1/accounts/show"
  -u "my_api_key:"
```

> 返回数据

```json
{
  "feeRates": {
    "payout": 0.005,
    "pay": 0,
    "withdrawToken": 0,
    "trade": 0
  },
  "limit": {
    "pay": {
        "currency": "USDT",
        "min": 5,
        "max": 10000
    },
    "payout": {
        "currency": "USDT",
        "min": 200
    },
    "withdrawToken": {
        "currency": "USDT",
        "min": 200
    }
  }
}
```

### 返回

返回目前账户的account 对象或者一个错误，详见 错误。
