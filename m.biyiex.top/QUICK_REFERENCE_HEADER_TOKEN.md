# C2C API 快速参考卡片（Header Token 版）

**基础 URL:** `https://www.biyiex.top/api`

**认证方式：** `--header 'token: <your_token>'`（请求头）

---

## 📋 快速索引表

| 接口 | 方法 | 地址 | 权限 | 说明 |
|------|------|------|------|------|
| 创建商户 | POST | `/c2c/merchant/create` | ✅ | 创建商户 |
| 重新提交商户 | POST | `/c2c/merchant/recommit` | ✅ | 修改被拒商户 |
| 获取商户详情 | GET | `/c2c/merchant/detail` | ✅ | 获取当前商户 |
| 商户编码查询 | GET | `/c2c/merchant/code/{code}` | ❌ | 查询其他商户 |
| 获取保证金 | GET | `/c2c/merchant/margin` | ❌ | 获取保证金列表 |
| 更新保证金 | POST | `/c2c/merchant/update-margin` | ✅ | 激活商户 |
| 创建收款方式 | POST | `/c2c/collway/create` | ✅ | 添加收款方式 |
| 解绑收款方式 | GET | `/c2c/collway/unbind?id=xx` | ✅ | 删除收款方式 |
| 获取所有收款方式 | POST | `/c2c/collway/all` | ✅ | 列出所有 |
| **图片上传** ⭐ | POST | **/res/upload** | **✅** | **上传图片** |
| **图片预览** ⭐ | GET | **/res/preview/{resseq}** | **❌** | **预览图片** |

---

## 🔥 最常用的接口示例

### 1️⃣ 创建商户
```bash
curl --location --request POST 'https://www.biyiex.top/api/c2c/merchant/create' \
--header 'Content-Type: application/json' \
--header 'token: UAT20283084048911319041772421382530' \
--data-raw '{
  "name": "商户名称",
  "phone": "13800138000"
}'
```

### 2️⃣ 上传图片 ⭐
```bash
curl --location --request POST 'https://www.biyiex.top/api/res/upload' \
--header 'token: UAT20283084048911319041772421382530' \
--form 'imgurl=@/path/to/image.jpg'

# 返回：{"code":0,"msg":"success","data":[{"resseq":"RES20260302001001"}]}
```

### 3️⃣ 预览图片 ⭐
```bash
# 在浏览器中打开（无需 token）：
https://www.biyiex.top/api/res/preview/RES20260302001001

# 或在 HTML img 标签中：
<img src="https://www.biyiex.top/api/res/preview/RES20260302001001" />
```

### 4️⃣ 添加银行卡收款
```bash
curl --location --request POST 'https://www.biyiex.top/api/c2c/collway/create' \
--header 'Content-Type: application/json' \
--header 'token: UAT20283084048911319041772421382530' \
--data-raw '{
  "collWay": "bank",
  "bankName": "工商银行",
  "bankAcc": "6222022409001234567",
  "bankHolder": "张三",
  "bankAddr": "北京朝阳区支行"
}'
```

---

## 📸 图片上传完整流程

### 步骤 1: 上传图片
```bash
curl --location --request POST 'https://www.biyiex.top/api/res/upload' \
--header 'token: UAT20283084048911319041772421382530' \
--form 'imgurl=@./wechat_qrcode.png'

# 得到 resseq：RES202603020001
```

### 步骤 2: 构造预览 URL
```
https://www.biyiex.top/api/res/preview/RES202603020001
```

### 步骤 3: 创建微信收款方式（使用上传的图片）
```bash
curl --location --request POST 'https://www.biyiex.top/api/c2c/collway/create' \
--header 'Content-Type: application/json' \
--header 'token: UAT20283084048911319041772421382530' \
--data-raw '{
  "collWay": "wx",
  "wxUrl": "https://www.biyiex.top/api/res/preview/RES202603020001",
  "wxAcc": "wechat_account",
  "wxName": "张三"
}'
```

### 步骤 4: 前端显示图片
```html
<img src="https://www.biyiex.top/api/res/preview/RES202603020001" 
     alt="微信收款码" 
     width="300">
```

---

## 📸 支持的图片格式

| 格式 | 扩展名 | 说明 |
|------|--------|------|
| .jpg, .jpeg | 最常用 ⭐ | 照片格式 |
| .png | 透明背景 | 常用格式 |
| .gif | 动画 | 动图 |
| .bmp | 位图 | 无压缩 |
| .svg | 矢量图 | 可缩放 |
| .tiff | 高质量 | 专业用 |

---

## ✅ 请求格式总结

### 标准 curl 格式（带 token header）

**GET 请求：**
```bash
curl --location --request GET 'https://www.biyiex.top/api/c2c/merchant/detail' \
--header 'token: YOUR_TOKEN'
```

**POST 请求（JSON）：**
```bash
curl --location --request POST 'https://www.biyiex.top/api/c2c/merchant/create' \
--header 'Content-Type: application/json' \
--header 'token: YOUR_TOKEN' \
--data-raw '{"name":"商户","phone":"13800138000"}'
```

**POST 请求（文件上传）：**
```bash
curl --location --request POST 'https://www.biyiex.top/api/res/upload' \
--header 'token: YOUR_TOKEN' \
--form 'imgurl=@/path/to/image.jpg'
```

**GET 请求（带查询参数）：**
```bash
curl --location --request GET 'https://www.biyiex.top/api/c2c/collway/unbind?id=1' \
--header 'token: YOUR_TOKEN'
```

**无需 token 的公开接口：**
```bash
curl --location --request GET 'https://www.biyiex.top/api/c2c/merchant/margin'

curl --location --request GET 'https://www.biyiex.top/api/res/preview/RES20260302001001'
```

---

## 🚨 常见错误

| 错误 | 原因 | 解决 |
|------|------|------|
| 401 | token 无效或过期 | 重新登录获取新 token，在 header 中加上 `--header 'token: YOUR_TOKEN'` |
| 参数错误 | 缺少必填参数或格式错误 | 检查请求 body 的 JSON 格式 |
| 404 | 资源不存在 | 确认资源 ID 或 code 是否正确 |
| 不支持的文件类型 | 上传了不支持的图片格式 | 只上传 jpg, png, gif, bmp, svg, tiff |
| Content-Type 错误 | 缺少 Content-Type header | POST JSON 数据时加上 `--header 'Content-Type: application/json'` |

---

## 💡 最佳实践

1. **Token 管理**
   ```bash
   # 登录获取 token
   curl -X POST 'https://www.biyiex.top/api/member/loginByTelOrEmail' \
     -H 'Content-Type: application/json' \
     -d '{"account":"user@example.com","areaCode":"+86","device":"web","loginPassword":"password"}'
   
   # 得到 token 后，在需要认证的请求中加上
   --header 'token: YOUR_TOKEN'
   ```

2. **图片上传优化**
   - 推荐 jpg 或 png 格式
   - 文件大小 < 5MB
   - 二维码可以是较小的 PNG（< 1MB）

3. **Header 格式**
   ```bash
   # 正确格式
   --header 'token: UAT20283084048911319041772421382530'
   
   # Token 是一个 header，不是查询参数
   # 在 URL 中不需要加 ?token=
   ```

4. **Postman 中使用**
   - Headers 标签
   - 新增 Key: `token`
   - Value: 粘贴你的 token 值
   - 所有需要认证的请求都会自动添加这个 header

---

**完整文档:** [API_DOCUMENTATION_HEADER_TOKEN.md](./API_DOCUMENTATION_HEADER_TOKEN.md)

