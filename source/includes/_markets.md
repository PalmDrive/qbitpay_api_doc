# Markets

## Ticker

> Object example

```json
{
  "symbol": "USDT/CNY",
  "timestamp": 1558062945801,
  "ask": 6.97
}
```

Ticker shows the price of the trading pair. The price is valid in 30 seconds.

field	    |     desc
--------  | -----------
symbol *string* | trading pair
timestamp *number* | timestamp in milisecond
ask *number* | ask price to buyer
bid *number* | bid price to seller

## Query Ticker

```shell
curl "https://api.qbitnetwork.com/api/v1/markets/ticker?symbol=USDT/CNY&side=ask"
  -u "my_api_key:"
```

> Response

```json
{
  "symbol": "USDT/CNY",
  "timestamp": 1558062945801,
  "ask": 6.97
}
```

Query the current price of the trading pair.

params	|   desc
---- | --------
symbol *REQUIRED string* | trading pair
side *REQUIRED string* | available values: ask, bid

### Response

Return a ticker object or an error.
