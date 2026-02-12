# RobotController API 文档

Base URL: `https://m.ibmxi.com/api`

本档档说明 `com.sdt.web.controller.RobotController` 中的接口。文档包含目录（可点击跳转到对应接口），每个接口给出：功能简介、HTTP 方法、完整路径、认证要求、参数说明（字段/类型/必填/含义）、示例请求、示例响应、常见错误及说明。

目录

- [GET /robot/symbols](#get-robotsymbols) — 获取币种行情 / 交易对 列表（可按 K 线类型过滤）（平台支持的交易对）
- [GET /robot/last](#get-robotlast) — 查询指定交易对的最新成交（单条）
- [GET /robot/last_count](#get-robotlast_count) — 查询指定交易对最近 N 条 K 线 / 成交数据
- [GET /robot/orders](#get-robotorders) — 获取模拟/撮合生成的最新成交/委托列表（机器人订单）
- [POST /robot/user/reg](#post-robotuserreg) — 用户注册（地址 + 邀请地址 + 签名）
- [GET /robot/user/info](#get-robotuserinfo) — 根据地址查询用户信息
- [POST /robot/user/recharge](#post-robotuserrecharge) — 人工充值（后台/接口触发）
- [POST /robot/user/withdraw](#post-robotuserwithdraw) — 人工扣款 / 提现（后台/接口触发）
- [GET /robot/user/assets](#get-robotuserassets) — 查询用户资产列表（按地址，可选按币种筛选）
- [POST /robot/buyLimit](#post-robotbuylimit) — 限价买入下单接口
- [POST /robot/sellLimit](#post-robotselllimit) — 限价卖出下单接口
- [POST /robot/buyMarket](#post-robotbuymarket) — 市价买入下单接口
- [POST /robot/sellMarket](#post-robotsellmarket) — 市价卖出下单接口
- [GET /robot/trad/order](#get-robottradsorder) — 分页查询用户交易订单（历史）
- [GET /robot/trad/entrust](#get-robottradseentrust) — 分页查询用户委托记录

---

公共字段说明（常用参数）

- address (string) — 必填：用户地址（钱包地址 / 唯一标识）。用于用户识别与签名验证。
- parentAddress (string) — 必填/注册时：推荐填写上级/邀请人地址。
- signature (string) — 必填：用户签名或校验字段，服务端会用于验证请求合法性。
- maincoin (string) — 必填：主币，例如 `USDT`（或业务中定义的母币）。在某些接口中用于构造交易对。
- tradcoin (string) — 必填：交易币（子币），如 `ETH`，常与 maincoin 一起构成 `tradcoin/maincoin` 的交易对字符串。
- number (string|decimal) — 必填（下单/充值/提现等）：数量（字符串形式接收，后端会转为 BigDecimal）。
- price (string|decimal) — 必填（限价交易）：单价（字符串形式）。
- coin (string) — 必填（充值/提现）：币种符号，例如 `BTC`、`USDT`。
- mine (string) — K线/市场标识（service 层使用），例如 `1` 表示某一市场；具体含义见业务实现。
- count (int) — 请求返回条数或生成订单数，默认值见接口说明或实现。
- page (int) — 分页页码，默认 1。
- limit (int) — 分页每页大小，默认 10。
- memberNo / member（string） — 后端内部用户编号，通常通过地址和签名验证后获得，客户端无需直接传（部分后台接口可能需要）。

认证与权限

- 大部分接口需要鉴权，认证方式为检查请求头中的 `authorization` 字段。现在测试的值是 `202602041124`，请根据实际部署环境调整。
错误码（常见）

以下错误信息来自国际化资源（LanguagesUtil.get）：

- `parameter` — 参数丢失。
- `addressOrSignNotExists` — 地址或签名不正确。
- `error` — 通用失败。
- `noCoin` — 币种不存在或未开放交易。
- `noUser` — 用户不存在。

---

## GET /robot/symbols

- 描述：获取币种行情/交易对列表（包含 K 线类型过滤）。
- HTTP 方法：GET
- 路径：`/robot/symbols`
- 完整 URL：`https://m.ibmxi.com/api/robot/symbols`
- Auth：取决于后端实现（通常不强制用户鉴权）。

参数（Query）:

- klineType (string) 可选：K 线类型代码（例如 "1m"、"5m" 等或系统定义的枚举值）。

示例请求：

GET https://m.ibmxi.com/api/robot/symbols?klineType=1m

cURL 示例：

```bash
# 使用 Bearer token
curl -X GET "https://m.ibmxi.com/api/robot/symbols?klineType=1m" \
  -H "Authorization: <YOUR_TOKEN>" \
  -H "Accept: application/json"

# 或使用测试 header 值
curl -X GET "https://m.ibmxi.com/api/robot/symbols?klineType=1m" -H "Authorization: 202602041124"
```

示例成功响应（字段精确说明与示例）：

返回类型：R<List<CoinTypeVo>>

CoinTypeVo 常见字段（截取自实体/mapper 返回示例）：

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| autoid | Integer | 否 | 记录 ID |
| tradcoin | string | 是 | 交易币，例如 `BTC` |
| maincoin | string | 是 | 主币，例如 `USDT` |
| symbol | string | 否 | 交易对表示，如 `BTC_USDT` |
| price | string | 否 | 最新价（字符串形式以保留精度） |
| volume | string | 否 | 24 小时成交量 |
| priceAccuracy | int | 否 | 价格小数位数 |
| amountAccuracy | int | 否 | 数量小数位数 |
| minnumber | string | 否 | 最小成交数量 |
| maxnumber | string | 否 | 最大成交数量 |
| klineType | string | 否 | K 线类型编码（对应服务端枚举） |
| logo | string | 否 | 币种图标 URL |

示例响应：

{
  "code": 200,
  "data": [
    {
      "autoid": 101,
      "tradcoin": "BTC",
      "maincoin": "USDT",
      "symbol": "BTC_USDT",
      "price": "65234.56",
      "volume": "1234.56",
      "priceAccuracy": 2,
      "amountAccuracy": 3,
      "minnumber": "0.001",
      "maxnumber": "100.0",
      "klineType": "Crypto",
      "logo": "https://example.com/btc.png"
    }
  ],
  "msg": "成功"
}

---

## GET /robot/last

- 描述：获取某交易对的最新成交信息（单条）。
- HTTP 方法：GET
- 路径：`/robot/last`
- 完整 URL：`https://m.ibmxi.com/api/robot/last`
- Auth：无（接口实现中无需鉴权）。

参数（Query）：

- maincoin (string) 必填
- tradcoin (string) 必填

示例请求：

GET https://m.ibmxi.com/api/robot/last?maincoin=USDT&tradcoin=ETH

cURL 示例：

```bash
curl -X GET "https://m.ibmxi.com/api/robot/last?maincoin=USDT&tradcoin=ETH" \
  -H "Authorization: <YOUR_TOKEN>" \
  -H "Accept: application/json"
```

返回类型：R<CoinBo>

CoinBo 字段（来自 `com.sdt.bo.CoinBo`）重要字段：

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| autoid | Integer | 否 | 记录 ID |
| tradcoin | string | 是 | 交易币 |
| maincoin | string | 是 | 主币 |
| state | string | 否 | 状态（enable/disable 等） |
| height | string | 否 | 24 小时最高价 |
| low | string | 否 | 24 小时最低价 |
| price | string | 否 | 最新成交价 |
| priceAccuracy | int | 否 | 价格精度 |
| minnumber | string | 否 | 最小成交量 |
| maxnumber | string | 否 | 最大成交量 |
| amountAccuracy | int | 否 | 数量精度 |
| robot | string | 否 | 机器人开关状态 |
| robotMaxNumber | string | 否 | 每次机器人最大下单量 |
| robotMinNumber | string | 否 | 每次机器人最小下单量 |
| robotPriceGas | string | 否 | 机器人盘口档位价格 |
| lastSz | string | 否 | 最新成交量 |
| updateTime | string | 否 | 更新时间（ISO8601） |

示例成功响应：

{
  "code": 200,
  "data": {
    "autoid": 101,
    "tradcoin": "ETH",
    "maincoin": "USDT",
    "state": "enable",
    "height": "1820.50",
    "low": "1780.00",
    "price": "1800.00",
    "priceAccuracy": 2,
    "minnumber": "0.001",
    "maxnumber": "1000",
    "amountAccuracy": 3,
    "robot": "enable",
    "robotMaxNumber": "10.0",
    "robotMinNumber": "0.1",
    "robotPriceGas": "0.5",
    "lastSz": "12.5",
    "updateTime": "2026-02-12T10:12:00Z"
  },
  "msg": "成功"
}

---

## GET /robot/last_count

- 描述：获取指定交易对最近 N 条 K 线或成交数据。
- HTTP 方法：GET
- 路径：`/robot/last_count`
- 完整 URL：`https://m.ibmxi.com/api/robot/last_count`
- Auth：无

参数（Query）：

- maincoin (string) 必填
- tradcoin (string) 必填
- count (int) 可选，默认 10
- mine (string) 必填，用于指定市场标识

示例请求：

GET https://m.ibmxi.com/api/robot/last_count?maincoin=USDT&tradcoin=ETH&count=20&mine=1

cURL 示例：

```bash
curl -X GET "https://m.ibmxi.com/api/robot/last_count?maincoin=USDT&tradcoin=ETH&count=20&mine=1" \
  -H "Authorization: <YOUR_TOKEN>" \
  -H "Accept: application/json"
```

返回类型：R<List<KLineBo>>

KLineBo（`com.sdt.bo.KLineBo`）字段：

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| wdate | string | 是 | 时间，ISO 8601 或 `yyyy-MM-ddTHH:mm:ssZ` |
| opens | string | 是 | 开盘价 |
| height | string | 是 | 最高价 |
| low | string | 是 | 最低价 |
| closes | string | 是 | 收盘价 |
| number | string | 否 | 成交量或期号（实现中用途不一） |

示例成功响应：

{
  "code": 200,
  "data": [
    {
      "wdate": "2026-02-12T10:10:00Z",
      "opens": "1795.00",
      "height": "1805.00",
      "low": "1790.00",
      "closes": "1800.00",
      "number": "12.5"
    },
    {
      "wdate": "2026-02-12T10:11:00Z",
      "opens": "1800.00",
      "height": "1808.00",
      "low": "1798.00",
      "closes": "1802.00",
      "number": "8.3"
    }
  ],
  "msg": "成功"
}

---

## GET /robot/orders

- 描述：获取模拟/撮合的最近几条订单（深度或成交），由 `TradServices.top_order_date2` 生成。
- HTTP 方法：GET
- 路径：`/robot/orders`
- 完整 URL：`https://m.ibmxi.com/api/robot/orders`
- Auth：无（实现中不强制）

参数（Query）：

- maincoin (string) 必填
- tradcoin (string) 必填
- count (int) 可选，默认 10

示例请求：

GET https://m.ibmxi.com/api/robot/orders?maincoin=USDT&tradcoin=ETH&count=10

cURL 示例：

```bash
curl -X GET "https://m.ibmxi.com/api/robot/orders?maincoin=USDT&tradcoin=ETH&count=10" \
  -H "Authorization: <YOUR_TOKEN>" \
  -H "Accept: application/json"
```

返回类型：R<List<JSONObject>>（每个对象字段在服务实现里动态构造）

top_order_date2 常见字段（由实现设置）：

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| price | string | 是 | 成交/委托价格 |
| type | string | 是 | 方向：`buy` 或 `sell` |
| autoid | string | 是 | 生成的唯一 ID |
| number | string | 是 | 数量 |
| wdate | string | 是 | 格式化时间字符串（例如 `yyyy-MM-dd HH:mm:ss`） |
| time | string | 否 | 可选的时间对象（存在时） |

示例成功响应：

{
  "code": 200,
  "data": [
    { "price": "1800.00", "type": "buy", "autoid": "167617920001", "number": "1.5", "wdate": "2026-02-12 10:11:30" },
    { "price": "1800.00", "type": "sell", "autoid": "167617920002", "number": "0.8", "wdate": "2026-02-12 10:11:31" }
  ],
  "msg": "成功"
}

---

## POST /robot/user/reg

- 描述：用户注册。
- HTTP 方法：POST
- 路径：`/robot/user/reg`
- 完整 URL：`https://m.ibmxi.com/api/robot/user/reg`
- Auth：需要在请求头中包含认证信息（Authorization header），第三方对接只需在请求头里带上认证值；需要用户签名的接口同时要求在请求体或查询串携带 `signature` 参数用于验签

请求体（JSON）：
- address (string) 必填 — 用户地址
- parentAddress (string) 必填 — 邀请人或上级地址
- signature (string) 必填 — 签名/验签字段

示例请求：

POST https://m.ibmxi.com/api/robot/user/reg
Content-Type: application/json

{
  "address": "0xabc...",
  "parentAddress": "0xparent...",
  "signature": "base64-or-hex-signature"
}

cURL 示例：

```bash
curl -X POST "https://m.ibmxi.com/api/robot/user/reg" \
  -H "Authorization: <YOUR_TOKEN>" \
  -H "Content-Type: application/json" \
  -d '{"address":"0xabc...","parentAddress":"0xparent...","signature":"<SIG>"}'
```

成功响应：

{
  "code": 200,
  "data": "0xabc...",
  "msg": "成功"
}

常见错误：
- 参数缺失：返回 `parameter` 信息。
- 签名校验失败：返回 `addressOrSignNotExists`。

---

## GET /robot/user/info

- 描述：根据地址查询用户信息。
- HTTP 方法：GET
- 路径：`/robot/user/info`
- 完整 URL：`https://m.ibmxi.com/api/robot/user/info`
- Auth：需要在请求头中包含认证信息（Authorization header），第三方对接只需在请求头里带上认证值；需要用户签名的接口同时要求在请求体或查询串携带 `signature` 参数用于验签

参数（Query）：
- address (string) 必填

示例请求：

GET https://m.ibmxi.com/api/robot/user/info?address=0xabc...

cURL 示例：

```bash
curl -X GET "https://m.ibmxi.com/api/robot/user/info?address=0xabc..." \
  -H "Authorization: <YOUR_TOKEN>" \
  -H "Accept: application/json"
```

返回类型：R<UsUserPo>

UsUserPo 字段（截取主要常用字段，详见 `com.sdt.po.UsUserPo`）：

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| autoid | Integer | 否 | 自增主键 |
| tel | string | 否 | 手机号 |
| email | string | 否 | 邮箱 |
| username | string | 否 | 用户名 / 真实姓名 |
| idcard | string | 否 | 身份证号 |
| writedate | string | 否 | 注册/创建时间 |
| recomcode | string | 否 | 推荐码 |
| parent | string | 否 | 推荐人 / 上级 member |
| idcardstate | string | 否 | 实名认证状态（no/review/completed/reject） |
| minlevel | string | 否 | 会员等级 |
| member | string | 是 | 会员编号（memberNo） |
| state | string | 否 | 账户状态（enable/disable） |
| address | string | 否 | 区块链地址 |
| parentAddress | string | 否 | 上级地址 |
| signature | string | 否 | 存储的签名（用于验签） |

示例成功响应：

{
  "code": 200,
  "data": {
    "autoid": 1001,
    "tel": "13800000000",
    "email": "user@example.com",
    "username": "张三",
    "idcard": "370102199001010000",
    "writedate": "2025-12-01T08:00:00Z",
    "member": "M000123",
    "state": "enable",
    "address": "0xabc...",
    "parentAddress": "0xparent...",
    "signature": "abcdef123456"
  },
  "msg": "成功"
}

---

## POST /robot/user/recharge

- 描述：人工充值（后台或接口触发）。
- HTTP 方法：POST
- 路径：`/robot/user/recharge`
- 完整 URL：`https://m.ibmxi.com/api/robot/user/recharge`
- Auth：需要在请求头中包含认证信息（Authorization header），并在请求中携带 `signature` 参数用于用户签名校验（如接口说明所示）。

请求体（JSON）：
- address (string) 必填
- coin (string) 必填
- number (string/decimal) 必填
- signature (string) 必填（用于 getMemberNo 校验）

示例请求体：
{
  "address": "0xabc...",
  "coin": "USDT",
  "number": "100",
  "signature": "<SIG>"
}

cURL 示例：

```bash
curl -X POST "https://m.ibmxi.com/api/robot/user/recharge" \
  -H "Authorization: <YOUR_TOKEN>" \
  -H "Content-Type: application/json" \
  -d '{"address":"0xabc...","coin":"USDT","number":"100","signature":"<SIG>"}'
```

返回类型：R<UsAccountPo>

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| autoid | Integer | 是 | 账户记录自增 ID |
| coin | String | 是 | 币种名称，例如 `USDT` |
| usable | BigDecimal / String | 是 | 可用余额（字符串形式以保留精度） |
| disable | BigDecimal / String | 否 | 冻结余额 |
| other | BigDecimal / String | 否 | 其他余额（预留字段） |
| member | String | 是 | 会员编号（member） |

示例成功响应：

{
  "code": 200,
  "data": {
    "autoid": 501,
    "coin": "USDT",
    "usable": "1200.50",
    "disable": "0.00",
    "other": "0.00",
    "member": "M000123"
  },
  "msg": "成功"
}

---

## POST /robot/user/withdraw

- 描述：人工扣款 / 提现（后台/接口触发）。
- HTTP 方法：POST
- 路径：`/robot/user/withdraw`
- 完整 URL：`https://m.ibmxi.com/api/robot/user/withdraw`
- Auth：需要在请求头中包含认证信息（Authorization header），并在请求中携带 `signature` 参数用于签名校验

请求体（JSON）：
- address (string) 必填
- coin (string) 必填
- number (string/decimal) 必填
- signature (string) 必填

示例请求体：
{
  "address": "0xabc...",
  "coin": "USDT",
  "number": "10",
  "signature": "<SIG>"
}

cURL 示例：

```bash
curl -X POST "https://m.ibmxi.com/api/robot/user/withdraw" \
  -H "Authorization: <YOUR_TOKEN>" \
  -H "Content-Type: application/json" \
  -d '{"address":"0xabc...","coin":"USDT","number":"10","signature":"<SIG>"}'
```

返回：{"code":0,"data":"成功"}

---

## GET /robot/user/assets

- 描述：查询用户资产列表（可按 address 查询所有币种，或传 coin 仅查询指定币种）。
- HTTP 方法：GET
- 路径：`/robot/user/assets`
- 完整 URL：`https://m.ibmxi.com/api/robot/user/assets`
- Auth：需要在请求头中包含认证信息（Authorization header），第三方只需在请求头带上认证值。

参数（Query）:
- address (string) 必填
- coin (string) 可选

示例请求：

GET https://m.ibmxi.com/api/robot/user/assets?address=0xabc...&coin=USDT

cURL 示例：

```bash
curl -X GET "https://m.ibmxi.com/api/robot/user/assets?address=0xabc...&coin=USDT" \
  -H "Authorization: <YOUR_TOKEN>" \
  -H "Accept: application/json"
```

返回类型：R<List<UsAccountPo>>

示例成功响应：

{
  "code": 200,
  "data": [
    { "autoid": 501, "coin": "USDT", "usable": "1200.50", "disable": "0.00", "member": "M000123" },
    { "autoid": 502, "coin": "BTC", "usable": "0.050", "disable": "0.000", "member": "M000123" }
  ],
  "msg": "成功"
}

---

## POST /robot/buyLimit

- 描述：限价买入接口（需要下单权限）。
- HTTP 方法：POST
- 路径：`/robot/buyLimit`
- 完整 URL：`https://m.ibmxi.com/api/robot/buyLimit`
- Auth：需要在请求头中包含认证信息（Authorization header），并在请求中携带 `signature` 参数用于用户签名校验（如接口说明所示）。

请求体（JSON）：
- maincoin (string) 必填
- tradcoin (string) 必填
- number (string/decimal) 必填
- price (string/decimal) 必填
- allIn (string) 可选（是否市价全仓）

返回：{"code":0,"data":"成功"}

示例请求体：
{
  "maincoin": "USDT",
  "tradcoin": "ETH",
  "number": "1.0",
  "price": "1800.00"
}

cURL 示例（限价买入）：

```bash
curl -X POST "https://m.ibmxi.com/api/robot/buyLimit" \
  -H "Authorization: <YOUR_TOKEN>" \
  -H "Content-Type: application/json" \
  -d '{"maincoin":"USDT","tradcoin":"ETH","number":"1.0","price":"1800.00"}'
```

---

## POST /robot/sellLimit

- 描述：限价卖出。
- HTTP 方法：POST
- 路径：`/robot/sellLimit`
- Auth：需要鉴权和签名

参数与 buyLimit 类似（price 必填）。

示例请求体（限价卖出，市价买/卖也类似）：
{
  "maincoin": "USDT",
  "tradcoin": "ETH",
  "number": "1.0",
  "price": "1800.00"
}

cURL 示例（限价卖出）：

```bash
curl -X POST "https://m.ibmxi.com/api/robot/sellLimit" \
  -H "Authorization: <YOUR_TOKEN>" \
  -H "Content-Type: application/json" \
  -d '{"maincoin":"USDT","tradcoin":"ETH","number":"1.0","price":"1800.00"}'
```

---

## POST /robot/buyMarket

- 描述：市价买入。
- HTTP 方法：POST
- 路径：`/robot/buyMarket`
- Auth：需要鉴权和签名

必填：maincoin, tradcoin, number。price 可选（执行时使用跌破/涨幅策略）。

示例请求体（市价买入）：
{
  "maincoin": "USDT",
  "tradcoin": "ETH",
  "number": "1.0"
}

cURL 示例（市价买入）：

```bash
curl -X POST "https://m.ibmxi.com/api/robot/buyMarket" \
  -H "Authorization: <YOUR_TOKEN>" \
  -H "Content-Type: application/json" \
  -d '{"maincoin":"USDT","tradcoin":"ETH","number":"1.0"}'
```

---

## POST /robot/sellMarket

- 描述：市价卖出。
- HTTP 方法：POST
- 路径：`/robot/sellMarket`
- Auth：需要鉴权和签名

必填：maincoin, tradcoin, number。price 可选（通常不传）。

示例请求体（市价卖出）：
{
  "maincoin": "USDT",
  "tradcoin": "ETH",
  "number": "0.5"
}

cURL 示例（市价卖出）：

```bash
curl -X POST "https://m.ibmxi.com/api/robot/sellMarket" \
  -H "Authorization: <YOUR_TOKEN>" \
  -H "Content-Type: application/json" \
  -d '{"maincoin":"USDT","tradcoin":"ETH","number":"0.5"}'
```

---

## GET /robot/trad/order

- 描述：分页查询用户的交易订单（内部 member 需要通过 getMemberNo 或 HelpOpenUser 获取）。
- HTTP 方法：GET
- 路径：`/robot/trad/order`
- 参数：page, limit（可选）
- Auth：需要鉴权（getMemberNo）

返回类型：R<PageDTO<TrOrderPo>>

TrOrderPo 常用字段（见 `com.sdt.po.TrOrderPo`）：

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| autoid | Long | 是 | 订单主键 |
| tradcoin | string | 是 | 交易币 |
| maincoin | string | 是 | 主币 |
| number | string | 是 | 数量 |
| price | string | 是 | 单价 |
| entrustid | Integer | 否 | 委托编号 |
| sellgas | string | 否 | 卖方手续费 |
| buygas | string | 否 | 买方手续费 |
| type | string | 是 | buy / sell |
| writedate | string | 否 | 创建时间 |
| buymember | string | 否 | 买方 member |
| sellmember | string | 否 | 卖方 member |
| style | string | 否 | limit / market |

示例成功响应：

{
  "code": 200,
  "data": {
    "total": 123,
    "current": 1,
    "size": 10,
    "records": [
      {
        "autoid": 20001,
        "tradcoin": "ETH",
        "maincoin": "USDT",
        "number": "0.50",
        "price": "1800.00",
        "entrustid": 3001,
        "type": "buy",
        "writedate": "2026-02-12T10:00:00Z",
        "buymember": "M000123",
        "sellmember": "M000456",
        "style": "limit"
      }
    ]
  },
  "msg": "成功"
}

cURL 示例：

```bash
curl -X GET "https://m.ibmxi.com/api/robot/trad/order?page=1&limit=10" \
  -H "Authorization: <YOUR_TOKEN>" \
  -H "Accept: application/json"
```

---

## GET /robot/trad/entrust

- 描述：分页查询用户的委托记录（entrust）。
- HTTP 方法：GET
- 路径：`/robot/trad/entrust`
- 参数：page, limit（可选）
- Auth：需要鉴权（getMemberNo）

返回类型：R<PageDTO<TrEntrustPo>>

TrEntrustPo 常用字段（见 `com.sdt.po.TrEntrustPo`）：

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| autoid | Integer | 是 | 委托主键 |
| tradcoin | string | 是 | 交易币 |
| maincoin | string | 是 | 主币 |
| number | string | 是 | 委托数量 |
| price | string | 是 | 委托单价 |
| tradeAmount | string | 否 | 交易金额 |
| unfilled | string | 否 | 未成交数量 |
| completed | string | 否 | 已成交数量 |
| gas | string | 否 | 手续费 |
| unfilledgas | string | 否 | 未成交手续费 |
| completedgas | string | 否 | 已成交手续费 |
| writedate | string | 否 | 创建时间 |
| state | string | 否 | 状态（unfilled/section/all/cancel） |
| type | string | 是 | buy / sell |
| member | string | 是 | 会员编号 |
| style | string | 否 | limit / market |

示例成功响应：

{
  "code": 200,
  "data": {
    "total": 45,
    "current": 1,
    "size": 10,
    "records": [
      {
        "autoid": 4001,
        "tradcoin": "ETH",
        "maincoin": "USDT",
        "number": "1.0",
        "price": "1800.00",
        "tradeAmount": "1800.00",
        "unfilled": "0.5",
        "completed": "0.5",
        "gas": "0.002",
        "unfilledgas": "0.001",
        "completedgas": "0.001",
        "writedate": "2026-02-12T09:55:00Z",
        "state": "section",
        "type": "buy",
        "member": "M000123",
        "style": "limit"
      }
    ]
  },
  "msg": "成功"
}

cURL 示例：

```bash
curl -X GET "https://m.ibmxi.com/api/robot/trad/entrust?page=1&limit=10" \
  -H "Authorization: <YOUR_TOKEN>" \
  -H "Accept: application/json"
```

---

统一响应结构与返回字段说明

所有接口采用统一响应包装 `R<T>`：

| 字段 | 类型 | 说明 |
|------|------|------|
| code | int | 状态码，200 表示成功，非 0 表示失败 |
| msg | string | 国际化提示信息（LanguagesUtil） |
| data | T | 实际返回的负载（对象/数组/字符串等） |

说明：本文档中每个接口已在各自段落中按表格形式定义了具体返回对象（例如 `CoinTypeVo`, `CoinBo`, `KLineBo`, `UsUserPo`, `UsAccountPo`, `TrOrderPo`, `TrEntrustPo`, 以及 `top_order_date2` 返回的 JSONObject 等）。请以对应接口段落中的“参数表格”作为字段定义的权威来源，避免跨处重复维护。

示例（统一成功返回）：

```
{
  "code": 200,
  "data": <payload>,
  "msg": "成功"
}
```

示例（统一失败返回）：

```
{
  "code": 1,
  "data": null,
  "msg": "参数丢失"
}
```

---

版本与备注

- 文档基于 `com.sdt.web.controller.RobotController` 当前源码生成（见 `coincasso-web/src/main/java/com/sdt/web/controller/RobotController.java`）。
- 若你决定修改 `user/reg` 的鉴权策略或 `orders` 返回类型（JSON vs Map），请告知，我可以同步更新文档并把返回类型标准化为 DTO 或 Map。
