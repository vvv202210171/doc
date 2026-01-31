# Coincasso WebSocket API 文档

本文档总结项目中后端 WebSocket 服务的使用方式、消息格式、以及如何从返回字段获取“数量精度”和“价格精度”。文档基于代码实现（`com.sdt.socket.*`、`com.sdt.socket.handler.*`）。

---

## 1 概要
- 服务器使用 Netty WebSocket，客户端通过文本帧订阅特定交易对的行情/委托/K 线等数据。
- 鉴权 / 标识通过连接路径携带 `member`（例如：`/ws/{member}`），服务端会在 handshake 时把这个 member 存到 Channel 属性里。

示例连接：
```
ws://api.ibmxi.com/ws/{member}
// 例如：ws://localhost:8080/ws/2111333232
```

---

## 2 连接与鉴权
- 在 `WebSocketPathHandler` 中解析请求 URI，当 URI 以 `/ws/` 开头时，会把 `/ws/{member}` 中的 `{member}` 保存到 Channel 的 Attribute（`SocketConstant.MEMBER`）。
- 服务器内部通过该 member 来识别用户（例如日志中会打印 `客户端发送userId:{}`）。
- 注意：目前鉴权只是**路径中携带 member**，如果需要更严格鉴权（签名、token），需在连接层或应用层另行实现。

---

## 3 订阅（客户端 -> 服务器）格式
- 文本消息格式（必须为纯文本消息）:

```
maincoin_tradcoin_type_mine
```

- 各段含义：
  - `maincoin`：主币（如 `USDT`, `CNY`...）
  - `tradcoin`：交易币（如 `BTC`, `ETH`...）
  - `type`：模块类型（目前代码中定义在 `Coina`）
    - `trad` — 币币（spot）
  - `mine`：客户端需要的周期或时间粒度（如 `1`, `5`, `15`, `30`, `60`, `240`, `1440` 等）

- 例子：
  - `USDT_BTC_trad_1m`

- 服务端接收到后会：
  - 通过 `KlineChannelManager.subscribe(key, session)` 把当前 Channel 加入对应 key 的 ChannelGroup（key 由 `maincoin_tradcoin_type_mine` 拼接而成）
  - 调用 `TradMessageHandler` 生成返回 JSON 并发送给客户端

---

## 4 心跳（Ping / Pong）
- 客户端可发送文本消息 `ping`，服务端会回复 `pong`。
- 服务端对于非 `ping` 文本消息会调用 `MessageHandlerFactory.handler(ctx.channel(), payload)` 进行处理。
- 建议：每 20-30 秒发送一次 `ping`，若没有收到 `pong` 或连接被断开，进行重连（指数退避）。

---

## 5 服务端推送的消息（币币 / trad）
服务端使用 `TradMessageHandler` 为币币（spot）订阅生成并推送数据。返回 JSON 的主要字段如下：

- `buy`：Array — 买盘委托数组（每项为 Map/JSON，对应后端 `entrust_buy` 的返回）
- `sell`：Array — 卖盘委托数组
- `top`：Array — 最近成交 / top 数据
- `info`：Object — 交易对信息（例：`price`、`marketcontrl` 等）
- `kline`：Object — k 线元信息（含 `amountUnit` / `orderMinUnit` / `tickUnit` / `series`，见下节）
- `flushKlineTradPair`：String — 需要刷新 K 线的交易对 key 列表（以逗号分隔）

示例（简化）：
```json
{
  "buy": [...],
  "sell": [...],
  "top": [...],
  "info": {"price": "65000.12", "marketcontrl": "N"},
  "kline": { /* see next section */ },
  "flushKlineTradPair": ""
}
```

---

## 6 kline 对象详解与精度字段
所有 trad 推送在 `kline.data` 中都会包含三个用于处理精度与量化单位的字段：
- `amountUnit`：数量单位（示例：`"0.0010000000"`）
- `orderMinUnit`：下单最小单位（示例：`"0.0001000000"`）
- `tickUnit`：价格步进（示例：`"0.0000000100"`）
- `series`：K 线序列（由 `KlineUtil.getSeries(list)` 生成，见下）

示例 `kline`：
```json
{
  "funcName":"getChart",
  "message":"Success.",
  "status":"0",
  "ticket":165,
  "data":{
    "amountUnit":"0.0010000000",
    "orderMinUnit":"0.0001000000",
    "tickUnit":"0.0000000100",
    "series":[ ... ]
  },
  "code":200,
  "mine":"1m"
}
```

