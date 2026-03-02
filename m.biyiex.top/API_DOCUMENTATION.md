# C2C API 接口文档

**基础 URL：** `https://www.biyiex.top/api`

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

### 资源管理接口
10. [图片上传](#10-图片上传)
11. [图片预览](#11-图片预览)

---

## C2C 商户管理接口

### 1. 创建商户

**描述：** 用户创建 C2C 商户

**请求方法：** `POST`

**请求地址：** `/c2c/merchant/create`

**完整 URL：** `https://www.biyiex.top/api/c2c/merchant/create`

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
curl --location --request POST 'https://www.biyiex.top/api/c2c/merchant/create' \
--header 'Content-Type: application/json' \
--header 'Authorization: Bearer YOUR_TOKEN' \
--data-raw '{
  "name": "我的商户",
  "phone": "13800138000",
  "type": "PERSONAL"
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

---

### 2. 重新提交商户信息

**描述：** 商户信息被后台拒绝后，重新提交修改后的信息

**请求方法：** `POST`

**请求地址：** `/c2c/merchant/recommit`

**完整 URL：** `https://www.biyiex.top/api/c2c/merchant/recommit`

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
curl --location --request POST 'https://www.biyiex.top/api/c2c/merchant/recommit' \
--header 'Content-Type: application/json' \
--header 'Authorization: Bearer YOUR_TOKEN' \
--data-raw '{
  "name": "我的商户-修改版",
  "phone": "13900139000"
}'
```

---

### 3. 获取商户详情

**描述：** 获取当前登录用户的商户信息

**请求方法：** `GET`

**请求地址：** `/c2c/merchant/detail`

**完整 URL：** `https://www.biyiex.top/api/c2c/merchant/detail`

**权限要求：** ✅ 需要认证

**请求参数：** 无（从 token 中提取 memberNo）

**请求示例（Curl）：**
```bash
curl --location --request GET 'https://www.biyiex.top/api/c2c/merchant/detail' \
--header 'Authorization: Bearer YOUR_TOKEN'
```

---

### 4. 根据商户编码查询

**描述：** 根据商户编码查询商户信息（用于查看其他商户）

**请求方法：** `GET`

**请求地址：** `/c2c/merchant/code/{merchantCode}`

**完整 URL：** `https://www.biyiex.top/api/c2c/merchant/code/{merchantCode}`

**权限要求：** ❌ 无需认证（公开接口）

**路径参数：**

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| merchantCode | string | ✅ | 商户编码，例如 `MCH20260302001` |

**请求示例（Curl）：**
```bash
curl --location --request GET 'https://www.biyiex.top/api/c2c/merchant/code/MCH20260302001'
```

---

### 5. 获取保证金信息

**描述：** 获取可用的保证金金额列表

**请求方法：** `GET`

**请求地址：** `/c2c/merchant/margin`

**完整 URL：** `https://www.biyiex.top/api/c2c/merchant/margin`

**权限要求：** ❌ 无需认证

**请求参数：** 无

**请求示例（Curl）：**
```bash
curl --location --request GET 'https://www.biyiex.top/api/c2c/merchant/margin'
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

---

### 6. 更新保证金

**描述：** 提交保证金，激活商户（状态从 PENDING 变为 ACTIVE）

**请求方法：** `POST`

**请求地址：** `/c2c/merchant/update-margin`

**完整 URL：** `https://www.biyiex.top/api/c2c/merchant/update-margin`

**权限要求：** ✅ 需要认证

**请求头：**
```
Authorization: Bearer <your_token>
```

**请求参数：** 无（Body 为空）

**请求示例（Curl）：**
```bash
curl --location --request POST 'https://www.biyiex.top/api/c2c/merchant/update-margin' \
--header 'Authorization: Bearer YOUR_TOKEN'
```

---

## C2C 收款方式接口

### 7. 创建收款方式

**描述：** 为商户添加收款方式（微信、支付宝、银行卡）

**请求方法：** `POST`

**请求地址：** `/c2c/collway/create`

**完整 URL：** `https://www.biyiex.top/api/c2c/collway/create`

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

**请求示例 - 微信：**
```bash
curl --location --request POST 'https://www.biyiex.top/api/c2c/collway/create' \
--header 'Content-Type: application/json' \
--header 'Authorization: Bearer YOUR_TOKEN' \
--data-raw '{
  "collWay": "wx",
  "wxUrl": "https://example.com/wx_qrcode.png",
  "wxAcc": "wechat_account_123",
  "wxName": "张三",
  "tag": "主账户"
}'
```

**请求示例 - 银行卡：**
```bash
curl --location --request POST 'https://www.biyiex.top/api/c2c/collway/create' \
--header 'Content-Type: application/json' \
--header 'Authorization: Bearer YOUR_TOKEN' \
--data-raw '{
  "collWay": "bank",
  "bankName": "中国工商银行",
  "bankAcc": "6222022409001234567",
  "bankHolder": "张三",
  "bankAddr": "北京市朝阳区支行",
  "tag": "工行账户"
}'
```

---

### 8. 解绑收款方式

**描述：** 删除/解绑已添加的收款方式

**请求方法：** `GET`

**请求地址：** `/c2c/collway/unbind`

**完整 URL：** `https://www.biyiex.top/api/c2c/collway/unbind?id={id}`

**权限要求：** ✅ 需要认证

**请求参数（Query）：**

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| id | Long | ✅ | 收款方式 ID |

**请求示例（Curl）：**
```bash
curl --location --request GET 'https://www.biyiex.top/api/c2c/collway/unbind?id=1' \
--header 'Authorization: Bearer YOUR_TOKEN'
```

---

### 9. 获取所有收款方式

**描述：** 获取当前登录用户添加的所有收款方式

**请求方法：** `POST`

**请求地址：** `/c2c/collway/all`

**完整 URL：** `https://www.biyiex.top/api/c2c/collway/all`

**权限要求：** ✅ 需要认证

**请求参数：** 无

**请求示例（Curl）：**
```bash
curl --location --request POST 'https://www.biyiex.top/api/c2c/collway/all' \
--header 'Authorization: Bearer YOUR_TOKEN'
```

---

## 资源管理接口

### 10. 图片上传

**描述：** 上传图片文件（支持多种格式），返回资源序列号用于后续引用

**请求方法：** `POST`

**请求地址：** `/res/upload`

**完整 URL：** `https://www.biyiex.top/api/res/upload`

**权限要求：** ✅ 需要认证（需要登录）

**请求头：**
```
Authorization: Bearer <your_token>
```

**请求参数（Form-Data）：**

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| imgurl | file | ✅ | 图片文件 |

**支持的图片格式：**

| 格式 | 扩展名 | 用途 |
|------|--------|------|
| GIF | .gif | 动画图片 |
| PNG | .png | 透明图片 |
| JPEG | .jpg, .jpeg | 照片 |
| SVG | .svg | 矢量图 |
| BMP | .bmp | 位图 |
| TIFF | .tiff | 高质量图像 |
| 其他 | .dxf, .pcx, .ufo, .tga, .raw, .ai, .fpx, .eps | 专业格式 |

**请求示例（Curl）：**
```bash
curl --location --request POST 'https://www.biyiex.top/api/res/upload' \
--header 'Authorization: Bearer YOUR_TOKEN' \
--form 'imgurl=@"/path/to/image.jpg"'
```

**请求示例（使用 Postman）：**
1. 请求方法：POST
2. URL：`https://www.biyiex.top/api/res/upload`
3. Headers：`Authorization: Bearer YOUR_TOKEN`
4. Body：选择 form-data 模式
5. 添加字段：`imgurl` (type: File)，选择要上传的图片文件
6. 点击 Send

**响应示例（成功 - 200）：**
```json
{
  "code": 0,
  "msg": "success",
  "data": [
    {
      "resseq": "RES20260302001001"
    }
  ]
}
```

**响应参数说明：**

| 参数 | 类型 | 说明 |
|------|------|------|
| resseq | string | 资源序列号，用于后续预览和引用 |

**错误响应：**

| 错误 | 说明 | 解决方案 |
|------|------|--------|
| 参数错误 | 未提供图片文件 | 检查是否正确上传了图片文件 |
| 不支持的文件类型 | 上传的文件格式不在允许列表中 | 只上传支持的图片格式（gif, png, jpg, jpeg 等） |
| 401 | token 无效或过期 | 重新登录获取有效的 token |
| 500 | 服务器错误 | 检查文件大小是否过大，联系技术支持 |

---

### 11. 图片预览

**描述：** 根据资源序列号预览图片，支持浏览器缓存，首次加载后 7 天内不需重新下载

**请求方法：** `GET`

**请求地址：** `/res/preview/{resseq}`

**完整 URL：** `https://www.biyiex.top/api/res/preview/{resseq}`

**权限要求：** ❌ 无需认证（公开接口）

**路径参数：**

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| resseq | string | ✅ | 资源序列号，来自图片上传接口返回值 |

**返回类型：** 图片文件（Content-Type: image/jpeg）

**请求示例（Curl）：**
```bash
# 在浏览器中预览
https://www.biyiex.top/api/res/preview/RES20260302001001

# 或用 curl 下载
curl -H 'Authorization: Bearer YOUR_TOKEN' \
  'https://www.biyiex.top/api/res/preview/RES20260302001001' \
  -o downloaded_image.jpg
```

**请求示例（HTML img 标签）：**
```html
<img src="https://www.biyiex.top/api/res/preview/RES20260302001001" alt="预览图片" />
```

**请求示例（JavaScript/Fetch）：**
```javascript
// 获取图片 URL
const imageUrl = 'https://www.biyiex.top/api/res/preview/RES20260302001001';

// 在 img 标签中显示
document.getElementById('preview').src = imageUrl;

// 或直接在浏览器中打开
window.open(imageUrl);
```

**响应说明：**

| 响应 | 说明 |
|------|------|
| 200 OK | 成功返回图片文件，响应体为图片二进制数据 |
| 404 Not Found | 资源序列号不存在或已被删除 |
| 500 Internal Server Error | 服务器错误或文件已丢失 |

**缓存策略：**

- **Cache-Control:** `max-age=604800`（7 天缓存）
- **Last-Modified：** 文件最后修改时间
- 首次加载后，7 天内重复访问同一资源不会重新下载

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
| 404 | 资源不存在 |
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

**文档版本：** v1.1  
**最后更新：** 2026年3月2日  
**维护人：** 开发团队

