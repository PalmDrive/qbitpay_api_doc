---
title:  Qbit Pay API reference

language_tabs: # must be one of https://git.io/vQNgJ
  - shell

toc_footers:
  - Made with love QbitPay

includes:
  - paymentMethods
  - accounts
  - balances
  - balanceTransactions
  - payouts
  - markets
  - webhooks

search: false
---

# Introduction

```shell
接口地址: https://matrix-content-s.ailingual.cn
```

QbitPay API is organzied round REST. The API has predictable resource-oriented URLs, accepts JSON-encoded request bodies, returns JSON-encoded responses, and uses standard HTTP response codes, authentication, and verbs.

# Quickstart

这里将介绍如何利用 Qbit Pay, 为商户提供支付服务。

流程如下：

1. 获得api key，设置 Webhook 回调接口。

2. 请求获得 access token (详见“认证”)。

3. 创建一个charge, 利用 charge.paymentPageUrl （详见“Charges - 创建Charge对象”）和 access token 调起支付H5页面。

4. 回调接口将会收到关于 charge 的事件推送，如 charge.succeeded。注意去验证签名（详见“Webhooks - 签名验证”）。

# Authentication

> Request example:

```shell
curl "https://matrix-content-s.ailingual.cn"
  -u "my_api_key:"

Please change my_api_key to your API Key。

Fetch access token:

curl "https://matrix-content-s.ailingual.cn/api/v1/accessToken"
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

Qbit Pay API 使用 HTTP 状态码 (status code) 来表明一个 API 请求的成功或失败状态。返回 HTTP 2XX 表明 API 请求成功。返回 HTTP 4XX 表明在请求 API 时提供了错误信息，例如参数缺失、参数错误、支付渠道错误等。返回 HTTP 5XX 表明 API 请求时，Qbit Pay 服务器发生了错误。

**Error object**

field |	desc
------- | ------
type	| error type，values are invalid_request_error、api_error、authentication_error or rate_limit_error
message	| description of the error
detail optional | more details

# Pagination

> Request example

```shell
curl "https://matrix-content-s.ailingual.cn/api/v1/charges?limit=3"
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

所有的 Qbit Pay 资源都可以被 list API 方法支持。这些 list API 方法拥有相同的数据结构。Qbit Pay 是基于 cursor 的分页机制，使用参数 starting_after 来决定列表从何处开始，使用参数 ending_before 来决定列表从何处结束。

params |	desc
------- | -------
limit *optional* | 限制每页可以返回多少对象，限制范围是从 1~100 项，默认是 10 项。
starting_after *optional* | 在分页时使用的指针，决定了列表的第一项从何处开始。假设你的一次请求返回列表的最后一项的 id 是 obj_end，你可以使用 starting_after = obj_end 去获取下一页。
ending_before *optional* | 在分页时使用的指针，决定了列表的最末项在何处结束。假设你的一次请求返回列表的第一项的 id 是 obj_start，你可以使用 ending_before = obj_start 去获取上一页。

**Response**

field |	desc
------- | -------
object *string* | value is "list"
url *string* | 表明获取该列表所使用的 URL。
data *array* | 包含一个由请求参数分页后的返回元素实体。
