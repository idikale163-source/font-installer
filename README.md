# 🎨 字体站一键部署安装器

> 填 3 个 Token，自动搭一个**完全属于你自己**的私有字体库网站。
> 支持上传、搜索、预览、下载字体，自带"转盘抽字体"。
> 资产全私有，站点可绑自己域名，**国内免梯子直连**。

---

## ✨ 它能做什么

打开一个网页 → 填入你的 GitHub / Vercel Token → 点一下按钮，自动完成：

| 步骤 | 自动执行 |
|---|---|
| 1 | 在你的 GitHub 建一个**私有仓库** |
| 2 | 推送完整的前端 + Serverless 后端代码 |
| 3 | 在 Vercel 创建项目并关联该仓库 |
| 4 | 配置 4 个环境变量（Token / 仓库名 / 分支） |
| 5 | 触发生产部署并等待构建完成 |
| 6 | 绑定你的自定义子域名 |

**全程 2-3 分钟。** 唯一需要手动的一步是去 Cloudflare 加一条 CNAME 记录（页面会给你现成的参数，复制粘贴即可）。

---

## 🏗️ 它部署出来的是什么

```
你的私有 GitHub 仓库
├── index.html        ← 主站（搜索 / 预览 / 上传 / 转盘抽卡）
├── share.html        ← 分享页（朋友免登录浏览）
├── api/
│   ├── font.js       ← 字体直链代理（Serverless）
│   └── list.js       ← 字体列表接口（Serverless）
└── vercel.json       ← 部署配置
```

**核心特性：**

- 🔒 **仓库私有** —— 但网页能正常读取（走后端代理，不暴露仓库）
- 🎲 **转盘抽字体** —— Canvas 手绘，莫兰迪色系，抽中即预览
- 📤 **网页上传** —— 拖拽字体文件，直传你的仓库
- 🌏 **免梯子** —— 配合 Cloudflare 橙云代理，`*.vercel.app` 的 SNI 阻断被绕过
- 📱 **PWA 支持** —— 可安装到手机桌面

---

## 🚀 使用方法

### 你需要准备

| 项目 | 说明 |
|---|---|
| **GitHub Token** | 需勾选 `repo` 权限（[生成地址](https://github.com/settings/tokens)） |
| **Vercel Token** | 在 [账号设置](https://vercel.com/account/tokens) 创建 |
| **一个域名** | 免费域名也行（如 `.cc.cd` / `.eu.org`），需能改 NS |
| **Cloudflare 账号** | 免费版即可，用来托管 DNS + 开橙云代理 |

### 操作流程

1. **打开安装器网页**（你部署好的那个地址）
2. 依次填入 GitHub Token、仓库名、Vercel Token、域名
3. 点「🚀 开始一键部署」
4. 等待 6 步自动完成
5. **按页面提示，去 Cloudflare 加一条 CNAME 记录**（参数都已生成好）
6. 完成！打开 `https://你的子域名` 即可

---

## 🖥️ 自托管这个安装器

```bash
git clone https://github.com/idikale163-source/font-installer.git
cd font-installer
# 直接用浏览器打开 index.html 也行
# 或部署到任意静态托管（Vercel / Cloudflare Pages / GitHub Pages）
```

**它是一个单文件 HTML，零依赖，无构建步骤。**

---

## 🔐 安全说明

- **你的 Token 不会被上传到任何服务器。** 所有 API 请求都在**你的浏览器**里直接发往 GitHub / Vercel 官方接口。
- Token 仅保存在**当前页面内存**中，刷新即清空。
- 部署生成的仓库是**私有的**，只有你自己能访问。
- 源码中**不含任何 Token、账号名或域名**。

---

## ⚠️ 注意事项

| 项目 | 说明 |
|---|---|
| **Vercel 免费额度** | 每天 100 次部署。反复调试时注意别耗尽 |
| **Cloudflare API 不支持跨域** | 因此 DNS 记录需**手动添加**（浏览器无法直接调用 CF API） |
| **GitHub Token 权限** | 若需删除仓库，还需 `delete_repo` 权限（部署不需要） |
| **空仓库限制** | 安装器已处理：先用 Contents API 创建初始提交，再用 Git Trees API 批量推送 |

---

## 🛠️ 技术实现

**安装器本体：**
- 纯前端单文件 HTML（无框架、无依赖）
- 模板代码以 **Base64 编码**内嵌于 `<script id="tpl-data">` 标签
  （避免模板中的 `</script>` 提前闭合页面）

**推代码机制（两阶段）：**
1. `PUT /repos/{owner}/{repo}/contents/README.md` —— 创建初始 commit（空仓库必需）
2. `POST /repos/{owner}/{repo}/git/blobs` ×5 → `git/trees` → `git/commits` → `PATCH git/refs`
   —— 一次性原子提交其余文件

**为什么不用 6 次 PUT？**
GitHub 的 Contents API 逐个写文件时容易触发限流，且失败难以定位。
Git Trees API 是原子操作，一次提交全部文件，更快也更可靠。

---

## 📄 License

[MIT License](LICENSE) © 2026 温语初

随便用，随便改，随便发，保留版权声明即可。

---
*Made with ♥ by Sully*