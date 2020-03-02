---
title:  Qbit Pay API reference

language_tabs: # must be one of https://git.io/vQNgJ
  - shell

toc_footers:
  - Made with love QbitPay

includes:
  - accounts
  - balances
  - balanceTransactions
  - markets
  - orders
  - paymentMethods
  - payouts
  - webhooks

search: false
---

# Introduction

```shell
API endpoint: https://api.qbitnetwork.com

Headers: Content-Type: application/json
```

QbitPay API is organzied round REST. The API has predictable resource-oriented URLs, accepts JSON-encoded request bodies, returns JSON-encoded responses, and uses standard HTTP response codes, authentication, and verbs.

The api endpoint and the headers are shown in the left.

# Quickstart

It illustrates a sample flow of QbitPay's payout service:

1. Setup: Get the API key and set the webhook URL.

2. Query the account API to get the wallet address.

3. Send fund to the wallet address and listen to the webhook notification. A notification will be sent to the webhook URL when the number of transaction confirmations meets the requirement.

4. Query the market ticker API to get the current price

5. Make request to the order API to create order. The order will be executed immediately and the corresponding balances are updated too.

6. Make request to the payout API to creeate a payout. The payout is executed asynchronously. A notification will be sent to the webhook URL when the payout is sent successfully or failed.

# Authentication

> Request example:

```shell
curl "https://api.qbitnetwork.com"
  -u "my_api_key:"

Please change my_api_key to your API Key。

Fetch access token:

curl "https://api.qbitnetwork.com/api/v1/accessToken"
  -u "my_api_key:"
```

> JSON response:

```json
{
  "accessToken": "eyJhbGciOiUzI1NiIsInR5cCI6IkpXVCJ9.eyJfaWQiOiI1YjhlYTFhNWVhOWViNzYyNjQyNTBkZGEiLCJpc0FkbWluIjp0cnVlLCJhdXRoQ29kZSI6IjIzOTY2OSIsImlhdCI6MTU1MTOTU5OSwiZXhwIjoxNTUzNzAxNTk5fQ.1Q7R9sXdEq1CziLroHekBDVQ4NLu1OtAhswHJECjU"
}
```
QbitPay uses API keys to authenticate requests. You can view and manage your API keys in the QbitPay Dashboard. Your API keys carry many privileges, so be sure to keep them secure! Do not share your secret API keys in publicly accessible areas such as GitHub, client-side code, and so forth.

QbitPay API uses [HTTP Basic Auth](https://en.wikipedia.org/wiki/Basic_access_authentication) to authenticate requests. You can set the API Key as basic auth username and leave the password empty.

# Errors

```shell
HTTP response status codes

200 - OK: Everything worked as expected
400 - Bad Request: The request was unacceptable, often due to missing a required parameter
401 - Unauthorized:	No valid API key provided
402 - Request Failed: The parameters were valid but the request failed
403 - Forbidden: The API key does not have permissions to perform the request
404 - Not Found: The requested resource does not exists
500, 502, 503, 504 - Server Errors: Something went wrong on QbitPay end

Error types

invalid_request_error: Invalid request errors arise when your request has invalid parameters
api_error: API errors cover any other types of problem and are extremely uncommon
authentication_error:	Failure to properly authenticate yourself
rate_limit_error: Too many requests hit the API too quickly
```

QbitPay uses conventional HTTP response codes to indicate the success or failure of an API request. In general: Codes in the 2xx range indicate success. Codes in the 4xx range indicate an error that failed given the information provided (e.g., a required parameter was omitted, a charge failed, etc.). Codes in the 5xx range indicate an error with QbitPay's servers (these are rare).

**Error object**

field |	desc
------- | ------
type	| error type，values are invalid_request_error、api_error、authentication_error or rate_limit_error
message	| description of the error
detail optional | more details

# Pagination

> Request example

```shell
curl "https://api.qbitnetwork.com/api/v1/charges?limit=3"
  -u "my_api_key:"
```

> Response

```json
{
  "object": "list",
  "url": "/api/v1/charges",
  "data": [
    {
      "id": "5uTSifDOuTy9i",
      "object": "charge",
      "createdAt": "2019-02-25T16:13:12.278Z",
      "paid": false,
      "status": "pending",
      "amount": 1000,
      "currency": "USD",
      "externalOrderId": "DTSifOuTy95ui",
      "paymentPageUrl": "https://qbitpay.blockscape.co/?zh#/otc/payment?tradeId=DTSifOuTy95ifOuTy95"
    },
    {
      "id": "OuTyTSi9i5ufD",
      "object": "charge",
      "createdAt": "2019-02-24T16:13:12.278Z",
      "paid": false,
      "status": "pending",
      "amount": 900,
      "currency": "USD",
      "externalOrderId": "DTSifOuTy95ui",
      "paymentPageUrl": "https://qbitpay.blockscape.co/?zh#/otc/payment?tradeId=OuTy95DTSififOuTy95"
    },
    {
      "id": "ifDOu5uTSTy9i",
      "object": "charge",
      "createdAt": "2019-02-23T16:13:12.278Z",
      "paid": true,
      "status": "closed",
      "amount": 950,
      "currency": "USD",
      "externalOrderId": "DTSifOuTy95ui",
      "paymentPageUrl": "https://qbitpay.blockscape.co/?zh#/otc/payment?tradeId=uTifOuTy95DTSifOy95"
    }
  ]
}
```

Most top-level API resources have support for bulk fetches via "list" API methods. These list API methods share a common structure.

QbitPay utilizes cursor-based pagination via the starting_after and ending_before parameters. Both parameters take an existing object ID value (see below) and return objects in reverse chronological order. The ending_before parameter returns objects listed before the named object. The starting_after parameter returns objects listed after the named object. These parameters are mutually exclusive -- only one of starting_after or ending_before may be used.

params |	desc
------- | -------
limit *optional* | page size from 1 to 100. The default value is 10
starting_after *optional* | the poiner in the pagination indicating from where the first item starts. For example, use the last item's id as the start_after to fetch the next page
ending_before *optional* | the pointer in the pagination indicating from where the last item ends. For example, use the first item's id as the end_before to fetch the previous page

**Response**

field |	desc
------- | -------
object *string* | value is 'list'
url *string* | The URL for accessing this list
data *array* | An array containing the actual response elements, paginated by any request parameters
