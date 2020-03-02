# Balances

## Balance

> Object example

```json
{
  "id": "5c808t8d55ba126aa38aeb0d",
  "object": "balance",
  "currency": "USD",
  "available": 1000.5,
  "pending": 100.5
}
```

The account balance includes the available amount and the pending amount.

field	    |     desc
--------  | -----------
id *string* | ID generated in QbitPay
object *string* | value is 'balance'
available *number* | the available amount
pending *number* | the pending amount, which is in process and cannot be used for withdrawal or payout
currency *string* | the balance currency

## Query Balance

> Request example

```shell
curl "https://api.qbitnetwork.com/api/v1/balances/show"
  -u "my_api_key:"
```

> Response

```json
{
  "id": "5c808t8d55ba126aa38aeb0d",
  "object": "balance",
  "currency": "USD",
  "available": 1000.5,
  "pending": 100.5
}
```

### Response

Return the balance object of the current account or an error object.
