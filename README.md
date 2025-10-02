# Cloudflare Pages Universal Proxy

这是一个通过 `_worker.js` 实现的 Cloudflare Pages Function，旨在为您的静态 Pages 项目添加强大的动态 HTTP/HTTPS 代理能力。它能够解决 API 跨域问题，并对流经的资源请求进行实时处理。

[![平台](https://img.shields.io/badge/Platform-Cloudflare%20Pages-F38020.svg)](https://pages.cloudflare.com/)
[![语言](https://img.shields.io/badge/Language-JavaScript-blue.svg)](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript)
[![许可证](https://img.shields.io/badge/License-MIT-green.svg)](https://opensource.org/licenses/MIT)

## 核心功能

*   **无缝集成 Pages**: 以 `_worker.js` 文件的形式，为任何 Cloudflare Pages 项目提供全路由代理功能。
*   **CORS 解决方案**: 自动为上游响应添加必要的 `Access-Control-Allow-*` 头部，解决浏览器端的跨域请求限制。
*   **支持流式处理**: 请求体和响应体都以数据流的形式进行代理，无需在内存中缓冲完整内容。这使得代理大文件或实时数据流成为可能，显著降低了内存占用和首字节时间（TTFB）。
*   **通用请求代理**: 支持代理任意 HTTP/HTTPS 目标地址的资源请求，包括但不限于 API 接口、JSON 数据、图片、脚本等。
*   **请求头隔离**: 在向上游服务器转发请求时，会自动移除 Cloudflare 平台特有的 (`cf-*`) 以及客户端 IP (`x-real-ip`) 等请求头，增强隐私性与安全性。
*   **无缓存**: 默认强制禁用缓存，确保每次通过代理请求都能获取到源站的最新数据。
*   **移除安全限制头**: 自动移除上游响应中的 `Content-Security-Policy` 和 `X-Frame-Options` 头部，便于在不同应用场景下进行资源嵌入和集成。

## 部署至 Cloudflare Pages

部署此功能非常简单，您只需要将其集成到您的 Cloudflare Pages 项目中。

1.  **准备 Git 仓库**:
    确保您的项目由一个 Git 仓库（如 GitHub, GitLab）管理。

2.  **添加 `_worker.js` 文件**:
    将本项目中的 `_worker.js` 文件放置在您 Git 仓库的**根目录**下。

3.  **创建 Cloudflare Pages 项目**:
    *   登录至您的 [Cloudflare 仪表板](https://dash.cloudflare.com/)。
    *   在左侧导航栏中，选择 **Workers & Pages**。
    *   点击 **创建应用程序 (Create Application)**，然后选择 **Pages** 选项卡。
    *   点击 **连接到 Git (Connect to Git)** 并授权 Cloudflare 访问您的 Git 仓库。

4.  **配置并部署**:
    *   选择您存放了 `_worker.js` 文件的 Git 仓库。
    *   在 **设置构建和部署 (Set up builds and deployments)** 步骤中：
        *   **项目名称 (Project name)**: 为您的项目命名，这将决定您的默认域名。
        *   **生产分支 (Production branch)**: 选择您的主分支 (如 `main`)。
        *   **构建设置 (Build settings)**: 如果您的仓库中**只有**此 `_worker.js` 文件而没有其他静态资源需要构建，您可以将 **构建命令 (Build command)** 和 **构建输出目录 (Build output directory)** 留空。
    *   点击 **保存并部署 (Save and Deploy)**。

部署完成后，您的代理服务将通过 `https://<您的项目名称>.pages.dev` 地址提供。

## 使用说明

通过将目标资源 URL 附加到您的 Pages 项目地址之后，即可发起代理请求。

**请求格式:**

```
https://<您的项目名称>.pages.dev/<目标资源 URL>
```

---

#### **示例 1: 代理一个存在跨域限制的 API**

**目标 URL:**
`https://api.example.com/data?id=123`

**代理请求 URL (假设您的 Pages 项目地址为 `my-proxy-app.pages.dev`):**
`https://my-proxy-app.pages.dev/https://api.example.com/data?id=123`

---

#### **示例 2: 代理一个 JSON 资源文件**

**目标 URL:**
`https://jsonplaceholder.typicode.com/posts/example.json`

**代理请求 URL:**
`https://my-proxy-app.pages.dev/jsonplaceholder.typicode.com/posts/example.json`

---

#### **说明:**

*   访问根路径 (`/`) 时，会返回一个简单的 HTML 页面，您可根据需要自行修改。
*   如果目标 URL 不包含协议 (如 `http://` 或 `https://`)，脚本会默认添加 `http://` 前缀。为确保准确性，建议始终提供完整的 URL。

## 注意事项

*   **资源滥用**: 请确保您的使用场景符合相关法律法规以及目标服务器的服务条款。请勿将此服务用于恶意或非授权的请求。
*   **安全策略**: 脚本移除了部分安全相关的响应头，这可能会降低目标资源在特定场景下的安全级别。请在充分了解其影响后使用。
*   **Cloudflare Pages 限制**: 请注意您在 Cloudflare Pages Functions 的套餐的[使用限制](https://developers.cloudflare.com/pages/platform/limits/)，避免超出额度。

## 作者

*   Colime

## 许可证

本项目基于 [MIT License](https://opensource.org/licenses/MIT) 授权。
