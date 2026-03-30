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
- [GET /robot/user/assets_detail](#get-robotuserassets_detail) — 查询用户资产明细（按 address + coin 分页）
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
  -H "Authorization: 202602041124" \
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
  -H "Authorization: 202602041124" \
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
  -H "Authorization: 202602041124" \
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
  -H "Authorization: 202602041124" \
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
curl --location 'https://m.ibmxi.com/api/robot/user/reg' \
--header 'Authorization: 202602041124' \
--header 'Content-Type: application/json' \
--data '{"address":"0xabc","parentAddress":"0x001...","signature":"<SIG>"}'
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
curl --location 'https://m.ibmxi.com/api/robot/user/info?address=0xabc' \
--header 'Authorization: 202602041124' \
--header 'Accept: application/json'
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
curl --location 'https://m.ibmxi.com/api/robot/user/recharge' \
--header 'Authorization: 202602041124' \
--header 'Content-Type: application/json' \
--data '{"address":"0xabc","coin":"USDT","number":"100","signature":"<SIG>"}'
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
curl --location 'https://m.ibmxi.com/api/robot/user/withdraw' \
--header 'Authorization: 202602041124' \
--header 'Content-Type: application/json' \
--data '{"address":"0xabc","coin":"USDT","number":"10","signature":"<SIG>"}'
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
curl --location 'https://m.ibmxi.com/api/robot/user/assets?address=0xabc&signature=%3CSIG%3E' \
--header 'Authorization: 202602041124' \
--header 'Accept: application/json'
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

## GET /robot/user/assets_detail

- 描述：查询指定地址在某一币种的账户变动明细（分页）。
- HTTP 方法：GET
- 路径：`/robot/user/assets_detail`
- 完整 URL：`https://m.ibmxi.com/api/robot/user/assets_detail`
- 鉴权：请求必须包含认证头 `token: <your_token>`（项目约定的 header，不是 Bearer）。
- 说明：后台实现会先根据 `address` 查到对应的 `member`，再按 `coin` 和 `member` 查询账户日志（UsAccountLogPo）。

请求参数（Query）

| 参数 | 类型 | 必填 | 说明 |
|------|------:|:----:|------|
| address | string | 是 | 用户地址（用于查找 member），示例: `0xabc...` |
| coin | string | 是 | 币种代码，例如 `USDT` |
| page | integer | 否 | 页码，默认 1 |
| limit | integer | 否 | 每页条数，默认 10 |

请求头

| Header | 示例 | 说明 |
|--------|------|------|
| token | token: UAT20283084048911319041772421382530 | 必需，项目约定的认证字段 |
| Accept | application/json | 推荐 |

请求示例（curl，方便导入 Postman）

Windows PowerShell / cmd：

```bash
curl --location "https://m.ibmxi.com/api/robot/user/assets_detail?address=0x123456&coin=USDT&page=1&limit=10" \
  --header "token: UAT20283084048911319041772421382530" \
  --header "Accept: application/json"
```

返回说明

统一响应包装（R）：

| 字段 | 类型 | 说明 |
|------|------|------|
| code | integer | 状态码；200 表示成功 |
| msg  | string  | 提示消息 |
| data | object  | 分页对象 PageDTO<UsAccountLogPo> |

PageDTO（data）常见字段：

| 字段 | 类型 | 说明 |
|------|------|------|
| total | long | 总记录数 |
| size  | int  | 每页大小 |
| current | int | 当前页码 |
| records | array | 记录数组（UsAccountLogPo 列表） |

UsAccountLogPo 字段说明（records 每项）

| 字段 | 类型 | 示例 | 说明 |
|------|------|------|------|
| id | integer | 321 | 自增记录 ID |
| usableBefore | decimal | 100.00000000 | 变动前可用余额 |
| usableCurr | decimal | 150.00000000 | 变动后可用余额 |
| usdisable | decimal | 0.00000000 | 冻结金额 |
| remark | string | "系统充值" | 备注 |
| detail | string(JSON) | "{\"orderNo\":\"...\"}" | 变动详情（JSON 字符串） |
| writedate | string (datetime) | "2026-03-27 10:10:10" | 记录时间 |
| coin | string | "USDT" | 币种 |
| member | string | "M10001" | 会员号（内部） |
| type | string | "IN" | 变动类型（IN/OUT/NO 等） |
| sourceType | string | "RECHARGE" | 来源类型编码 |
| flowNo | string | "FLOW202603271001" | 流水号 |
| outFlowNo | string | null | 外部流水号（如有） |

示例响应（成功）

{
  "code": 200,
  "msg": "成功",
  "data": {
    "total": 2,
    "size": 10,
    "current": 1,
    "records": [
      {
        "id": 321,
        "usableBefore": 100.00000000,
        "usableCurr": 150.00000000,
        "usdisable": 0.00000000,
        "remark": "手工充值",
        "detail": "{\"txId\":\"abc...\",\"note\":\"管理员充值\"}",
        "writedate": "2026-03-27 10:10:10",
        "coin": "USDT",
        "member": "M10001",
        "type": "IN",
        "sourceType": "RECHARGE",
        "flowNo": "FLOW202603271001",
        "outFlowNo": null
      }
    ]
  }
}

错误示例（参数缺失）：

{
  "code": 1,
  "msg": "parameter",
  "data": null
}

注意事项

- 请使用项目约定的 header `token: <your_token>` 进行鉴权（而不是 Bearer）。
- detail 字段为字符串化的 JSON，前端解析展示时需先尝试 JSON.parse。
- writedate 的时区与后端服务器设置有关，如遇时差问题请与后端确认时区或统一使用 UTC。

---

## POST /robot/buyLimit

- 描述：限价买入下单接口。用户以指定的价格买入指定数量的交易币。
- HTTP 方法：POST
- 路径：`/robot/buyLimit`
- 完整 URL：`https://m.ibmxi.com/api/robot/buyLimit`
- Auth：需要在请求头中包含认证信息（Authorization header）

请求体（JSON）：
- address (string) 必填 — 用户地址
- maincoin (string) 必填 — 主币（支付币种），例如 `USDT`
- tradcoin (string) 必填 — 交易币（购买币种），例如 `BTC`
- number (string/decimal) 必填 — 购买数量
- price (string/decimal) 必填 — 购买单价
- signature (string) 必填 — 签名校验字段

示例请求体：
```json
{
  "address": "0xabc...",
  "maincoin": "USDT",
  "tradcoin": "BTC",
  "number": "0.5",
  "price": "45000.00",
  "signature": "<SIG>"
}
```

cURL 示例：

```bash
curl --location 'https://m.ibmxi.com/api/robot/buyLimit' \
--header 'Authorization: 202602041124' \
--header 'Content-Type: application/json' \
--data '{"address":"0xabc","maincoin":"USDT","tradcoin":"BTC","number":"0.5","price":"45000.00","signature":"<SIG>"}'
```

返回类型：R<Object>

示例成功响应：

```json
{
  "code": 200,
  "data": {
    "orderId": "12345678",
    "tradcoin": "BTC",
    "maincoin": "USDT",
    "number": "0.5",
    "price": "45000.00",
    "totalAmount": "22500.00",
    "status": "PENDING",
    "createTime": "2026-03-30T10:30:00Z"
  },
  "msg": "成功"
}
```

常见错误：
- 参数缺失：返回 `parameter`
- 余额不足：返回 `insufficientBalance`
- 币种不存在：返回 `noCoin`

---

## POST /robot/sellLimit

- 描述：限价卖出下单接口。用户以指定的价格卖出指定数量的交易币。
- HTTP 方法：POST
- 路径：`/robot/sellLimit`
- 完整 URL：`https://m.ibmxi.com/api/robot/sellLimit`
- Auth：需要在请求头中包含认证信息（Authorization header）

请求体（JSON）：
- address (string) 必填 — 用户地址
- maincoin (string) 必填 — 主币（结算币种），例如 `USDT`
- tradcoin (string) 必填 — 交易币（卖出币种），例如 `BTC`
- number (string/decimal) 必填 — 卖出数量
- price (string/decimal) 必填 — 卖出单价
- signature (string) 必填 — 签名校验字段

示例请求体：
```json
{
  "address": "0xabc...",
  "maincoin": "USDT",
  "tradcoin": "BTC",
  "number": "0.5",
  "price": "44800.00",
  "signature": "<SIG>"
}
```

cURL 示例：

```bash
curl --location 'https://m.ibmxi.com/api/robot/sellLimit' \
--header 'Authorization: 202602041124' \
--header 'Content-Type: application/json' \
--data '{"address":"0xabc","maincoin":"USDT","tradcoin":"BTC","number":"0.5","price":"44800.00","signature":"<SIG>"}'
```

返回类型：R<Object>

示例成功响应：

```json
{
  "code": 200,
  "data": {
    "orderId": "12345679",
    "tradcoin": "BTC",
    "maincoin": "USDT",
    "number": "0.5",
    "price": "44800.00",
    "totalAmount": "22400.00",
    "status": "PENDING",
    "createTime": "2026-03-30T10:31:00Z"
  },
  "msg": "成功"
}
```

常见错误：
- 参数缺失：返回 `parameter`
- 余额不足：返回 `insufficientBalance`
- 币种不存在：返回 `noCoin`

---

## POST /robot/buyMarket

- 描述：市价买入下单接口。用户以当前市价购买指定金额或数量的交易币。
- HTTP 方法：POST
- 路径：`/robot/buyMarket`
- 完整 URL：`https://m.ibmxi.com/api/robot/buyMarket`
- Auth：需要在请求头中包含认证信息（Authorization header）

请求体（JSON）：
- address (string) 必填 — 用户地址
- maincoin (string) 必填 — 主币（支付币种），例如 `USDT`
- tradcoin (string) 必填 — 交易币（购买币种），例如 `BTC`
- number (string/decimal) 必填 — 购买数量
- signature (string) 必填 — 签名校验字段

示例请求体：
```json
{
  "address": "0xabc...",
  "maincoin": "USDT",
  "tradcoin": "BTC",
  "number": "0.5",
  "signature": "<SIG>"
}
```

cURL 示例：

```bash
curl --location 'https://m.ibmxi.com/api/robot/buyMarket' \
--header 'Authorization: 202602041124' \
--header 'Content-Type: application/json' \
--data '{"address":"0xabc","maincoin":"USDT","tradcoin":"BTC","number":"0.5","signature":"<SIG>"}'
```

返回类型：R<Object>

示例成功响应：

```json
{
  "code": 200,
  "data": {
    "orderId": "12345680",
    "tradcoin": "BTC",
    "maincoin": "USDT",
    "number": "0.5",
    "avgPrice": "45050.00",
    "totalAmount": "22525.00",
    "status": "FILLED",
    "createTime": "2026-03-30T10:32:00Z"
  },
  "msg": "成功"
}
```

常见错误：
- 参数缺失：返回 `parameter`
- 余额不足：返回 `insufficientBalance`
- 币种不存在：返回 `noCoin`

---

## POST /robot/sellMarket

- 描述：市价卖出下单接口。用户以当前市价卖出指定数量的交易币。
- HTTP 方法：POST
- 路径：`/robot/sellMarket`
- 完整 URL：`https://m.ibmxi.com/api/robot/sellMarket`
- Auth：需要在请求头中包含认证信息（Authorization header）

请求体（JSON）：
- address (string) 必填 — 用户地址
- maincoin (string) 必填 — 主币（结算币种），例如 `USDT`
- tradcoin (string) 必填 — 交易币（卖出币种），例如 `BTC`
- number (string/decimal) 必填 — 卖出数量
- signature (string) 必填 — 签名校验字段

示例请求体：
```json
{
  "address": "0xabc...",
  "maincoin": "USDT",
  "tradcoin": "BTC",
  "number": "0.5",
  "signature": "<SIG>"
}
```

cURL 示例：

```bash
curl --location 'https://m.ibmxi.com/api/robot/sellMarket' \
--header 'Authorization: 202602041124' \
--header 'Content-Type: application/json' \
--data '{"address":"0xabc","maincoin":"USDT","tradcoin":"BTC","number":"0.5","signature":"<SIG>"}'
```

返回类型：R<Object>

示例成功响应：

```json
{
  "code": 200,
  "data": {
    "orderId": "12345681",
    "tradcoin": "BTC",
    "maincoin": "USDT",
    "number": "0.5",
    "avgPrice": "44950.00",
    "totalAmount": "22475.00",
    "status": "FILLED",
    "createTime": "2026-03-30T10:33:00Z"
  },
  "msg": "成功"
}
```

常见错误：
- 参数缺失：返回 `parameter`
- 余额不足：返回 `insufficientBalance`
- 币种不存在：返回 `noCoin`

---

## GET /robot/trad/order

- 描述：分页查询用户的历史交易订单（已成交）。
- HTTP 方法：GET
- 路径：`/robot/trad/order`
- 完整 URL：`https://m.ibmxi.com/api/robot/trad/order`
- Auth：需要在请求头中包含认证信息（Authorization header）

请求参数（Query）

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| page | integer | 否 | 页码，默认 1 |
| limit | integer | 否 | 每页条数，默认 10 |

示例请求：

GET https://m.ibmxi.com/api/robot/trad/order?address=0xabc&maincoin=USDT&tradcoin=BTC&page=1&limit=10

cURL 示例：

```bash
curl --location 'https://m.ibmxi.com/api/robot/trad/order?address=0xabc&maincoin=USDT&tradcoin=BTC&page=1&limit=10' \
--header 'Authorization: 202602041124' \
--header 'Accept: application/json'
```

返回类型：R<PageDTO<TradOrderPo>>

返回字段说明

分页对象（PageDTO）包含以下字段：

| 字段 | 类型 | 说明 |
|------|------|------|
| total | long | 总记录数 |
| size  | int  | 每页大小 |
| current | int | 当前页码 |
| records | array | 订单记录数组 |

订单记录（TradOrderPo）字段说明：

| 字段 | 类型 | 说明 |
|------|------|------|
| autoid | long | 主键ID（自增） |
| tradcoin | string | 交易币种 |
| maincoin | string | 主币种 |
| number | decimal | 成交数量 |
| price | decimal | 成交单价 |
| entrustid | integer | 关联委托单ID/订单编号 |
| sellgas | decimal | 卖方交易手续费 |
| type | string | 成交类型：`buy`（买）/`sell`（卖） |
| writedate | datetime | 创建时间 |
| buymember | string | 买方会员编号 |
| sellmember | string | 卖方会员编号 |
| style | string | 下单方式：`limit`（限价）/`market`（市价） |
| buygas | decimal | 买方交易手续费 |

示例成功响应：

```json
{
  "code": 200,
  "msg": "成功",
  "data": {
    "total": 5,
    "size": 10,
    "current": 1,
    "records": [
      {
        "id": 101,
        "address": "0xabc...",
        "tradcoin": "BTC",
        "maincoin": "USDT",
        "direction": "buy",
        "number": "0.5",
        "price": "45000.00",
        "totalAmount": "22500.00",
        "status": "FILLED",
        "createTime": "2026-03-30 10:30:00",
        "finishTime": "2026-03-30 10:35:00"
      },
      {
        "id": 102,
        "address": "0xabc...",
        "tradcoin": "ETH",
        "maincoin": "USDT",
        "direction": "sell",
        "number": "1.0",
        "price": "1800.00",
        "totalAmount": "1800.00",
        "status": "FILLED",
        "createTime": "2026-03-30 11:00:00",
        "finishTime": "2026-03-30 11:05:00"
      }
    ]
  }
}
```

错误示例：

```json
{
  "code": 1,
  "msg": "parameter",
  "data": null
}
```

---

## GET /robot/trad/entrust

- 描述：分页查询用户的委托记录（包含待成交、部分成交、已取消等状态）。
- HTTP 方法：GET
- 路径：`/robot/trad/entrust`
- 完整 URL：`https://m.ibmxi.com/api/robot/trad/entrust`
- Auth：需要在请求头中包含认证信息（Authorization header）

请求参数（Query）

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| address | string | 是 | 用户地址 |
| maincoin | string | 否 | 主币过滤，例如 `USDT` |
| tradcoin | string | 否 | 交易币过滤，例如 `BTC` |
| direction | string | 否 | 委托方向：`buy`（买）或 `sell`（卖） |
| status | string | 否 | 委托状态：`PENDING`、`PARTIAL`、`FILLED`、`CANCELLED`、`EXPIRED` |
| page | integer | 否 | 页码，默认 1 |
| limit | integer | 否 | 每页条数，默认 10 |

示例请求：

GET https://m.ibmxi.com/api/robot/trad/entrust?address=0xabc&status=PENDING&page=1&limit=10

cURL 示例：

```bash
curl --location 'https://m.ibmxi.com/api/robot/trad/entrust?address=0xabc&status=PENDING&page=1&limit=10' \
--header 'Authorization: 202602041124' \
--header 'Accept: application/json'
```

返回类型：R<PageDTO<EntrustPo>>

返回字段说明

分页对象（PageDTO）包含以下字段：

| 字段 | 类型 | 说明 |
|------|------|------|
| total | long | 总记录数 |
| size  | int  | 每页大小 |
| current | int | 当前页码 |
| records | array | 委托记录数组 |

委托记录（EntrustPo）字段说明：

| 字段 | 类型 | 示例 | 说明 |
|------|------|------|------|
| id | integer | 201 | 委托记录 ID |
| entrustNo | string | ENT20260330001 | 委托单号 |
| address | string | 0xabc... | 委托用户地址 |
| tradcoin | string | BTC | 交易币 |
| maincoin | string | USDT | 主币 |
| direction | string | buy | 委托方向：buy（买）、sell（卖） |
| number | decimal | 1.0 | 委托数量 |
| price | decimal | 44800.00 | 委托单价 |
| totalAmount | decimal | 44800.00 | 委托总金额 |
| dealNumber | decimal | 0.5 | 已成交数量 |
| status | string | PENDING | 委托状态 |
| createTime | datetime | 2026-03-30 10:45:00 | 委托创建时间 |
| updateTime | datetime | 2026-03-30 10:50:00 | 委托更新时间 |

示例成功响应：

```json
{
  "code": 200,
  "msg": "成功",
  "data": {
    "total": 3,
    "size": 10,
    "current": 1,
    "records": [
      {
        "id": 201,
        "entrustNo": "ENT20260330001",
        "address": "0xabc...",
        "tradcoin": "BTC",
        "maincoin": "USDT",
        "direction": "buy",
        "number": "1.0",
        "price": "44800.00",
        "totalAmount": "44800.00",
        "dealNumber": "0.5",
        "status": "PENDING",
        "createTime": "2026-03-30 10:45:00",
        "updateTime": "2026-03-30 10:50:00"
      },
      {
        "id": 202,
        "entrustNo": "ENT20260330002",
        "address": "0xabc...",
        "tradcoin": "ETH",
        "maincoin": "USDT",
        "direction": "sell",
        "number": "2.0",
        "price": "1805.00",
        "totalAmount": "3610.00",
        "dealNumber": "1.0",
        "status": "PARTIAL",
        "createTime": "2026-03-30 11:10:00",
        "updateTime": "2026-03-30 11:20:00"
      }
    ]
  }
}
```

错误示例：

```json
{
  "code": 1,
  "msg": "parameter",
  "data": null
}
```

注意事项

- 委托状态说明：
  - `PENDING`：待成交，未有成交量
  - `PARTIAL`：部分成交，已成交部分数量
  - `FILLED`：已成交，全部成交
  - `CANCELLED`：已取消，用户手动取消
  - `EXPIRED`：已过期，委托超时自动取消
- 查询结果按创建时间倒序排列
- 时间字段格式为 `yyyy-MM-dd HH:mm:ss`
