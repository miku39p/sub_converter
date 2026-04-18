# Subconverter Worker

基于 Cloudflare Worker 的无服务器订阅转换与代理分发工具，内置现代化的美观前端界面与短链接生成功能。

## ✨ 特性

- **现代化UI**：内置极简、玻璃拟物化风格的前端界面，支持各种设备。
- **无服务器架构**：依托 Cloudflare Worker，无需自建服务器，零维护成本。
- **真实后端隐藏**：前端直接与 Worker 交互，Worker 作为反向代理保护真实的 subconverter 后端 IP 及域名。
- **内置短链服务**：使用 Cloudflare KV/R2 存储直接在生成订阅时提供短链接转换功能。
- **丰富的远程配置**：前端预置了数十种各大机场、规则作者的常用远程配置与策略组方案。

## 🚀 部署指南

### 1. 准备工作

* 一个 Cloudflare 账号。
* (可选) 一个自定义域名。
* 一个运行中的 subconverter 后端 (如 `https://sub.example.com` 或者使用现有的公开后端)。

### 2. Cloudflare Worker 配置

1. 登录 Cloudflare 控制台，进入 **Workers & Pages**。
2. 点击 **Create application** -> **Create Worker**，随便取个名字，点击 Deploy。
3. 点击 **Edit code**，将仓库中 `worker.js` 的所有代码复制并粘贴进去。

### 3. 配置环境变量与 KV 存储

在 Worker 的 **Settings** -> **Variables** 选项卡中，配置以下内容：

#### 环境变量 (Environment Variables)

* 添加变量 `BACKEND`
  * **值**：填写你真实的 subconverter 后端地址（例如：`https://sub.39projects.com`）。Worker 会将所有转换请求转发到这个地址。

#### KV 命名空间绑定 (KV Namespace Bindings)

因为工具自带了短链接功能和链接预处理，需要绑定存储：
1. 返回 Cloudflare 首页，进入 **Workers & Pages** -> **KV**。
2. 创建一个命名空间（例如命名为 `sub_kv`）。
3. 回到你的 Worker 的设置，在 **KV Namespace Bindings** 中添加绑定：
   * **Variable name**: `SUB_BUCKET` (必须严格是这个名字)
   * **KV namespace**: 选择你刚才创建的 `sub_kv`。

### 4. 发布与使用

* 保存并部署你的 Worker。
* 访问你的 Worker 地址（如 `https://your-worker.workers.dev`），即可看到美观的前端界面。
* 在前端界面中输入你的订阅链接，选择转换类型，即可生成对应的订阅链接和短链接。

## 💡 前后端分离机制

前端 HTML 已经硬编码到了 `worker.js` 中动态获取（默认从 GitHub 获取你的 `frontend.html`）。

前端在发起转换和短链请求时，会自动请求当前的 `window.location.origin`，而 `worker.js` 会拦截这些请求，处理短链逻辑或将订阅转换请求透明代理到你的 `BACKEND` 服务器。这确保了跨域问题不存在，同时也隐藏了真实的转换服务器。

## 📝 证书与许可

本项目部分代码参考/修改自不良林的 psub 项目以及其他开源项目，请遵循原作者许可。
