# Coincasso REST API 文档（机器人接口）

本文档说明 RobotController 提供的 REST API 接口，用于获取交易对的实时价格、K线数据、成交订单以及用户管理。

---

## 基础信息

- **Base URL**: `https://m.ibmxi.com/api`
- **请求方式**: GET / POST
- **返回格式**: JSON
- **字符编码**: UTF-8
- **授权方式**: Header 中包含 `authorization` 字段（用于用户相关接口） 测试密钥为 `202602041124` 上线后请单独询问

---

## 1. 获取最新价格 `/robot/last`

获取指定交易对的最新价格及相关信息。

### 1.1 请求信息

**接口地址**
```
GET /robot/last
```

**请求参数**

| 参数名 | 类型 | 必填 | 说明 | 示例 |
|--------|------|------|------|------|
| maincoin | String | 是 | 主币（如 USDT） | USDT |
| tradcoin | String | 是 | 交易币（如 BTC） | BTC |

**请求示例**
```
GET /robot/last?maincoin=USDT&tradcoin=BTC
```

### 1.2 返回数据

**成功响应示例**
```json
{
  "code": 200,
  "msg": "操作成功",
  "data": {
    "autoid": 1,
    "tradcoin": "BTC",
    "maincoin": "USDT",
    "state": "enable",
    "height": "67000.00",
    "low": "64500.00",
    "price": "65234.56",
    "priceAccuracy": 2,
    "minnumber": "0.001",
    "maxnumber": "100.0",
    "amountAccuracy": 3,
    "robot": "enable",
    "robotMaxNumber": "1.5",
    "robotMinNumber": "0.5",
    "mainMarket": "enable",
    "robotPriceGas": "10.0",
    "robotPriceHigh": "0.02",
    "robotPriceLow": "0.02",
    "type": "trad",
    "updateTime": "2026-01-31T12:00:00",
    "lastSz": "150.5",
    "open": "65000.00",
    "klineType": "Crypto"
  }
}
```

**返回字段说明（data 对象）**

| 字段 | 类型 | 说明 |
|------|------|------|
| autoid | Integer | 自增ID |
| tradcoin | String | 交易币种 |
| maincoin | String | 主币种 |
| state | String | 状态（enable：正在交易；disable：已下架；ready：即将交易） |
| height | BigDecimal | 24H最高价 |
| low | BigDecimal | 24H最低价 |
| price | BigDecimal | 最新成交价 ⭐ |
| priceAccuracy | Integer | 价格精度（小数位数）⭐ |
| minnumber | BigDecimal | 最小成交量 |
| maxnumber | BigDecimal | 最大成交量 |
| amountAccuracy | Integer | 数量精度（小数位数）⭐ |
| robot | String | 机器人状态（enable：启用；disable：停用） |
| robotMaxNumber | BigDecimal | 每次刷单数量上限 |
| robotMinNumber | BigDecimal | 每次刷单数量下限 |
| mainMarket | String | 是否是主流币种 |
| robotPriceGas | BigDecimal | 机器人盘口档位 |
| robotPriceHigh | BigDecimal | 上涨幅度 |
| robotPriceLow | BigDecimal | 下跌幅度 |
| type | String | 类型（trad：币币） |
| updateTime | Date | 更新时间 |
| lastSz | BigDecimal | 最新的成交量 |
| open | BigDecimal | 24小时开盘价或者分钟开盘价 |
| klineType | String | K线类型（Crypto：数字货币） |

**错误响应示例**
```json
{
  "code": 500,
  "msg": "参数错误",
  "data": null
}
```

---

## 2. 获取K线数据 `/robot/last_count`

获取指定交易对的历史K线数据（最近N条）。

### 2.1 请求信息

**接口地址**
```
GET /robot/last_count
```

**请求参数**

| 参数名 | 类型 | 必填 | 默认值 | 说明 | 示例 |
|--------|------|------|--------|------|------|
| maincoin | String | 是 | - | 主币（如 USDT） | USDT |
| tradcoin | String | 是 | - | 交易币（如 BTC） | BTC |
| count | Integer | 否 | 10 | 返回K线数量 | 20 |
| mine | String | 是 | - | 时间周期（1m, 5m, 15m, 30m, 60m, 240m, 1D） | 1m |

**时间周期说明**
- `1` - 1分钟
- `5` - 5分钟
- `15` - 15分钟
- `30` - 30分钟
- `60` - 1小时
- `240` - 4小时
- `1440` - 1天

