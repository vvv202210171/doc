# C2C API 快速参考卡片

**基础 URL:** `https://m.biyiex.top/api`

---

## 📋 快速索引表

| 接口 | 方法 | 地址 | 权限 | 说明 |
|------|------|------|------|------|
| 创建商户 | POST | `/c2c/merchant/create` | ✅ | 用户创建商户 |
| 重新提交商户 | POST | `/c2c/merchant/recommit` | ✅ | 拒绝后重新提交 |
| 获取商户详情 | GET | `/c2c/merchant/detail` | ✅ | 获取当前商户 |
| 商户编码查询 | GET | `/c2c/merchant/code/{code}` | ❌ | 查询其他商户 |
| 获取保证金 | GET | `/c2c/merchant/margin` | ❌ | 获取保证金列表 |
| 更新保证金 | POST | `/c2c/merchant/update-margin` | ✅ | 激活商户 |
| 创建收款方式 | POST | `/c2c/collway/create` | ✅ | 添加微信/支付宝/银行 |
| 解绑收款方式 | GET | `/c2c/collway/unbind?id=xx` | ✅ | 删除收款方式 |
| 获取所有收款方式 | POST | `/c2c/collway/all` | ✅ | 列出所有收款方式 |

---

## 🔥 最常用的 3 个请求

### 1️⃣ 创建商户
```bash
curl -X POST 'https://m.biyiex.top/api/c2c/merchant/create' \
  -H 'Content-Type: application/json' \
  -H 'Authorization: Bearer TOKEN' \
  -d '{
    "name": "商户名称",
    "phone": "13800138000"
  }'
```

### 2️⃣ 添加微信收款
```bash
curl -X POST 'https://m.biyiex.top/api/c2c/collway/create' \
  -H 'Content-Type: application/json' \
  -H 'Authorization: Bearer TOKEN' \
  -d '{
    "collWay": "wx",
    "wxUrl": "https://example.com/qr.png",
    "wxAcc": "wechat123",
    "wxName": "张三"
  }'
```

### 3️⃣ 添加银行卡收款
```bash
curl -X POST 'https://m.biyiex.top/api/c2c/collway/create' \
  -H 'Content-Type: application/json' \
  -H 'Authorization: Bearer TOKEN' \
  -d '{
    "collWay": "bank",
    "bankName": "工商银行",
    "bankAcc": "6222022409001234567",
    "bankHolder": "张三",
    "bankAddr": "北京朝阳区支行"
  }'
```

---

## 🎯 常见参数值

### 商户类型
- `PERSONAL` - 个人商户
- `PLATFORM` - 平台商户

### 商户状态
- `PENDING` - 待审核
- `ACTIVE` - 已激活
- `SUSPENDED` - 已暂停
- `REJECTED` - 已拒绝

### 收款方式
- `wx` - 微信
- `alipay` - 支付宝
- `bank` - 银行卡

---

## ✅ 请求检查清单

创建商户前：
- [ ] 商户名称（name）不为空
- [ ] 联系电话（phone）不为空
- [ ] token 有效且未过期

添加收款方式前：
- [ ] 选择正确的 collWay（wx/alipay/bank）
- [ ] 根据 collWay 填写对应字段：
  - **wx**: wxUrl, wxAcc, wxName
  - **alipay**: alipayUrl, alipayAcc, alipayName
  - **bank**: bankName, bankAcc, bankHolder
- [ ] token 有效且未过期

---

## 🚨 常见错误

| 错误 | 原因 | 解决 |
|------|------|------|
| 401 | token 无效或过期 | 重新登录获取 token |
| 参数错误 | 字段为空或类型错误 | 检查请求体格式 |
| 商户已存在 | 用户已有商户 | 使用 recommit 修改或获取现有商户 |
| 收款码已存在 | wxUrl/alipayUrl/bankAcc 重复 | 删除旧记录后重试 |
| 500 | 服务器错误 | 联系技术支持 |

---

## 📱 在 Postman 中快速导入

1. **打开 Postman**
2. 点击 **Import** → **Raw text**
3. 复制上面任意 curl 命令粘贴
4. 点击 **Import** 完成

或直接导入完整 Collection：
```
postman_collection.json
```

---

## 💡 Tips

- 💵 **保证金:** 大多数商户需要提交保证金才能激活，使用 `/c2c/merchant/update-margin`
- 🔄 **重新提交:** 如果商户被拒绝，修改后使用 `/c2c/merchant/recommit`
- 📸 **收款码:** URL 可以是图片链接或 Base64 编码的图片
- 🏷️ **标签:** `tag` 字段用于区分多个相同类型的收款方式（如多个微信账户）

---

**完整文档:** [C2C_API_DOCUMENT.md](C2C_API_DOCUMENT.md) 或 [C2C_API_DOCUMENT.html](C2C_API_DOCUMENT.html)

