# Accounts

## Account

> Object example

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

The fee rates and amount limitation are in the account object. 

The value of feeRates field is an object：

field	    |     desc
--------  | -----------
payout *number* | payout fee rate
pay *number* | process payment fee rate
trade *number* | trade fee rate
withdrawToken *number* | withdraw fee rate

The value of limit field is an object:

field	    |     desc
--------  | -----------
payout *object* | payout amount limit { currency <string>, min <number> }
pay *object* | payment amount limit { currency <string>, min <number>, max <number> }
withdrawToken *object* | withdraw crypo token amount limit { currency <string>, min <number> }

## Query Account

> Request example

```shell
curl "https://matrix-content-s.ailingual.cn/api/v1/accounts/show"
  -u "my_api_key:"
```

> Response

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

### Response

Return the account object or an error.
