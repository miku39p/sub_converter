# Subconverter Worker

基于 Cloudflare Worker 的无服务器订阅转换与代理分发工具，内置现代化的美观前端界面与短链接生成功能。

## ✨ 特性

- **现代化UI**：内置极简、玻璃拟物化风格的前端界面，支持各种设备。
- **无服务器架构**：依托 Cloudflare Worker，无需自建服务器，零维护成本。
- **真实后端隐藏**：前端直接与 Worker 交互，Worker 作为反向代理保护真实的 subconverter 后端 IP 及域名。
- **内置短链服务**：使用 Cloudflare KV/R2 存储直接在生成订阅时提供短链接转换功能。
- **丰富的远程配置**：前端预置了数十种各大机场、规则作者的常用远程配置与策略组方案。

## 🚀 部署指南 (推荐使用 GitHub 自动部署)

本项目已经配置了完善的部署体系，推荐直接在 Cloudflare 后台关联本 GitHub 仓库，实现 `git push` 全自动部署。

### 1. 准备 KV 存储空间
因为工具自带了短链接功能和节点隐私脱敏功能，必须要有一个 KV 存储空间：
1. 登录 Cloudflare 控制台，进入 **Workers & Pages** -> **KV**。
2. 创建一个命名空间（例如命名为 `sub_kv`）。**记下它的名称，稍后要用。**

### 2. 关联 GitHub 自动部署
1. 进入 **Workers & Pages**，点击 **Create application** -> **Create Worker**，随便取个名字，点击 Deploy。
2. 进入刚才建好的 Worker，点击顶部的 **Settings (设置)** -> **Integrations (集成)**。
3. 找到 **GitHub** 选项，点击 Connect，授权并选择你自己的这个代码仓库。
4. **最重要的一步**：在 Worker 的 **Settings** -> **Variables** 里，手动添加以下配置：
   - **环境变量 (Environment Variables)**: 添加变量 `BACKEND`，值为你真实的转换后端（例如：`https://api.wcc.best`）。
   - **KV 命名空间绑定 (KV Namespace Bindings)**: 添加绑定，Variable name 必须填 `SUB_BUCKET`，KV namespace 选择你刚才创建的 `sub_kv`。
5. 配置好以后，以后只要你在本地修改代码并 `git push` 到 GitHub，Cloudflare 就会自动重新部署！

## 💻 本地开发与测试

如果你想在本地修改前端界面或调试 Worker 逻辑，可以使用官方提供的 Wrangler 工具。
本项目针对本地测试专门隔离了 `dev` 环境，绝不会影响线上。本地会使用虚拟的内存 KV，且不消耗线上额度。

### 本地测试步骤
1. 确保电脑已安装 [Node.js](https://nodejs.org/)。
2. 在项目根目录，打开终端，运行本地调试命令：
   ```bash
   npx wrangler dev -e dev
   ```
3. 终端会提示启动了一个本地服务器（通常是 `http://localhost:8787`）。
4. 在浏览器打开该地址，你就能看到完整的前端界面了。
5. 当你生成订阅链接并在浏览器访问新链接时，**所有的假节点脱敏过程、请求真实后端的全记录日志，都会直接打印在你的终端里**，调试极为方便。

## 💡 前后端分离机制

前端 HTML 已经硬编码到了 `worker.js` 中动态获取（默认从 GitHub 获取你的 `frontend.html`）。

前端在发起转换和短链请求时，会自动请求当前的 `window.location.origin`，而 `worker.js` 会拦截这些请求，处理短链逻辑或将订阅转换请求透明代理到你的 `BACKEND` 服务器。这确保了跨域问题不存在，同时也隐藏了真实的转换服务器。

## 📝 证书与许可

本项目部分代码参考/修改自不良林的 psub 项目以及其他开源项目，请遵循原作者许可。
