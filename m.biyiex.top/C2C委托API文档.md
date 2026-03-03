# C2C 委托 API（文档仅包含项目中实际存在的接口）

基础 URL: https://m.biyiex.top/api
认证：在请求头中加入 `token`，示例：

    token: UAT20283084048911319041772421382530

说明：此文档仅包含 `coincasso-web` 模块中 `com.sdt.web.controller.C2cEntrustController` 实际存在的接口。目录中的链接可以跳转到对应接口节。

---

## 目录

- [创建委托单 - POST /c2c/entrust/create](#创建委托单)
- [获取进行中委托单列表 - GET /c2c/entrust/list-detailed](#获取进行中委托单列表)
- [按 ID 查询（单条） - GET /c2c/entrust/query?id=...](#按-id-查询单条)
- [按委托单号查询 - GET /c2c/entrust/detail-by-no/{entrustNo}](#按委托单号查询)
- [购买虚拟币（下单/成交） - POST /c2c/entrust/trade/buy](#购买虚拟币)
- [取消委托 - POST /c2c/entrust/cancel/{id}](#取消委托)
- [委托统计 - GET /c2c/entrust/stats](#委托统计)

---

## 公共字段说明（常出现在响应体）

| 参数 | 类型 | 说明 |
|------|------|------|
| id | number | 委托数据库主键 |
| entrustNo | string | 委托单号 |
| member | string | 会员号 |
| merchantCode | string | 商户编码（如适用） |
| direction | string | 方向：`BUY` 或 `SELL` |
| virtualCoin | string | 虚拟币（原 main_coin） |
| faitCoin | string | 法币（原 trad_coin / fiat） |
| totalAmount | number | 委托数量 |
| filledAmount | number | 已成交数量 |
| unfilledAmount | number | 未成交数量 |
| totalPrice | number | 委托总价 |
| buyRate | number | 买入汇率（买方） |
| sellRate | number | 卖出汇率（卖方） |
| supportedPaymentMethods | string | 支持的支付/到款方式，逗号分隔，例如 `ALIPAY,WX,BANK` |
| paymentMethod | string | 本次成交使用的支付方式 |
| status | string | 委托总体状态（如 `PENDING`, `PARTIAL`, `FILLED`, `CANCELLED`, `EXPIRED`） |
| buyer_status | string | 买方状态（`PENDING`,`PAID`,`CONFIRMED`） |
| seller_status | string | 卖方状态（`PENDING`,`DELIVERED`,`CONFIRMED`） |
| createdAt / writedate | string | 创建时间，格式 `yyyy-MM-dd HH:mm:ss` |


---

## 创建委托单

POST /c2c/entrust/create

说明：创建一条委托单，服务器从请求头获取 `member`（使用内部工具 HelpOpenUser.getMemberNo(request)）。

请求头示例：

curl 示例：

```bash
curl -X POST "https://m.biyiex.top/api/c2c/entrust/create" \
  -H "Content-Type: application/json" \
  -H "token: UAT20283084048911319041772421382530" \
  -d '{
    "direction":"BUY",
    "virtualCoin":"BTC",
    "faitCoin":"USD",
    "totalAmount": "0.01",
    "buyRate": "50000",
    "sellRate": "", 
    "supportedPaymentMethods": "ALIPAY,WX,BANK"
}'
```

请求参数（JSON body）：

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| direction | string | 是 | `BUY` 或 `SELL` |
| virtualCoin | string | 是 | 虚拟币代码，例如 `BTC` |
| faitCoin | string | 是 | 法币代码，例如 `USD` |
| totalAmount | string/number | 是 | 委托数量 |
| buyRate | string/number | 否 | 买入汇率（direction=BUY 时可能使用） |
| sellRate | string/number | 否 | 卖出汇率（direction=SELL 时可能使用） |
| supportedPaymentMethods | string | 否 | 支持的支付方式，逗号分隔（例如 `ALIPAY,WX,BANK`） |

响应示例：

| 字段 | 类型 | 说明 |
|------|------|------|
| data | string | 成功返回生成的 `entrustNo` |
| code | number | 状态码（0 表示成功，按项目约定） |
| msg | string | 说明信息 |

---

## 获取进行中委托单列表

GET /c2c/entrust/list-detailed

说明：分页获取委托单（controller 方法名 list-detailed）。默认返回包含商户信息及成交明细的分页结果。

curl 示例：

```bash
curl -G "https://m.biyiex.top/api/c2c/entrust/list-detailed" \
  -H "token: UAT20283084048911319041772421382530" \
  --data-urlencode "direction=BUY" \
  --data-urlencode "virtualCoin=BTC" \
  --data-urlencode "faitCoin=USD" \
  --data-urlencode "page=1" \
  --data-urlencode "limit=10"
```

请求参数（query）：

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| direction | string | 否 | `BUY` / `SELL` |
| virtualCoin | string | 否 | 虚拟币 |
| faitCoin | string | 否 | 法币 |
| page | int | 否 | 第几页，默认 1 |
| limit | int | 否 | 每页条数，默认 10 |

响应：分页结构（示例字段）：

| 字段 | 类型 | 说明 |
|------|------|------|
| total | number | 总条数 |
| records | array | 列表，每项包含公共字段（上方表格）以及 merchantName / merchantAvatar 等扩展信息 |

---

## 按 ID 查询（单条）

GET /c2c/entrust/query?id={id}

说明：controller 中存在 `query` 方法，接收 `id` 参数并返回对应委托的 DTO。

curl 示例：

```bash
curl -G "https://m.biyiex.top/api/c2c/entrust/query" \
  -H "token: UAT20283084048911319041772421382530" \
  --data-urlencode "id=123"
```

请求参数：

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| id | number | 是 | 委托 ID |

响应字段：返回一条委托 DTO，包含公共字段与商户、成交明细等（参见公共字段表）。

---

## 按委托单号查询

GET /c2c/entrust/detail-by-no/{entrustNo}

说明：按委托单号返回一条完整委托信息。

curl 示例：

```bash
curl -G "https://m.biyiex.top/api/c2c/entrust/detail-by-no/ENTRUST20260303A" \
  -H "token: UAT20283084048911319041772421382530"
```

响应字段：委托完整 DTO，包含公共字段与详情。

---

## 购买虚拟币（trade buy / 下单）

POST /c2c/entrust/trade/buy

说明：该接口在 controller 中存在，用于用户在某条委托上发起购买行为（传入委托 id、交易数量、支付方式等）。

curl 示例：

```bash
curl -X POST "https://m.biyiex.top/api/c2c/entrust/trade/buy" \
  -H "Content-Type: application/json" \
  -H "token: UAT20283084048911319041772421382530" \
  -d '{
    "id": 123,
    "tradeAmount": "0.005",
    "paymentMethod": "ALIPAY"
}'
```

请求参数（JSON body）：

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| id | number | 是 | 目标委托的数据库 ID |
| tradeAmount | string/number | 是 | 本次购买数量 |
| paymentMethod | string | 是 | 本次使用的支付/到款方式，必须是项目中 `C2cCollWayEnum` 支持的值（例如 `ALIPAY`） |

响应：

| 字段 | 类型 | 说明 |
|------|------|------|
| code | number | 状态码 |
| msg | string | 提示信息 |
| data | null/string | 如成功可返回消息或新交易编号 |

错误处理：
- 如果参数校验不通过，接口会抛出业务异常（ExWebException），请按项目统一错误格式处理。

---

## 取消委托

POST /c2c/entrust/cancel/{id}

curl 示例：

```bash
curl -X POST "https://m.biyiex.top/api/c2c/entrust/cancel/123" \
  -H "token: UAT20283084048911319041772421382530" \
  --data-urlencode "cancelReason=用户主动取消"
```

请求参数：

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| id | path | 是 | 委托 ID |
| cancelReason | string | 否 | 取消原因 |

响应：返回标准成功响应（R.success）。

---

## 委托统计

GET /c2c/entrust/stats

说明：返回当前用户的委托统计（总委托数、买方委托数、卖方委托数）。

curl 示例：

```bash
curl -G "https://m.biyiex.top/api/c2c/entrust/stats" \
  -H "token: UAT20283084048911319041772421382530"
```

响应字段示例：

| 字段 | 类型 | 说明 |
|------|------|------|
| totalEntrusts | number | 总委托数 |
| buyEntrusts | number | 买委托数 |
| sellEntrusts | number | 卖委托数 |

---

## 备注

- 本文档严格以 `C2cEntrustController` 中当前存在的方法为准；如果后续 Controller 增删方法，请同步更新本文件。
- 认证头使用 `token` 字段（不是 Bearer）。
- 响应中 `code/msg/data` 结构按项目统一的 `R` 类封装（`R.success()` 返回的结构），异常会以业务异常 `ExWebException` 或全局异常处理器返回标准错误结构。


