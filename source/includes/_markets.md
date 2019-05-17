# Markets

## Ticker对象概述

> 对象示例

```json
{
  "symbol": "USDT/CNY",
  "timestamp": 1558062945801,
  "ask": 6.97
}
```

Ticker对象记录当时交易对的价格

属性	    |     描述
--------  | -----------
symbol *string* | 交易对
timestamp *number* | 毫秒时间戳
ask *number* | 市卖价，对应自身是买家
bid *number* | 市买价，对应自身是卖家

## 查询Ticker

```shell
curl "https://matrix-content-s.ailingual.cn/api/v1/markets/ticker?symbol=USDT/CNY&side=ask"
  -u "my_api_key:"
```

> 返回数据

```json
{
  "symbol": "USDT/CNY",
  "timestamp": 1558062945801,
  "ask": 6.97
}
```

可以用此接口查询当前交易对的价格。

请求参数	|    描述
---- | --------
symbol *REQUIRED string* | 查询的交易对。
side *REQUIRED string* | 查询的市买/卖价。值ask表示市卖价，bid表示市买价

### 返回

返回一个 ticker 对象或者一个错误，详见 错误。
