---
title:  Qbit Pay 开发文档

language_tabs: # must be one of https://git.io/vQNgJ
  - shell

toc_footers:
  - Made with love Qbit Pay

includes:
  - charges
  - webhooks

search: false
---

# 简介

```shell
接口地址: https://matrix-content-s.ailingual.cn
```

Qbit Pay API 采用 REST 风格设计。所有接口请求地址都是可预期的以及面向资源的。使用规范的 HTTP 响应代码来表示请求结果的正确或错误信息。使用 HTTP 内置的特性，如 HTTP Authentication 和 HTTP 请求方法让接口易于理解。所有的 API 请求都会以规范友好的 JSON 对象格式返回（包括错误信息）。

# 快速入门

这里将介绍如何利用 Qbit Pay, 为商户提供支付服务。

流程如下：

1. 获得api key，设置 Webhook 回调接口。

2. 请求获得 access token (详见“认证”)。

3. 创建一个charge, 利用 charge.paymentPageUrl （详见“Charges - 创建Charge对象”）和 access token 调起支付H5页面。

4. 回调接口将会收到关于 charge 的事件推送，如 charge.succeeded。注意去验证签名（详见“Webhooks - 签名验证”）。

# 认证

> 请求示例:

```shell
curl "https://matrix-content-s.ailingual.cn"
  -u "my_api_key:"

请将示例中的 my_api_key 替换成你自己的 API Key。

请求获取access token:

curl "https://matrix-content-s.ailingual.cn/api/v1/accessToken"
  -u "my_api_key:"
```

> 接口返回 JSON 数据如下:

```json
{
  "accessToken": "eyJhbGciOiUzI1NiIsInR5cCI6IkpXVCJ9.eyJfaWQiOiI1YjhlYTFhNWVhOWViNzYyNjQyNTBkZGEiLCJpc0FkbWluIjp0cnVlLCJhdXRoQ29kZSI6IjIzOTY2OSIsImlhdCI6MTU1MTOTU5OSwiZXhwIjoxNTUzNzAxNTk5fQ.1Q7R9sXdEq1CziLroHekBDVQ4NLu1OtAhswHJECjU"
}
```

在调用 API 时，必须提供 API Key 作为每个请求的身份验证。你可以在管理平台内管理你的 API Key。API Key 是商户在 Qbit Pay 系统中的身份标识，请安全存储，确保其不要被泄露。

Qbit Pay API 使用 [HTTP Basic Auth](https://en.wikipedia.org/wiki/Basic_access_authentication) 进行认证。 将 API Key 作为 basic auth 的用户名。不需要填写密码。

此外，调起的支付H5页面需要用bearer auth。先请求接口获取access token，再将access token放入到Authorization header中。

# 错误

```shell
HTTP 返回状态码

200 - OK: 一切正常。
400 - Bad Request: 一般由缺失参数，参数格式不正确等引起。
401 - Unauthorized:	没有提供正确的 API Key。
402 - Request Failed: 参数格式正确但是请求失败，一般由业务错误引起。
403 - Forbidden: 调用接口超过并发限制或无权访问。
404 - Not Found: 请求的资源不存在。
500, 502, 503, 504 - Server Errors: 服务器出错。

错误类型 (type)

invalid_request_error: 请求错误，传入了不正确的地址，参数或值。
api_error: 服务器出现的异常错误。
authentication_error:	认证错误。
rate_limit_error: 接口超过并发错误。
```

Qbit Pay API 使用 HTTP 状态码 (status code) 来表明一个 API 请求的成功或失败状态。返回 HTTP 2XX 表明 API 请求成功。返回 HTTP 4XX 表明在请求 API 时提供了错误信息，例如参数缺失、参数错误、支付渠道错误等。返回 HTTP 5XX 表明 API 请求时，Qbit Pay 服务器发生了错误。

**错误汇总**

返回属性 |	描述
------- | ------
type	| 错误类型，可以是 invalid_request_error、api_error、authentication_error 或 rate_limit_error。
message	| 返回具体的错误描述。
detail optional | 更为详细的信息

# 分页

> 请求示例

```shell
curl "https://matrix-content-s.ailingual.cn/api/v1/charges?limit=3"
  -u "my_api_key:"
```

> 返回数据

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

请求参数 |	描述
------- | -------
limit *optional* | 限制每页可以返回多少对象，限制范围是从 1~100 项，默认是 10 项。
starting_after *optional* | 在分页时使用的指针，决定了列表的第一项从何处开始。假设你的一次请求返回列表的最后一项的 id 是 obj_end，你可以使用 starting_after = obj_end 去获取下一页。
ending_before *optional* | 在分页时使用的指针，决定了列表的最末项在何处结束。假设你的一次请求返回列表的第一项的 id 是 obj_start，你可以使用 ending_before = obj_start 去获取上一页。

**返回**

返回参数 |	描述
------- | -------
object *string* | 值为 "list"
url *string* | 表明获取该列表所使用的 URL。
data *array* | 包含一个由请求参数分页后的返回元素实体。
