# C2C API 文档文件索引

**生成日期：** 2026年3月2日  
**基础 URL：** `https://m.biyiex.top/api`

---

## 📚 文件清单

### 1. 完整 API 文档

#### 📄 C2C_API_DOCUMENT.md
- **格式：** Markdown
- **大小：** 完整详细文档
- **用途：** 
  - 后端开发参考
  - 版本控制和文档更新
  - 集成到 GitLab/GitHub Wiki
  - 作为标准参考文档
- **特点：**
  - 分章节清晰，目录支持跳转
  - 包含所有接口的详细参数说明
  - 包含错误处理和字段验证规则
  - 易于编辑和更新

**推荐打开方式：**
```bash
# VS Code
code C2C_API_DOCUMENT.md

# 或用任何 Markdown 编辑器打开
```

---

#### 🌐 C2C_API_DOCUMENT.html
- **格式：** HTML（单文件，开箱即用）
- **大小：** 约 50KB
- **用途：**
  - 前端团队查看
  - Web 浏览器查看
  - 分享给客户
  - 内网文档展示
- **特点：**
  - 左侧导航栏，支持快速跳转
  - 响应式设计，支持手机/平板/PC
  - 美观的代码高亮和样式
  - 搜索友好（方便浏览器 Ctrl+F 搜索）
  - 无需服务器，直接双击打开
  - 回到顶部快捷按钮

**推荐打开方式：**
```bash
# Windows
双击打开 C2C_API_DOCUMENT.html

# Mac/Linux
open C2C_API_DOCUMENT.html
# 或用任何浏览器打开
```

---

### 2. 快速参考

#### ⚡ C2C_API_QUICK_REFERENCE.md
- **格式：** Markdown
- **大小：** 简洁紧凑
- **用途：**
  - 快速查阅最常用的 3 个接口
  - 快速查看接口索引表
  - 查看常见错误及解决方案
  - 打印版参考卡片
- **特点：**
  - 3 个最常用的 curl 示例
  - 完整接口快速索引表（一页纸）
  - 常见参数值速查
  - 常见错误速查

**推荐打开方式：**
```bash
# 作为快速参考卡片保存/打印
# 或放在项目 README 中
```

---

### 3. Postman 导入文件

#### 📮 postman_collection.json
- **格式：** JSON（Postman Collection 格式）
- **大小：** 约 10KB
- **用途：**
  - Postman 中导入并测试
  - 包含环境变量配置
  - 支持快速切换 baseUrl 和 token
- **特点：**
  - 包含登录接口（获取 token）
  - 包含所有 9 个 C2C 接口
  - 预配置的请求头和参数
  - 支持环境变量（baseUrl, token）

**导入步骤：**
1. 打开 Postman
2. 点击左上角 **Import** 按钮
3. 选择 **Upload Files** → 选择此文件
4. 点击 **Import** 完成
5. 左侧会出现 "Exchange API Collection"
6. 配置 Environment 变量（baseUrl, token）
7. 选择需要测试的接口，点击 **Send**

---

#### 📝 postman_curl_commands.sh
- **格式：** Shell 脚本（包含所有 curl 命令）
- **大小：** 约 5KB
- **用途：**
  - 在 Postman 中逐个导入 curl 命令
  - 在终端/PowerShell 中直接执行
  - 作为 curl 命令参考
- **特点：**
  - 包含所有接口的 curl 命令
  - 包含详细的导入说明注释
  - 易于复制粘贴

**使用步骤：**
```bash
# 在 Postman 中导入单个 curl
1. Postman → Import → Raw text
2. 复制 curl 命令粘贴
3. 点击 Import

# 或在终端中执行
bash postman_curl_commands.sh
```

---

### 4. 配置和指南

#### 🔗 postman_login_collway_guide.md
- **格式：** Markdown 指南文档
- **用途：** Postman 详细使用指南
- **特点：**
  - 环境变量配置说明
  - 3 种导入方法详解
  - 分步骤使用说明
  - 常见问题解答

---

## 🎯 根据角色选择文档

### 👨‍💻 后端开发者
```
推荐阅读顺序：
1. C2C_API_DOCUMENT.md（完整详细）
2. C2C_API_QUICK_REFERENCE.md（快速查阅）
```