### 6.1 `series` 数组说明
- `series` 的每一项由 `KlineUtil.getSeries` 生成，常见返回格式为每个 k 线点的数组或对象，包含时间戳、开/高/低/收、成交量等。
- 注意：项目中 `KlineUtil.getSeries` 的具体格式请以代码实现为准（上面的示例为常见格式），客户端收到后先打印/查看 `series[0]` 确认索引解释。

---

## 7 精度计算（算法与使用场景）

### 7.1 推荐算法（字符串方法，避免浮点误差）
1. 将单位字符串视为纯文本，去掉前后的空格。
2. 如果不包含小数点，精度为 0。
3. 去掉尾部的 0（右侧 0），然后计算小数点右侧剩余字符数：该长度即为精度（小数位数）。

示例实现（伪代码 / JS）：
```javascript
function calcPrecisionFromUnit(unitStr) {
  if (!unitStr || typeof unitStr !== 'string') return 0;
  const s = unitStr.trim();
  if (!s.includes('.')) return 0;
  // 去掉末尾的 0
  const trimmed = s.replace(/0+$/, '');
  const parts = trimmed.split('.');
  return parts[1] ? parts[1].length : 0;
}
```

### 7.2 使用场景
- `pricePrecision = calcPrecisionFromUnit(tickUnit)`
- `quantityPrecision = calcPrecisionFromUnit(amountUnit)` 或使用 `orderMinUnit` 作为下单最小单位

前端显示/下单时应：
- 显示价格时保留 `pricePrecision` 位小数
- 显示/校验数量时保留 `quantityPrecision` 位小数，并确保下单数量能被 `orderMinUnit` 整除（即数量 % orderMinUnit == 0）

---

## 8 示例
### 8.1 客户端订阅请求
```
// 文本消息（WebSocket TextFrame）
USDT_BTC_trad_1m
```

### 8.2 服务端响应（简化示例 - trad）
```json
{
  "buy": [
    {"price":"65000.12","amount":"0.5"},
    {"price":"64950.00","amount":"1.2"}
  ],
  "sell": [ ... ],
  "top": [ ... ],
  "info": {"price":"65000.12345","marketcontrl":"N"},
  "kline": {
    "funcName":"getChart",
    "message":"Success.",
    "status":"0",
    "ticket":165,
    "data":{
      "amountUnit":"0.0010000000",
      "orderMinUnit":"0.0001000000",
      "tickUnit":"0.0000000100",
      "series":[
        “20251223172100|1.919|1.919|1.918|1.919|1792.092228”,数据分别为时间 ，开盘价，最高价，最低价，最后价，成交数
      “20251223172200|1.919|1.92|1.919|1.92|1.043188”
      ]
    },
    "code":200,
    "mine":"1m"
  },
  "flushKlineTradPair":""
}
```

---

## 9 浏览器端 JS 示例（简明）
```javascript
const member = '2111333232';
const ws = new WebSocket('ws://localhost:8080/ws/' + member);
let pingInterval;

ws.addEventListener('open', () => {
  console.log('ws open');
  // 心跳
  pingInterval = setInterval(() => {
    if (ws.readyState === WebSocket.OPEN) ws.send('ping');
  }, 20000);
  // 订阅
  ws.send('USDT_BTC_trad_1m');
});

ws.addEventListener('message', (ev) => {
  const text = ev.data;
  if (text === 'pong') return;
  try {
    const data = JSON.parse(text);
    if (data.kline && data.kline.data) {
      const tickUnit = data.kline.data.tickUnit;
      const pricePrecision = calcPrecisionFromUnit(tickUnit);
      // 格式化价格显示
      // parse buy/sell/top 等
    }
  } catch (e) {
    console.error('解析消息失败', e, text);
  }
});

ws.addEventListener('close', () => {
  clearInterval(pingInterval);
  // 重连逻辑（指数退避）
});

function calcPrecisionFromUnit(unitStr) {
  if (!unitStr || typeof unitStr !== 'string') return 0;
  const s = unitStr.trim();
  if (!s.includes('.')) return 0;
  const trimmed = s.replace(/0+$/, '');
  const parts = trimmed.split('.');
  return parts[1] ? parts[1].length : 0;
}
```

---