**请求示例**
```
GET /robot/last_count?maincoin=USDT&tradcoin=BTC&count=20&mine=1m
```

### 2.2 返回数据

**成功响应示例**
```json
{
  "code": 200,
  "msg": "操作成功",
  "data": [
    {
      "wdate": "2026-01-31T12:00:00",
      "number": "150.5",
      "height": "65300.00",
      "low": "65100.00",
      "closes": "65234.56",
      "opens": "65200.00"
    },
    {
      "wdate": "2026-01-31T12:01:00",
      "number": "120.3",
      "height": "65400.00",
      "low": "65200.00",
      "closes": "65350.00",
      "opens": "65234.56"
    }
  ]
}
```

**返回字段说明（data 数组中的每个对象）**

| 字段 | 类型 | 说明 |
|------|------|------|
| wdate | Date | K线时间（该周期的开始时间） |
| number | BigDecimal | 成交量 |
| height | BigDecimal | 最高价 |
| low | BigDecimal | 最低价 |
| closes | BigDecimal | 收盘价 |
| opens | BigDecimal | 开盘价 |

**注意事项**
- 数据按时间倒序排列（最新的在前）
- `count` 参数最大建议不超过 100，避免数据量过大
- K线数据来自实时行情源，与 WebSocket 推送的数据一致

---

## 3. 获取成交订单 `/robot/orders`

获取指定交易对的模拟机器人成交订单列表（最近N条）。

### 3.1 请求信息

**接口地址**
```
GET /robot/orders
```

**请求参数**

| 参数名 | 类型 | 必填 | 默认值 | 说明 | 示例 |
|--------|------|------|--------|------|------|
| maincoin | String | 是 | - | 主币（如 USDT） | USDT |
| tradcoin | String | 是 | - | 交易币（如 BTC） | BTC |
| count | Integer | 否 | 10 | 返回订单数量 | 20 |

**请求示例**
```
GET /robot/orders?maincoin=USDT&tradcoin=BTC&count=20
```

### 3.2 返回数据

**成功响应示例**
```json
{
  "code": 200,
  "msg": "操作成功",
  "data": [
    {
      "price": "65234.56",
      "number": "0.523",
      "type": "buy",
      "wdate": "2026-01-31 12:00:30"
    },
    {
      "price": "65240.12",
      "number": "0.835",
      "type": "sell",
      "wdate": "2026-01-31 12:00:25"
    }
  ]
}
```

**返回字段说明（data 数组中的每个对象）**

| 字段 | 类型 | 说明 |
|------|------|------|
| price | String | 成交价格（格式化为字符串，保留指定精度） |
| number | String | 成交数量（格式化为字符串，保留指定精度） |
| type | String | 订单类型（buy：买入；sell：卖出） |
| wdate | String | 成交时间（格式：yyyy-MM-dd HH:mm:ss） |

**注意事项**
- 这些订单是模拟机器人生成的成交数据，用于展示市场活跃度
- 订单按时间倒序排列（最新的在前）
- 订单价格基于实时行情上下浮动生成
- `count` 参数建议不超过 50

---

## 4. 用户注册 `/robot/user/reg`

注册新用户（机器人用户），需要提供钱包地址、上级推荐人地址和签名。

### 4.1 请求信息

**接口地址**
```
POST /robot/user/reg
```

**授权要求**
- 必须在请求 Header 中包含 `authorization` 字段
- `authorization` 值应与服务器配置的密钥相匹配

**请求参数**

| 参数名 | 类型 | 必填 | 说明 | 示例 |
|--------|------|------|------|------|
| address | String | 是 | 用户钱包地址（新用户唯一标识） | 0x1234567890abcdef... |
| parentAddress | String | 是 | 上级推荐人的钱包地址 | 0xabcdef1234567890... |
| signature | String | 是 | 地址签名（用于验证地址所有权） | 0x签名数据 |

**请求示例**
```bash
curl -X POST https://m.ibmxi.com/api/robot/user/reg \
  -H "Content-Type: application/json" \
  -H "authorization: your-auth-key" \
  -d '{
    "address": "0x1234567890abcdef1234567890abcdef12345678",
    "parentAddress": "0xabcdef1234567890abcdef1234567890abcdef12",
    "signature": "0x签名结果"
  }'
```

### 4.2 返回数据

**成功响应示例**
```json
{
  "code": 200,
  "msg": "操作成功",
  "data": "0x1234567890abcdef1234567890abcdef12345678"
}
```

**返回字段说明**