### 👨‍🎨 前端开发者
```
推荐阅读顺序：
1. C2C_API_DOCUMENT.html（打开浏览器查看）
2. C2C_API_QUICK_REFERENCE.md（快速查阅）
3. postman_collection.json（在 Postman 中测试）
```

### 🧪 测试工程师
```
推荐使用工具和文档：
1. postman_collection.json（在 Postman 中导入）
2. C2C_API_QUICK_REFERENCE.md（查看检查清单）
3. postman_curl_commands.sh（命令行测试）
```

### 📊 产品经理/技术主管
```
推荐阅读：
1. C2C_API_DOCUMENT.html（易读易分享）
2. C2C_API_QUICK_REFERENCE.md（概览）
```

### 🚀 部署/运维
```
推荐：
1. C2C_API_QUICK_REFERENCE.md（快速了解接口）
2. 文档中的错误码说明部分
```

---

## 📊 接口覆盖范围

| 功能模块 | 接口数 | 是否已文档化 |
|---------|--------|-----------|
| C2C 商户管理 | 6 | ✅ 全部 |
| C2C 收款方式 | 3 | ✅ 全部 |
| **总计** | **9** | **✅** |

每个接口都包含：
- ✅ 请求方法和 URL
- ✅ 完整的参数说明表
- ✅ curl 示例
- ✅ JSON 响应示例
- ✅ 错误处理说明
- ✅ 字段验证规则

---

## 🔄 文档更新流程

当后端修改接口时：

1. **更新 Markdown 文档**
   ```bash
   编辑 C2C_API_DOCUMENT.md
   ```

2. **重新生成 HTML**（可选，如果大幅改动）
   ```bash
   # 可以使用 markdown-to-html 工具转换
   # 或手动编辑 HTML
   ```

3. **更新快速参考**
   ```bash
   编辑 C2C_API_QUICK_REFERENCE.md
   ```

4. **更新 Postman Collection**
   ```bash
   在 Postman 中修改，然后导出覆盖 postman_collection.json
   ```

---

## 🎁 文件分享方案

### 方案 A：内网 Wiki（推荐）
1. 将 C2C_API_DOCUMENT.html 上传到内网服务器
2. 所有人用浏览器访问链接
3. 或将 Markdown 内容复制到 Confluence/GitLab Wiki

### 方案 B：Git 仓库
1. 提交 C2C_API_DOCUMENT.md 到项目仓库
2. 所有人通过 Git 克隆获取最新版本
3. 易于版本控制和追踪更新

### 方案 C：邮件分发
1. 直接发送 C2C_API_DOCUMENT.html 文件
2. 收件人双击打开即可查看

### 方案 D：Postman 工作区
1. 在 Postman 团队工作区中导入 postman_collection.json
2. 所有团队成员可以访问和协作

---

## ⚙️ 扩展和定制

### 如果需要修改：

1. **修改 API 地址（基础 URL）**
   - Markdown 文档：编辑开头的 `https://m.biyiex.top/api`
   - HTML 文档：编辑 `<p>基础 URL: https://m.biyiex.top/api</p>`
   - Postman Collection：编辑 `baseUrl` 变量值

2. **添加新接口**
   - 复制已有接口的 Markdown 格式
   - 按照相同的格式填写新接口信息
   - 更新目录索引

3. **修改样式（HTML 版本）**
   - 编辑 `<style>` 段落中的 CSS
   - 修改颜色、字体、布局等

---

## 📞 文档维护

| 责任 | 人员 | 频率 |
|------|------|------|
| 接口变更同步 | 后端开发 | 每次发布时 |
| 内容审核 | 技术主管 | 月度审核 |
| 格式优化 | 文档编辑 | 按需 |

---

## 🎯 快速导航

- **完整详细文档** → `C2C_API_DOCUMENT.md`
- **网页版文档** → `C2C_API_DOCUMENT.html` ⭐ 推荐
- **快速查阅** → `C2C_API_QUICK_REFERENCE.md`
- **Postman 测试** → `postman_collection.json`
- **命令行测试** → `postman_curl_commands.sh`

---

**✅ 所有文档已准备就绪，可发送给团队使用！**

