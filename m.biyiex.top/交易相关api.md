# 交易相关 API

**基础 URL：** `https://m.biyiex.top/api`

**认证方式：** 请求头中添加 `token` 参数，示例：
```
token: UAT20283084048911319041772421382530
```

---

## 目录

1. [获取法币交易币种列表](#1-获取法币交易币种列表)
2. [获取可交易虚拟币列表](#2-获取可交易虚拟币列表)

---

## 1. 获取法币交易币种列表

**请求方式：** GET

**请求路径：** `/c2c/c2c_fait_coin_list`

**描述：** 查看所有可用于 C2C 交易的法币币种列表

**请求参数：** 无

**请求示例（cURL）：**
```bash
curl --location --request GET 'https://m.biyiex.top/api/c2c/c2c_fait_coin_list' \
  --header 'token: UAT20283084048911319041772421382530'
```

**请求示例（Postman）：**
- Method: GET
- URL: `https://m.biyiex.top/api/c2c/c2c_fait_coin_list`
- Headers:
  - Key: `token`
  - Value: `UAT20283084048911319041772421382530`

**响应示例：**
```json
{
  "code": 200,
  "msg": "success",
  "data": [
    {
      "id": 1,
      "fiatCode": "CNY",
      "fiatName": "人民币",
      "status": "ENABLED",
      "sort": 1
    },
    {
      "id": 2,
      "fiatCode": "USD",
      "fiatName": "美元",
      "status": "ENABLED",
      "sort": 2
    }
  ]
}
```

**响应字段说明：**

| 字段 | 类型 | 说明 |
|------|------|------|
| code | int | 返回码，200 表示成功 |
| msg | string | 返回消息 |
| data | array | 法币列表，对应 `FiatCurrencyPo` |
| data[].id | int | 法币ID |
| data[].fiatCode | string | 法币编码（如 CNY、USD） |
| data[].fiatName | string | 法币名称 |
| data[].status | string | 状态（ENABLED/DISABLED） |
| data[].sort | int | 排序号 |

---

## 2. 获取可交易虚拟币列表

**请求方式：** GET

**请求路径：** `/c2c/c2c_trade_coin_list`

**描述：** 获取所有可用于 C2C 交易的虚拟币列表

**请求参数：** 无

**请求示例（cURL）：**
```bash
curl --location --request GET 'https://m.biyiex.top/api/c2c/c2c_trade_coin_list' \
  --header 'token: UAT20283084048911319041772421382530'
```

**请求示例（Postman）：**
- Method: GET
- URL: `https://m.biyiex.top/api/c2c/c2c_trade_coin_list`
- Headers:
  - Key: `token`
  - Value: `UAT20283084048911319041772421382530`

**响应示例：**
```json
{
  "code": 200,
  "msg": "success",
  "data": [
    {
      "id": 1,
      "currencyCode": "BTC",
      "currencyName": "Bitcoin",
      "icon": "https://example.com/btc.png",
      "status": "ENABLED",
      "sort": 1
    },
    {
      "id": 2,
      "currencyCode": "ETH",
      "currencyName": "Ethereum",
      "icon": "https://example.com/eth.png",
      "status": "ENABLED",
      "sort": 2
    }
  ]
}
```

**响应字段说明：**

| 字段 | 类型 | 说明 |
|------|------|------|
| code | int | 返回码，200 表示成功 |
| msg | string | 返回消息 |
| data | array | 虚拟币列表，对应 `BlCurrencyPo` |
| data[].id | int | 虚拟币ID |
| data[].currencyCode | string | 币种编码（如 BTC、ETH） |
| data[].currencyName | string | 币种名称 |
| data[].icon | string | 币种图标 URL |
| data[].status | string | 状态（ENABLED/DISABLED） |
| data[].sort | int | 排序号 |

---

## 错误响应

所有接口均可能返回以下错误码：

| 错误码 | 说明 |
|------|------|
| 401 | 认证失败，请检查 token 是否有效 |
| 500 | 服务器内部错误 |

**错误响应示例：**
```json
{
  "code": 401,
  "msg": "Unauthorized",
  "data": null
}
```