| 字段 | 类型 | 说明 |
|------|------|------|
| code | Integer | 状态码（200：成功） |
| msg | String | 返回消息 |
| data | String | 返回注册的钱包地址 |

**错误响应示例**
```json
{
  "code": 1,
  "msg": "参数丢失",
  "data": null
}
```

**可能的错误码**

| 错误码 | 说明 |
|--------|------|
| 1 | 参数丢失（address、parentAddress 或 signature 为空） |
| 401 | 未授权（缺少或错误的 authorization 字段） |
| 1001 | 无权限访问（authorization 值不正确） |

**注意事项**
- `address`、`parentAddress` 和 `signature` 都是必填项，不能为空
- 地址应为有效的以太坊地址格式（0x开头，共42个字符）
- 签名用于验证用户对该地址的所有权
- 首次注册会自动记录用户的IP地址和地理位置信息

---

## 5. 获取用户信息 `/robot/user/info`

根据钱包地址获取用户的详细信息，包括基本信息、账户状态、推荐关系和权限等。

### 5.1 请求信息

**接口地址**
```
GET /robot/user/info
```

**授权要求**
- 必须在请求 Header 中包含 `authorization` 字段
- `authorization` 值应与服务器配置的密钥相匹配

**请求参数**

| 参数名 | 类型 | 必填 | 说明 | 示例 |
|--------|------|------|------|------|
| address | String | 是 | 用户钱包地址（机器人用户标识） | 0x001 |

**请求示例**

使用 curl：
```bash
curl -X GET "https://m.ibmxi.com/api/robot/user/info?address=0x001" \
  -H "authorization: your-auth-key"
```

使用 JavaScript：
```javascript
const response = await fetch('/robot/user/info?address=0x001', {
  method: 'GET',
  headers: {
    'authorization': 'your-auth-key'
  }
});
const result = await response.json();
console.log(result);
```

### 5.2 返回数据

**成功响应示例**
```json
{
  "code": 200,
  "msg": "成功",
  "data": {
    "autoid": 1,
    "address": "0x001",
    "parentAddress": "0xparent",
    "signature": null,
    "tel": "13118181818",
    "email": "1@163.com",
    "username": "系统机器人(勿动)",
    "idcard": "--",
    "bankcard": "--",
    "khyh": "--",
    "khzh": "--",
    "xyz": 80,
    "writedate": "2019-12-03 13:00:00",
    "recomcode": "888888",
    "parent": "666666",
    "idcardstate": "completed",
    "minlevel": "0",
    "member": "test001",
    "wx": "567",
    "wximg": "567",
    "zfb": "567",
    "zfbimg": "567",
    "areacode": "86",
    "state": "enable",
    "disableinfo": null,
    "imitate": "enable",
    "directPeople": 277,
    "unDirectPeople": 65,
    "directCoinNum": "0",
    "indirectPeople": 313,
    "indirectCoinNum": "0",
    "unIndirectPeople": 72,
    "superuser": "enable",
    "userIp": "183.94.150.15",
    "registerIp": null,
    "superuserGas": "0",
    "discount": "0",
    "tradNum": "0",
    "cycleState": "loss",
    "minerState": "disable",
    "reputation": "100",
    "flagWithdraw": 1,
    "flagOrder": 1,
    "territorial": null,
    "nation": null,
    "remark": null,
    "salesmanName": null,
    "flagVirtual": 0
  }
}
```

**返回字段说明（data 对象）**

**基本信息**

| 字段 | 类型 | 说明 |
|------|------|------|
| autoid | Integer | 用户自增ID（系统内部唯一标识） |
| address | String | 钱包地址（机器人用户唯一标识） |
| parentAddress | String | 上级推荐人的钱包地址 |
| signature | String | 地址签名（用于验证地址所有权） |
| username | String | 用户名/真实姓名 |
| tel | String | 手机号码 |
| email | String | 电子邮箱 |
| areacode | String | 国家区号（如 86 代表中国） |

**身份认证信息**

| 字段 | 类型 | 说明 |
|------|------|------|
| idcard | String | 身份证号码 |
| idcardstate | String | 实名认证状态：<br/>- no：未认证<br/>- review：审核中<br/>- completed：已完成<br/>- reject：驳回 |
| zm | String | 证件正面照片URL |
| fm | String | 证件反面照片URL |
| sc | String | 手持证件照片URL |

**账户状态**

| 字段 | 类型 | 说明 |
|------|------|------|
| state | String | 账户状态：<br/>- enable：启用<br/>- disable：禁用 |
| disableinfo | String | 账户禁用原因 |
| minerState | String | 矿机状态：enable 或 disable |
| cycleState | String | 周期状态：如 loss、profit 等 |

