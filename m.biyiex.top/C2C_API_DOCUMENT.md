# C2C API 接口文档

**基础 URL：** `https://m.biyiex.top/api`

**更新时间：** 2026年3月2日

---

## 📑 目录

### C2C 商户管理接口
1. [创建商户](#1-创建商户)
2. [重新提交商户信息](#2-重新提交商户信息)
3. [获取商户详情](#3-获取商户详情)
4. [根据商户编码查询](#4-根据商户编码查询)
5. [获取保证金信息](#5-获取保证金信息)
6. [更新保证金](#6-更新保证金)

### C2C 收款方式接口
7. [创建收款方式](#7-创建收款方式)
8. [解绑收款方式](#8-解绑收款方式)
9. [获取所有收款方式](#9-获取所有收款方式)

---

## C2C 商户管理接口

### 1. 创建商户

**描述：** 用户创建 C2C 商户

**请求方法：** `POST`

**请求地址：** `/c2c/merchant/create`

**完整 URL：** `https://m.biyiex.top/api/c2c/merchant/create`

**权限要求：** ✅ 需要认证（需要登录）

**请求头：**
```
Content-Type: application/json
Authorization: Bearer <your_token>
```

**请求参数（Body）：**

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| name | string | ✅ | 商户全称，不能重复 |
| phone | string | ✅ | 商户联系手机号 |
| type | string | ❌ | 商户类型：`PERSONAL`（个人）、`PLATFORM`（平台），默认 `PERSONAL` |

**请求示例（Curl）：**
```bash
curl --location --request POST 'https://m.biyiex.top/api/c2c/merchant/create' \
--header 'Content-Type: application/json' \
--header 'Authorization: Bearer YOUR_TOKEN' \
--data-raw '{
  "name": "我的商户",
  "phone": "13800138000",
  "type": "PERSONAL"
}'
```

**请求示例（JSON）：**
```json
{
  "name": "我的商户",
  "phone": "13800138000",
  "type": "PERSONAL"
}
```

**响应示例（成功 - 200）：**
```json
{
  "code": 0,
  "msg": "success",
  "data": {
    "id": 1,
    "merchantCode": "MCH20260302001",
    "name": "我的商户",
    "member": "U10001",
    "type": "PERSONAL",
    "status": "PENDING",
    "phone": "13800138000",
    "margin": 0.00,
    "marginCoin": "USDT",
    "verifiedStatus": 1,
    "verifiedAt": null,
    "verificationNote": null,
    "createdBy": "U10001",
    "createdAt": "2026-03-02T10:30:00",
    "updatedBy": "U10001",
    "updatedAt": "2026-03-02T10:30:00"
  }
}
```

**响应参数说明：**

| 参数 | 类型 | 说明 |
|------|------|------|
| id | Long | 商户 ID |
| merchantCode | string | 商户唯一编码 |
| name | string | 商户名称 |
| member | string | 所属用户 ID |
| type | string | 商户类型（PERSONAL/PLATFORM） |
| status | string | 商户状态（PENDING-待审核、ACTIVE-已激活、SUSPENDED-已暂停、REJECTED-已拒绝） |
| phone | string | 联系电话 |
| margin | BigDecimal | 保证金金额 |
| marginCoin | string | 保证金币种 |
| verifiedStatus | integer | 审核状态（1-待审核、2-已通过、3-已拒绝） |
| verifiedAt | datetime | 审核时间 |
| verificationNote | string | 审核备注/拒绝原因 |
| createdAt | datetime | 创建时间 |
| updatedAt | datetime | 更新时间 |

**错误响应：**

| 错误码 | 说明 | 解决方案 |
|--------|------|--------|
| 400 | 参数错误（name 或 phone 为空） | 检查请求参数是否完整 |
| 401 | 未授权（token 过期或无效） | 重新登录获取 token |
| 409 | 商户已存在 | 该用户已经创建过商户，使用推荐或编辑接口 |

---

### 2. 重新提交商户信息

**描述：** 商户信息被后台拒绝后，重新提交修改后的信息

**请求方法：** `POST`

**请求地址：** `/c2c/merchant/recommit`

**完整 URL：** `https://m.biyiex.top/api/c2c/merchant/recommit`

**权限要求：** ✅ 需要认证

**请求头：**
```
Content-Type: application/json
Authorization: Bearer <your_token>
```

**请求参数（Body）：**

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| name | string | ✅ | 修改后的商户名称 |
| phone | string | ✅ | 修改后的联系电话 |

**请求示例（Curl）：**
```bash
curl --location --request POST 'https://m.biyiex.top/api/c2c/merchant/recommit' \
--header 'Content-Type: application/json' \
--header 'Authorization: Bearer YOUR_TOKEN' \
--data-raw '{
  "name": "我的商户-修改版",
  "phone": "13900139000"
}'
```

**响应示例（成功 - 200）：**
```json
{
  "code": 0,
  "msg": "success",
  "data": {
    "id": 1,
    "merchantCode": "MCH20260302001",
    "name": "我的商户-修改版",
    "member": "U10001",
    "type": "PERSONAL",
    "status": "PENDING",
    "phone": "13900139000",
    "verifiedStatus": 1,
    "verifiedAt": null,
    "verificationNote": null,
    "createdAt": "2026-03-02T10:30:00",
    "updatedAt": "2026-03-02T11:00:00"
  }
}
```

---

### 3. 获取商户详情

**描述：** 获取当前登录用户的商户信息

**请求方法：** `GET`

**请求地址：** `/c2c/merchant/detail`

**完整 URL：** `https://m.biyiex.top/api/c2c/merchant/detail`

**权限要求：** ✅ 需要认证

**请求参数：** 无（从 token 中提取 memberNo）

**请求示例（Curl）：**
```bash
curl --location --request GET 'https://m.biyiex.top/api/c2c/merchant/detail' \
--header 'Authorization: Bearer YOUR_TOKEN'
```

**响应示例（成功 - 200）：**
```json
{
  "code": 0,
  "msg": "success",
  "data": {
    "id": 1,
    "merchantCode": "MCH20260302001",
    "name": "我的商户",
    "member": "U10001",
    "type": "PERSONAL",
    "status": "ACTIVE",
    "phone": "13800138000",
    "margin": 1000.00,
    "marginCoin": "USDT",
    "verifiedStatus": 2,
    "verifiedAt": "2026-03-02T10:45:00",
    "verificationNote": null,
    "createdAt": "2026-03-02T10:30:00",
    "updatedAt": "2026-03-02T10:45:00"
  }
}
```

**响应为 null 的情况：**
- 用户尚未创建商户，返回 `null`，此时应调用 [创建商户](#1-创建商户) 接口

---

### 4. 根据商户编码查询

**描述：** 根据商户编码查询商户信息（用于查看其他商户）

**请求方法：** `GET`

**请求地址：** `/c2c/merchant/code/{merchantCode}`

**完整 URL：** `https://m.biyiex.top/api/c2c/merchant/code/{merchantCode}`

**权限要求：** ❌ 无需认证（公开接口）

**路径参数：**

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| merchantCode | string | ✅ | 商户编码，例如 `MCH20260302001` |

**请求示例（Curl）：**
```bash
curl --location --request GET 'https://m.biyiex.top/api/c2c/merchant/code/MCH20260302001'
```

**响应示例（成功 - 200）：**
```json
{
  "code": 0,
  "msg": "success",
  "data": {
    "id": 1,
    "merchantCode": "MCH20260302001",
    "name": "我的商户",
    "member": "U10001",
    "type": "PERSONAL",
    "status": "ACTIVE",
    "phone": "13800138000",
    "margin": 1000.00,
    "marginCoin": "USDT",
    "verifiedStatus": 2,
    "verifiedAt": "2026-03-02T10:45:00",
    "verificationNote": null
  }
}
```

---

### 5. 获取保证金信息

**描述：** 获取可用的保证金金额列表

**请求方法：** `GET`

**请求地址：** `/c2c/merchant/margin`

**完整 URL：** `https://m.biyiex.top/api/c2c/merchant/margin`

**权限要求：** ❌ 无需认证

**请求参数：** 无

**请求示例（Curl）：**
```bash
curl --location --request GET 'https://m.biyiex.top/api/c2c/merchant/margin'
```

**响应示例（成功 - 200）：**
```json
{
  "code": 0,
  "msg": "success",
  "data": [
    "1000",
    "USDT"
  ]
}
```

**响应说明：**
- 返回数组，第一个元素为保证金金额，第二个元素为币种

---

### 6. 更新保证金

**描述：** 提交保证金，激活商户（状态从 PENDING 变为 ACTIVE）

**请求方法：** `POST`

**请求地址：** `/c2c/merchant/update-margin`

**完整 URL：** `https://m.biyiex.top/api/c2c/merchant/update-margin`

**权限要求：** ✅ 需要认证

**请求头：**
```
Authorization: Bearer <your_token>
```

**请求参数：** 无（Body 为空）

**请求示例（Curl）：**
```bash
curl --location --request POST 'https://m.biyiex.top/api/c2c/merchant/update-margin' \
--header 'Authorization: Bearer YOUR_TOKEN'
```

**响应示例（成功 - 200）：**
```json
{
  "code": 0,
  "msg": "success",
  "data": {
    "id": 1,
    "merchantCode": "MCH20260302001",
    "name": "我的商户",
    "member": "U10001",
    "status": "ACTIVE",
    "margin": 1000.00,
    "marginCoin": "USDT",
    "verifiedStatus": 2,
    "verifiedAt": "2026-03-02T10:45:00",
    "updatedAt": "2026-03-02T11:00:00"
  }
}
```

---

## C2C 收款方式接口

### 7. 创建收款方式

**描述：** 为商户添加收款方式（微信、支付宝、银行卡）

**请求方法：** `POST`

**请求地址：** `/c2c/collway/create`

**完整 URL：** `https://m.biyiex.top/api/c2c/collway/create`

**权限要求：** ✅ 需要认证

**请求头：**
```
Content-Type: application/json
Authorization: Bearer <your_token>
```

**请求参数（Body）：**

所有收款方式都需要的共用参数：

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| collWay | string | ✅ | 收款方式：`wx`（微信）、`alipay`（支付宝）、`bank`（银行卡） |
| merchantCode | string | ❌ | 商户编码（可选） |
| tag | string | ❌ | 标记/备注 |

#### 微信收款方式专属参数

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| wxUrl | string | ✅ | 微信收款二维码 URL 或 Base64 |
| wxAcc | string | ✅ | 微信账户/微信号 |
| wxName | string | ✅ | 微信账户真实姓名 |

#### 支付宝收款方式专属参数

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| alipayUrl | string | ✅ | 支付宝收款二维码 URL 或 Base64 |
| alipayAcc | string | ✅ | 支付宝账户/支付宝账号 |
| alipayName | string | ✅ | 支付宝账户真实姓名 |

#### 银行卡收款方式专属参数

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| bankName | string | ✅ | 开户银行名称，例如 `中国工商银行` |
| bankAcc | string | ✅ | 银行账户号，例如 `6222022409001234567` |
| bankHolder | string | ✅ | 持卡人姓名 |
| bankAddr | string | ❌ | 开户行地址，例如 `北京市朝阳区支行` |

**请求示例 1（微信）：**
```bash
curl --location --request POST 'https://m.biyiex.top/api/c2c/collway/create' \
--header 'Content-Type: application/json' \
--header 'Authorization: Bearer YOUR_TOKEN' \
--data-raw '{
  "collWay": "wx",
  "wxUrl": "https://example.com/wx_qrcode.png",
  "wxAcc": "wechat_account_123",
  "wxName": "张三",
  "merchantCode": "MCH20260302001",
  "tag": "主账户"
}'
```

**请求示例 2（支付宝）：**
```bash
curl --location --request POST 'https://m.biyiex.top/api/c2c/collway/create' \
--header 'Content-Type: application/json' \
--header 'Authorization: Bearer YOUR_TOKEN' \
--data-raw '{
  "collWay": "alipay",
  "alipayUrl": "https://example.com/alipay_qrcode.png",
  "alipayAcc": "13800138000",
  "alipayName": "张三",
  "merchantCode": "MCH20260302001"
}'
```

**请求示例 3（银行卡）：**
```bash
curl --location --request POST 'https://m.biyiex.top/api/c2c/collway/create' \
--header 'Content-Type: application/json' \
--header 'Authorization: Bearer YOUR_TOKEN' \
--data-raw '{
  "collWay": "bank",
  "bankName": "中国工商银行",
  "bankAcc": "6222022409001234567",
  "bankHolder": "张三",
  "bankAddr": "北京市朝阳区支行",
  "merchantCode": "MCH20260302001",
  "tag": "工行账户"
}'
```

**响应示例（成功 - 200）：**
```json
{
  "code": 0,
  "msg": "success",
  "data": {
    "id": 1,
    "merchantCode": "MCH20260302001",
    "member": "U10001",
    "collWay": "wx",
    "wxUrl": "https://example.com/wx_qrcode.png",
    "wxAcc": "wechat_account_123",
    "wxName": "张三",
    "alipayUrl": null,
    "alipayAcc": null,
    "alipayName": null,
    "bankName": null,
    "bankAcc": null,
    "bankHolder": null,
    "bankAddr": null,
    "tag": "主账户",
    "state": "enable",
    "writedate": "2026-03-02T10:30:00",
    "writeby": "U10001"
  }
}
```

**错误响应：**

| 错误 | 说明 | 解决方案 |
|------|------|--------|
| 参数错误 | 收款方式对应的必填参数缺失 | 检查请求体，确保 `collWay` 对应的所有必填参数都已提供 |
| 微信收款码已存在 | wxUrl 重复 | 修改或删除旧的微信收款方式 |
| 微信账户已存在 | wxAcc 重复 | 修改或删除旧的微信账户 |
| 支付宝收款码已存在 | alipayUrl 重复 | 修改或删除旧的支付宝收款方式 |
| 支付宝账户已存在 | alipayAcc 重复 | 修改或删除旧的支付宝账户 |
| 银行账户已存在 | bankAcc 重复 | 修改或删除旧的银行账户 |

---

### 8. 解绑收款方式

**描述：** 删除/解绑已添加的收款方式

**请求方法：** `GET`

**请求地址：** `/c2c/collway/unbind`

**完整 URL：** `https://m.biyiex.top/api/c2c/collway/unbind?id={id}`

**权限要求：** ✅ 需要认证

**请求参数（Query）：**

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| id | Long | ✅ | 收款方式 ID |

**请求示例（Curl）：**
```bash
curl --location --request GET 'https://m.biyiex.top/api/c2c/collway/unbind?id=1' \
--header 'Authorization: Bearer YOUR_TOKEN'
```

**响应示例（成功 - 200）：**
```json
{
  "code": 0,
  "msg": "success",
  "data": 1
}
```

**响应说明：** 返回被删除的收款方式 ID

---

### 9. 获取所有收款方式

**描述：** 获取当前登录用户添加的所有收款方式

**请求方法：** `POST`

**请求地址：** `/c2c/collway/all`

**完整 URL：** `https://m.biyiex.top/api/c2c/collway/all`

**权限要求：** ✅ 需要认证

**请求参数：** 无

**请求示例（Curl）：**
```bash
curl --location --request POST 'https://m.biyiex.top/api/c2c/collway/all' \
--header 'Authorization: Bearer YOUR_TOKEN'
```

**响应示例（成功 - 200）：**
```json
{
  "code": 0,
  "msg": "success",
  "data": [
    {
      "id": 1,
      "merchantCode": "MCH20260302001",
      "member": "U10001",
      "collWay": "wx",
      "wxUrl": "https://example.com/wx_qrcode.png",
      "wxAcc": "wechat_account_123",
      "wxName": "张三",
      "tag": "主账户",
      "state": "enable",
      "writedate": "2026-03-02T10:30:00"
    },
    {
      "id": 2,
      "merchantCode": "MCH20260302001",
      "member": "U10001",
      "collWay": "alipay",
      "alipayUrl": "https://example.com/alipay_qrcode.png",
      "alipayAcc": "13800138000",
      "alipayName": "张三",
      "tag": null,
      "state": "enable",
      "writedate": "2026-03-02T10:35:00"
    },
    {
      "id": 3,
      "merchantCode": "MCH20260302001",
      "member": "U10001",
      "collWay": "bank",
      "bankName": "中国工商银行",
      "bankAcc": "6222022409001234567",
      "bankHolder": "张三",
      "bankAddr": "北京市朝阳区支行",
      "tag": "工行账户",
      "state": "enable",
      "writedate": "2026-03-02T10:40:00"
    }
  ]
}
```

**响应参数说明：**

| 参数 | 类型 | 说明 |
|------|------|------|
| id | Long | 收款方式 ID |
| collWay | string | 收款方式（wx/alipay/bank） |
| wxUrl | string | 微信二维码 URL（仅微信方式） |
| wxAcc | string | 微信账户（仅微信方式） |
| wxName | string | 微信真实姓名（仅微信方式） |
| alipayUrl | string | 支付宝二维码 URL（仅支付宝方式） |
| alipayAcc | string | 支付宝账户（仅支付宝方式） |
| alipayName | string | 支付宝真实姓名（仅支付宝方式） |
| bankName | string | 开户银行名称（仅银行卡方式） |
| bankAcc | string | 银行账户号（仅银行卡方式） |
| bankHolder | string | 持卡人姓名（仅银行卡方式） |
| bankAddr | string | 开户行地址（仅银行卡方式） |
| tag | string | 标记/备注 |
| state | string | 状态（enable-启用、disable-禁用） |
| writedate | datetime | 创建时间 |

---

## 📌 通用说明

### 认证方式

所有需要认证的接口都需要在请求头中添加：

```
Authorization: Bearer <your_token>
```

其中 `your_token` 是通过登录接口 `/member/loginByTelOrEmail` 获取的 token。

### 通用错误响应

所有失败响应都遵循以下格式：

```json
{
  "code": -1,
  "msg": "error_message",
  "data": null
}
```

| HTTP 状态码 | 说明 |
|-------------|------|
| 200 | 请求成功 |
| 400 | 参数错误或业务验证失败 |
| 401 | 未授权（token 过期或无效） |
| 500 | 服务器内部错误 |

### 字段验证规则

#### 商户类型（type）

| 值 | 说明 |
|----|------|
| `PERSONAL` | 个人商户（默认） |
| `PLATFORM` | 平台商户 |

#### 商户状态（status）

| 值 | 说明 |
|----|------|
| `PENDING` | 待审核 |
| `ACTIVE` | 已激活 |
| `SUSPENDED` | 已暂停 |
| `REJECTED` | 已拒绝 |

#### 审核状态（verifiedStatus）

| 值 | 说明 |
|----|------|
| `1` | 待审核 |
| `2` | 已通过 |
| `3` | 已拒绝 |

#### 收款方式（collWay）

| 值 | 说明 |
|----|------|
| `wx` | 微信支付 |
| `alipay` | 支付宝 |
| `bank` | 银行卡转账 |

---

## 🔗 快速链接

- [Postman Collection](./postman_collection.json)
- [错误代码参考](./error-codes.md)
- [商户审核流程](./merchant-verification-flow.md)

---

**文档版本：** v1.0  
**最后更新：** 2026年3月2日  
**维护人：** 开发团队

