# C2C委托 API 文档

**基础 URL：** `https://m.biyiex.top/api`

**认证方式：** 请求头中添加 `token` 参数，示例：
```
token: UAT20283084048911319041772421382530
```

---

## 目录

1. [创建委托单](#1-创建委托单)
2. [获取委托单详情](#2-获取委托单详情)
3. [按委托单号获取详情](#3-按委托单号获取详情)
4. [获取用户委托列表](#4-获取用户委托列表)
5. [取消委托](#5-取消委托)
6. [获取委托统计信息](#6-获取委托统计信息)

---

## 1. 创建委托单

**请求方式：** POST

**请求路径：** `/c2c/entrust/create`

**描述：** 创建新的C2C委托单，支持买入和卖出两种方向

**请求头：**

| 字段 | 值 |
|------|-----|
| Content-Type | application/json |
| token | 你的token值 |

**请求体（JSON 对象）：**

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| direction | string | ✓ | 委托方向：`buy`（买入）或 `sell`（卖出） |
| virtualCoin | string | ✓ | 虚拟币编码，如：`BTC`、`ETH`、`USDT` |
| faitCoin | string | ✓ | 法币编码，如：`CNY`、`USD` |
| totalAmount | number | ✓ | 委托数量，精确到小数点后8位 |
| buyRate | number | 条件 | 购买汇率，当 `direction=buy` 时必填 |
| sellRate | number | 条件 | 出售汇率，当 `direction=sell` 时必填 |
| supportedPaymentMethods | string | 否 | 支持的支付方式，多个用逗号分隔，如：`alipay,wx,bank` |

**请求示例（cURL）- 创建买入委托：**
```bash
curl --location --request POST 'https://m.biyiex.top/api/c2c/entrust/create' \
--header 'Content-Type: application/json' \
--header 'token: UAT20283084048911319041772421382530' \
--data '{
  "direction": "buy",
  "virtualCoin": "BTC",
  "faitCoin": "CNY",
  "totalAmount": 0.5,
  "buyRate": 350000,
  "supportedPaymentMethods": "alipay,wx,bank"
}'
```

**请求示例（cURL）- 创建卖出委托：**
```bash
curl --location --request POST 'https://m.biyiex.top/api/c2c/entrust/create' \
--header 'Content-Type: application/json' \
--header 'token: UAT20283084048911319041772421382530' \
--data '{
  "direction": "sell",
  "virtualCoin": "BTC",
  "faitCoin": "CNY",
  "totalAmount": 0.5,
  "sellRate": 345000,
  "supportedPaymentMethods": "alipay,wx",
  "merchantCode": "MCH20260101001"
}'
```

**响应示例：**
```json
{
  "code": 200,
  "msg": "委托单创建成功",
  "data": "C2C20260303153045123XX"
}
```

**响应字段说明：**

| 字段 | 类型 | 说明 |
|------|------|------|
| code | int | 返回码，200 表示成功 |
| msg | string | 返回消息 |
| data | string | 委托单号（唯一标识，格式：C2C+yyyyMMddHHmmssSSS+2位随机数） |

---

## 2. 获取委托单详情

**请求方式：** GET

**请求路径：** `/c2c/entrust/detail/{id}`

**描述：** 根据委托ID获取委托单完整信息

**请求参数：**

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| id | number | ✓ | 委托ID（路径参数） |

**请求示例（cURL）：**
```bash
curl --location --request GET 'https://m.biyiex.top/api/c2c/entrust/detail/1' \
--header 'token: UAT20283084048911319041772421382530'
```

**响应示例：**
```json
{
  "code": 200,
  "msg": "查询成功",
  "data": {
    "id": 1,
    "entrustNo": "C2C1709548274392A1B2C3D4E",
    "member": "USER20260101001",
    "merchantCode": "MCH20260101001",
    "direction": "buy",
    "virtualCoin": "BTC",
    "faitCoin": "CNY",
    "totalAmount": "0.5",
    "filledAmount": "0.2",
    "unfilledAmount": "0.3",
    "tradedCoinAmount": "0",
    "totalPrice": "175000",
    "filledPrice": "70000",
    "unfilledPrice": "105000",
    "buyRate": "350000",
    "sellRate": null,
    "feeRate": "0.01",
    "feeAmount": "70",
    "paymentMethod": null,
    "supportedPaymentMethods": "alipay,wx,bank",
    "status": "PARTIAL",
    "cancelReason": null,
    "entrustTime": "2026-03-02 10:30:00",
    "filledTime": null,
    "expireTime": null,
    "createdAt": "2026-03-02 10:30:00",
    "updatedAt": "2026-03-02 10:35:00",
    "remark": null
  }
}
```

**响应字段说明：**

| 字段 | 类型 | 说明 |
|------|------|------|
| id | number | 委托ID |
| entrustNo | string | 委托单号 |
| member | string | 会员号 |
| merchantCode | string | 商户编码 |
| direction | string | 委托方向（buy/sell） |
| virtualCoin | string | 虚拟币 |
| faitCoin | string | 法币 |
| totalAmount | number | 委托总数量 |
| filledAmount | number | 已成交数量 |
| unfilledAmount | number | 未成交数量 |
| totalPrice | number | 委托总金额 |
| filledPrice | number | 已成交金额 |
| unfilledPrice | number | 未成交金额 |
| buyRate | number | 购买汇率 |
| sellRate | number | 出售汇率 |
| feeRate | number | 手续费率(%) |
| feeAmount | number | 已扣手续费 |
| supportedPaymentMethods | string | 支持的支付方式 |
| status | string | 委托状态（UNFILLED/PARTIAL/ALL/CANCEL） |
| entrustTime | string | 委托时间 |
| createdAt | string | 创建时间 |
| updatedAt | string | 更新时间 |

---

## 3. 按委托单号获取详情

**请求方式：** GET

**请求路径：** `/c2c/entrust/detail-by-no/{entrustNo}`

**描述：** 根据委托单号获取委托单完整信息

**请求参数：**

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| entrustNo | string | ✓ | 委托单号（路径参数） |

**请求示例（cURL）：**
```bash
curl --location --request GET 'https://m.biyiex.top/api/c2c/entrust/detail-by-no/C2C1709548274392A1B2C3D4E' \
--header 'token: UAT20283084048911319041772421382530'
```

**响应示例：** 同获取委托单详情

---

## 4. 获取用户委托列表

**请求方式：** GET

**请求路径：** `/c2c/entrust/list`

**描述：** 获取当前用户的委托列表，可按方向筛选

**请求参数：**

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| member | string | ✓ | 会员号 |
| direction | string | 否 | 委托方向（buy/sell），不填表示查询全部 |

**请求示例（cURL）：**
```bash
# 查询所有委托
curl --location --request GET 'https://m.biyiex.top/api/c2c/entrust/list' \
--header 'token: UAT20283084048911319041772421382530' \
--data-urlencode 'member=USER20260101001'

# 只查询买入委托
curl --location --request GET 'https://m.biyiex.top/api/c2c/entrust/list?direction=buy' \
--header 'token: UAT20283084048911319041772421382530' \
--data-urlencode 'member=USER20260101001'
```

**响应示例：**
```json
{
  "code": 200,
  "msg": "查询成功",
  "data": [
    {
      "id": 1,
      "entrustNo": "C2C1709548274392A1B2C3D4E",
      "direction": "buy",
      "virtualCoin": "BTC",
      "faitCoin": "CNY",
      "status": "PARTIAL",
      "totalAmount": "0.5",
      "filledAmount": "0.2",
      "unfilledAmount": "0.3",
      "buyRate": "350000",
      "entrustTime": "2026-03-02 10:30:00"
    },
    {
      "id": 2,
      "entrustNo": "C2C1709548274392B1B2C3D4F",
      "direction": "sell",
      "virtualCoin": "ETH",
      "faitCoin": "CNY",
      "status": "UNFILLED",
      "totalAmount": "10",
      "filledAmount": "0",
      "unfilledAmount": "10",
      "sellRate": "18000",
      "entrustTime": "2026-03-02 11:00:00"
    }
  ]
}
```

---

## 5. 取消委托

**请求方式：** POST

**请求路径：** `/c2c/entrust/cancel/{id}`

**描述：** 取消指定的委托单（仅可取消待成交或部分成交的委托）

**请求参数：**

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| id | number | ✓ | 委托ID（路径参数） |
| member | string | ✓ | 会员号 |
| cancelReason | string | 否 | 取消原因 |

**请求示例（cURL）：**
```bash
curl --location --request POST 'https://m.biyiex.top/api/c2c/entrust/cancel/1' \
--header 'token: UAT20283084048911319041772421382530' \
--data-urlencode 'member=USER20260101001' \
--data-urlencode 'cancelReason=价格不满意'
```

**响应示例：**
```json
{
  "code": 200,
  "msg": "委托取消成功",
  "data": null
}
```

---

## 6. 获取委托统计信息

**请求方式：** GET

**请求路径：** `/c2c/entrust/stats`

**描述：** 获取当前用户的委托统计信息（总数、买入数、卖出数）

**请求参数：**

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| member | string | ✓ | 会员号 |

**请求示例（cURL）：**
```bash
curl --location --request GET 'https://m.biyiex.top/api/c2c/entrust/stats' \
--header 'token: UAT20283084048911319041772421382530' \
--data-urlencode 'member=USER20260101001'
```

**响应示例：**
```json
{
  "code": 200,
  "msg": "查询成功",
  "data": {
    "totalEntrusts": 5,
    "buyEntrusts": 3,
    "sellEntrusts": 2
  }
}
```

---

## 委托状态说明

| 状态 | 代码 | 说明 |
|------|------|------|
| 待成交 | UNFILLED | 刚创建的委托，暂未有成交 |
| 部分成交 | PARTIAL | 委托已有部分成交，仍有未成交部分 |
| 已成交 | ALL | 委托全部成交完成 |
| 已取消 | CANCEL | 用户主动取消的委托 |
| 已过期 | EXPIRED | 委托超过有效期自动过期 |

---

## 错误响应

所有接口均可能返回以下错误码：

| 错误码 | 说明 |
|------|------|
| 200 | 请求成功 |
| 400 | 请求参数错误 |
| 401 | 认证失败，请检查 token 是否有效 |
| 500 | 服务器内部错误 |

**错误响应示例：**
```json
{
  "code": 400,
  "msg": "委托数量必须大于0",
  "data": null
}
```

---

## 业务规则

1. **创建委托**
   - 买入委托必须填写 `buyRate`（购买汇率）
   - 卖出委托必须填写 `sellRate`（出售汇率）
   - 委托数量必须大于0
   - 卖出委托时需要账户有足够余额，会自动冻结相应币种

2. **取消委托**
   - 只有待成交（UNFILLED）和部分成交（PARTIAL）状态的委托可以取消
   - 取消后会自动解冻冻结的币种
   - 已成交部分不会被撤销

3. **支付方式**
   - 支持多种支付方式：支付宝（alipay）、微信（wx）、银行卡（bank）
   - 可在创建委托时指定支持的支付方式
   - 买卖双方需要商定具体的支付方式才能成交

---

## 集成建议

1. 创建委托时检查账户余额和冻结资金
2. 定期轮询委托状态以获得最新信息
3. 取消委托前确认委托未成交或部分成交
4. 处理好异常情况（网络错误、服务器错误等）的重试逻辑