**等级与权限**

| 字段 | 类型 | 说明 |
|------|------|------|
| minlevel | String | 会员等级（0, VIP1, VIP2 等） |
| member | String | 账户类型/会员身份 |
| superuser | String | 是否超级用户：enable 或 disable |
| imitate | String | 是否签约币币交易：<br/>- enable：已签约<br/>- disable：未签约 |
| flagWithdraw | Integer | 是否允许提币：1=允许，0=禁止 |
| flagOrder | Integer | 是否允许下单：1=允许，0=禁止 |
| flagVirtual | Integer | 是否为虚拟用户：1=是，0=否 |

**推荐关系与业绩**

| 字段 | 类型 | 说明 |
|------|------|------|
| recomcode | String | 用户的推荐码（邀请他人时使用） |
| parent | String | 上级推荐人标识 |
| directPeople | Integer | 直接推荐人数 |
| unDirectPeople | Integer | 未激活的直接推荐人数 |
| indirectPeople | Integer | 间接推荐人数 |
| unIndirectPeople | Integer | 未激活的间接推荐人数 |
| directCoinNum | String | 直接推荐的成交额 |
| indirectCoinNum | String | 间接推荐的成交额 |

**交易与结算**

| 字段 | 类型 | 说明 |
|------|------|------|
| tradNum | String | 交易笔数 |
| discount | String | 手续费折扣率 |
| superuserGas | String | 超级用户手续费 |
| xyz | Integer | 信用值（0-100） |
| reputation | String | 信誉评分 |

**收款方式**

| 字段 | 类型 | 说明 |
|------|------|------|
| bankcard | String | 银行卡号 |
| khyh | String | 开户银行 |
| khzh | String | 开户支行 |
| wx | String | 微信账号 |
| wximg | String | 微信收款码图片URL |
| zfb | String | 支付宝账号 |
| zfbimg | String | 支付宝收款码图片URL |

**地理位置信息**

| 字段 | 类型 | 说明 |
|------|------|------|
| userIp | String | 用户当前IP地址 |
| registerIp | String | 用户注册时的IP地址 |
| territorial | String | 用户所在领地 |
| nation | String | 用户所在国家 |

**其他字段**

| 字段 | 类型 | 说明 |
|------|------|------|
| writedate | Date | 注册时间 |
| remark | String | 备注信息 |
| salesmanName | String | 销售员名称 |
| googlekey | String | 谷歌验证器密钥 |
| loginpwd | String | 登录密码（已加密） |
| tradpwd | String | 交易密码（已加密） |

**错误响应示例**

参数丢失错误：
```json
{
  "code": 1,
  "msg": "参数丢失",
  "data": null
}
```

授权错误：
```json
{
  "code": 401,
  "msg": "无权限访问",
  "data": null
}
```

**可能的错误码**

| 错误码 | 说明 |
|--------|------|
| 1 | 参数丢失（address 为空或格式错误） |
| 401 | 未授权（缺少 authorization 字段） |
| 1001 | 无权限访问（authorization 值不正确） |
| 500 | 服务器错误（用户不存在或数据库异常） |

**注意事项**
- `address` 参数是必填的，不能为空
- 返回的某些字段可能为 null 或空字符串，表示该信息未填写
- 敏感字段（如银行卡号、身份证号）在实际应用中可能被部分隐藏
- `flagWithdraw` 和 `flagOrder` 为 0 时表示该用户被限制相关操作
- `state` 为 "disable" 且 `disableinfo` 有值时，表示账户被禁用及原因
- 推荐关系字段可用于统计用户的推广业绩

---

## 6. 通用说明

### 6.1 响应结构

所有接口返回统一的响应结构：

```json
{
  "code": 200,
  "msg": "操作成功",
  "data": {}
}
```

| 字段 | 类型 | 说明 |
|------|------|------|
| code | Integer | 状态码（200：成功；500：失败） |
| msg | String | 返回消息 |
| data | Object/Array | 返回数据（具体结构见各接口说明） |

### 6.2 错误码说明

| 错误码 | 说明 |
|--------|------|
| 200 | 请求成功 |
| 500 | 服务器内部错误 |
| 参数错误 | 缺少必填参数或参数格式不正确 |

### 6.3 精度说明

