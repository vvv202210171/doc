# C2C API 快速参考卡片（更新版）

**基础 URL:** `https://www.biyiex.top/api`

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
| **图片上传** ⭐ | POST | `/res/upload` | ✅ | **上传图片文件** |
| **图片预览** ⭐ | GET | `/res/preview/{resseq}` | ❌ | **预览图片文件** |

---

## 🔥 最常用的接口示例

### 1️⃣ 创建商户
```bash
curl -X POST 'https://www.biyiex.top/api/c2c/merchant/create' \
  -H 'Content-Type: application/json' \
  -H 'Authorization: Bearer TOKEN' \
  -d '{
    "name": "商户名称",
    "phone": "13800138000"
  }'
```

### 2️⃣ 上传图片 ⭐
```bash
curl -X POST 'https://www.biyiex.top/api/res/upload' \
  -H 'Authorization: Bearer TOKEN' \
  -F 'imgurl=@/path/to/image.jpg'

# 返回示例：
# {"code":0,"msg":"success","data":[{"resseq":"RES20260302001001"}]}
```

### 3️⃣ 预览图片 ⭐
```bash
# 在浏览器中打开：
https://www.biyiex.top/api/res/preview/RES20260302001001

# 或在 HTML img 标签中：
<img src="https://www.biyiex.top/api/res/preview/RES20260302001001" />
```

### 4️⃣ 添加银行卡收款
```bash
curl -X POST 'https://www.biyiex.top/api/c2c/collway/create' \
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

## 🎯 图片上传完整流程

### 步骤 1: 上传图片
```bash
# 上传微信收款码
curl -X POST 'https://www.biyiex.top/api/res/upload' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -F 'imgurl=@./wechat_qrcode.png'

# 响应得到 resseq
# {"code":0,"msg":"success","data":[{"resseq":"RES202603020001"}]}
```

### 步骤 2: 创建微信收款方式（使用上传的图片）
```bash
curl -X POST 'https://www.biyiex.top/api/c2c/collway/create' \
  -H 'Content-Type: application/json' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -d '{
    "collWay": "wx",
    "wxUrl": "https://www.biyiex.top/api/res/preview/RES202603020001",
    "wxAcc": "wechat_account",
    "wxName": "张三"
  }'
```

### 步骤 3: 在前端显示图片
```html
<!-- 使用 resseq 预览上传的图片 -->
<img src="https://www.biyiex.top/api/res/preview/RES202603020001" 
     alt="微信收款码" 
     width="300">
```

---

## 📸 支持的图片格式

| 格式 | 说明 |
|------|------|
| .jpg, .jpeg | 最常用的照片格式 ⭐ |
| .png | 支持透明背景的图片 |
| .gif | 动画图片 |
| .bmp | 位图格式 |
| .svg | 矢量图格式 |
| .tiff | 高质量图像 |
| .dxf, .pcx, .ufo, .tga, .raw, .ai, .fpx, .eps | 专业设计格式 |

---

## ✅ 图片上传检查清单

上传前：
- [ ] 选择支持的图片格式（jpg, png, gif, bmp 等）
- [ ] 检查文件大小（通常 < 10MB）
- [ ] 确保 token 有效且未过期
- [ ] 检查网络连接

上传后：
- [ ] 记录返回的 `resseq`（资源序列号）
- [ ] 用 `resseq` 构造预览 URL：`/res/preview/{resseq}`
- [ ] 在创建收款方式时使用完整的预览 URL

---

## 🔗 图片 URL 构造

### 上传图片后返回的 resseq
```
RES202603020001
```

### 构造完整的预览 URL
```
https://www.biyiex.top/api/res/preview/RES202603020001
```

### 在各个场景中使用

**商户认证时上传身份证：**
```bash
# 上传图片
curl -X POST 'https://www.biyiex.top/api/res/upload' \
  -H 'Authorization: Bearer TOKEN' \
  -F 'imgurl=@./id_card.jpg'

# 得到 resseq 后，构造 URL 保存到数据库
# https://www.biyiex.top/api/res/preview/RES202603020001
```

**创建收款方式时上传二维码：**
```bash
# 上传二维码
curl -X POST 'https://www.biyiex.top/api/res/upload' \
  -H 'Authorization: Bearer TOKEN' \
  -F 'imgurl=@./qrcode.png'

# 得到 resseq 后，用完整 URL 创建收款方式
# wxUrl: "https://www.biyiex.top/api/res/preview/RES202603020001"
```

---

## 🚨 常见错误

| 错误 | 原因 | 解决 |
|------|------|------|
| 401 | token 无效或过期 | 重新登录获取 token |
| 不支持的文件类型 | 上传了不支持的图片格式 | 只上传 jpg, png, gif, bmp 等支持格式 |
| 参数错误 | 未提供 imgurl 参数 | 检查 form-data 中是否有 imgurl 字段 |
| 404 Not Found | resseq 不存在或已删除 | 确认 resseq 正确，重新上传图片 |
| 文件过大 | 上传的文件超过限制 | 压缩图片大小后重试 |

---

## 💡 最佳实践

1. **使用 jpg 或 png 格式**
   - jpg：照片和普通图片
   - png：需要透明背景的图片

2. **图片大小优化**
   - 推荐 < 5MB
   - 对于二维码：可以是较小的 PNG（< 1MB）

3. **错误处理**
   ```javascript
   // JavaScript 示例
   try {
     const formData = new FormData();
     formData.append('imgurl', fileInput.files[0]);
     
     const response = await fetch('/res/upload', {
       method: 'POST',
       headers: { 'Authorization': `Bearer ${token}` },
       body: formData
     });
     
     const result = await response.json();
     if (result.code === 0) {
       const resseq = result.data[0].resseq;
       const imageUrl = `/res/preview/${resseq}`;
       console.log('Image URL:', imageUrl);
     }
   } catch (error) {
     console.error('Upload failed:', error);
   }
   ```

4. **缓存策略**
   - 图片首次加载后会缓存 7 天
   - 同一图片重复访问无需重新下载
   - 如需强制刷新，使用 `?v=timestamp` 参数

---

## 📋 完整请求体示例

### 创建收款方式（使用上传的图片）
```json
{
  "collWay": "wx",
  "wxUrl": "https://www.biyiex.top/api/res/preview/RES202603020001",
  "wxAcc": "wechat_account",
  "wxName": "张三",
  "merchantCode": "MCH20260302001",
  "tag": "主账户"
}
```

---

**完整文档:** [API_DOCUMENTATION.md](./API_DOCUMENTATION.md)