- **价格精度（priceAccuracy）**：从 `/robot/last` 接口返回，表示价格保留的小数位数
- **数量精度（amountAccuracy）**：从 `/robot/last` 接口返回，表示数量保留的小数位数
- 前端显示价格和数量时，应根据这两个精度字段进行格式化

示例（JavaScript）：
```javascript
// 假设从 /robot/last 获取到
const priceAccuracy = 2;  // 价格精度
const amountAccuracy = 3; // 数量精度

// 格式化价格
const price = 65234.56789;
const formattedPrice = price.toFixed(priceAccuracy); // "65234.57"

// 格式化数量
const amount = 1.23456789;
const formattedAmount = amount.toFixed(amountAccuracy); // "1.235"
```

### 6.4 使用建议

1. **获取交易对信息流程**：
    - 首先调用 `/robot/last` 获取交易对的基础信息（价格、精度等）
    - 根据需要调用 `/robot/last_count` 获取K线数据用于图表展示
    - 调用 `/robot/orders` 获取最近成交订单用于展示市场活跃度

2. **轮询建议**：
    - 价格信息（`/robot/last`）：建议每 3-5 秒轮询一次
    - K线数据（`/robot/last_count`）：建议每 10-30 秒轮询一次（根据周期调整）
    - 成交订单（`/robot/orders`）：建议每 5-10 秒轮询一次

3. **性能优化**：
    - 如需实时推送，建议使用 WebSocket 接口（见 `ws-api.md`）
    - REST API 适合初始化数据和按需查询场景

---

## 7. 完整示例（JavaScript）

```javascript
// 基础配置
const BASE_URL = 'http://your-host:port/robot';
const MAINCOIN = 'USDT';
const TRADCOIN = 'BTC';

// 1. 获取最新价格
async function getLastPrice() {
    const response = await fetch(
        `${BASE_URL}/last?maincoin=${MAINCOIN}&tradcoin=${TRADCOIN}`
    );
    const result = await response.json();

    if (result.code === 200) {
        const coinInfo = result.data;
        console.log('当前价格:', coinInfo.price);
        console.log('价格精度:', coinInfo.priceAccuracy);
        console.log('数量精度:', coinInfo.amountAccuracy);
        return coinInfo;
    } else {
        console.error('获取价格失败:', result.msg);
        return null;
    }
}

// 2. 获取K线数据
async function getKlineData(mine = '1m', count = 20) {
    const response = await fetch(
        `${BASE_URL}/last_count?maincoin=${MAINCOIN}&tradcoin=${TRADCOIN}&count=${count}&mine=${mine}`
    );
    const result = await response.json();

    if (result.code === 200) {
        console.log('K线数据:', result.data);
        return result.data;
    } else {
        console.error('获取K线失败:', result.msg);
        return [];
    }
}

// 3. 获取成交订单
async function getOrders(count = 20) {
    const response = await fetch(
        `${BASE_URL}/orders?maincoin=${MAINCOIN}&tradcoin=${TRADCOIN}&count=${count}`
    );
    const result = await response.json();

    if (result.code === 200) {
        console.log('成交订单:', result.data);
        return result.data;
    } else {
        console.error('获取订单失败:', result.msg);
        return [];
    }
}

// 初始化
async function init() {
    // 获取基础信息
    const coinInfo = await getLastPrice();

    // 获取1分钟K线（最近20条）
    const klineData = await getKlineData('1m', 20);

    // 获取最近成交订单（20条）
    const orders = await getOrders(20);

    // 根据精度格式化显示
    if (coinInfo) {
        const price = parseFloat(coinInfo.price);
        const formattedPrice = price.toFixed(coinInfo.priceAccuracy);
        console.log('格式化价格:', formattedPrice);
    }
}

// 启动定时轮询
setInterval(async () => {
    await getLastPrice();
    await getOrders(10);
}, 5000); // 每5秒更新一次

init();
```

---

## 8. 与 WebSocket API 的配合使用

建议结合使用 REST API 和 WebSocket API：

- **初始化阶段**：使用 REST API 获取初始数据
    - `/robot/last` 获取交易对信息和精度
    - `/robot/last_count` 获取历史K线用于图表初始化
    - `/robot/orders` 获取历史成交订单

- **实时更新阶段**：使用 WebSocket 订阅实时推送
    - 订阅 `USDT_BTC_trad_1m` 获取实时行情、委托和K线更新
    - WebSocket 推送的数据包含 `buy`、`sell`、`top`、`info`、`kline` 等字段

详见 WebSocket API 文档：`doc/ws-api.md`

---

如有疑问或需要补充其他接口文档，请联系开发团队。

