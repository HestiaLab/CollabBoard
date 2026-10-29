# CollabBoard

[English](README.en.md) | 简体中文

**A collaborative whiteboard scaffold built with Vue 3 and ASP.NET Core on .NET Aspire.** · 协作白板工程脚手架（实验性）：Vue 3 + ASP.NET Core + .NET Aspire，业务功能尚未实现。

**定位**：想按一份 113 条需求规格从零实现协作白板的开发者，以及需要评估现有工程骨架的维护者；当前仓库是 .NET Aspire + Vue 3 前端 + ASP.NET Core 后端的模板脚手架，不是可直接部署的产品。
**状态**：实验性 / 未完成（工程脚手架）· 无 Release、无 tag、无 CI · 主干 HEAD `7f7955b`（2026-10-24）。`docs/requirements.md` 共 113 条业务需求，当前实现 0 条（1 条部分：日志级别配置）。

[![License](https://img.shields.io/github/license/HestiaLab/CollabBoard)](LICENSE)

[需求规格](docs/requirements.md) · [许可证](LICENSE) · [前端说明](src/collabboard.client/README.md) · [服务端变更记录](src/CollabBoard.Server/CHANGELOG.md)

暂无截图与在线 Demo。首屏素材采用真实构建终端记录、项目结构树与启动命令（见「快速开始」）；截图待业务功能落地后补充。

CollabBoard 是一个基于 .NET Aspire 编排的协作白板**脚手架**：AppHost 同时拉起 ASP.NET Core 后端与 Vue 3 + Vite 前端，后端预置 OpenTelemetry 与健康检查，前端通过 Vite 代理访问后端接口。
`docs/requirements.md` 以 113 条需求描述用户、白板、绘图、实时协作、历史、导出、系统支撑与安装部署 8 个模块，但这些业务模块尚未实现；当前可运行的是模板自带的天气接口样例。
本仓库适合作为「按需求规格从零实现」的起点，不适合作为可直接部署的协作白板。

> **技术栈说明**：当前代码只包含 Vue 3 + ASP.NET Core（.NET 10）+ .NET Aspire；需求文档规划的 Redis、MySQL、SignalR 尚未接入，仓库中没有数据库脚本、迁移、连接串或相关依赖。

## 已实现能力与未实现边界

| 能力 | 证据 | 状态 |
|---|---|---|
| .NET Aspire 编排后端与前端 | `src/CollabBoard.AppHost/AppHost.cs:3-8` | 已实现（代码） |
| 后端 `/weatherforecast` 样例接口 | `src/CollabBoard.Server/Controllers/WeatherForecastController.cs:14-24` | 已实现（模板样例；未启动验证） |
| 前端页面与接口调用样例 | `src/collabboard.client/src/App.vue:7-17`、`src/collabboard.client/src/components/HelloWorld.vue:74-83` | 已实现（模板页面；构建通过，未启动 dev server） |
| 健康检查 `/health`、`/alive` | `src/CollabBoard.ServiceDefaults/Extensions.cs:100-123` | 已实现（代码；未启动验证） |
| OpenTelemetry 指标/链路/日志 | `src/CollabBoard.ServiceDefaults/Extensions.cs:47-78` | 已实现（代码；未启动验证） |
| 用户注册/登录/JWT/Redis 会话（MOD-01） | `docs/requirements.md` 需求 | 未实现 |
| 白板/邀请码/owner-editor-viewer 权限（MOD-02） | `docs/requirements.md` 需求 | 未实现 |
| 绘图工具与画布（MOD-03） | `docs/requirements.md` 需求 | 未实现 |
| SignalR 实时协作（MOD-04） | `docs/requirements.md` 需求 | 未实现 |
| 撤销重做/历史回放（MOD-05） | `docs/requirements.md` 需求 | 未实现 |
| PNG/SVG/PDF 导出（MOD-06） | `docs/requirements.md` 需求 | 未实现 |
| 日志/缓存/任务/发布订阅（MOD-07） | 仅 `src/CollabBoard.Server/appsettings.json:2-7` 的日志级别配置 | 部分（1 条） |
| MySQL 表结构与迁移 | 全仓无 SQL、迁移或 `docker-compose` 文件 | 未实现 |
| WPF 安装向导与打包（MOD-08） | `docs/requirements.md` 需求 | 未实现 |
| CI / Release / 测试 | 仅 `.github/workflows/add-to-project.yml`；无 CI 工作流、无 tag、无测试项目 | 未实现 |

## 快速开始

前置：.NET SDK 10（`net10.0`，见 `src/CollabBoard.Server/CollabBoard.Server.csproj:4`）；Node.js `^20.19.0` 或 `>=22.12.0`（`src/collabboard.client/package.json:36-38`）。

已验证（本机 Windows，.NET 10.0.401，Node 24.18.0）：

```console
$ dotnet build src/CollabBoard.slnx --no-restore
37 个警告
0 个错误

$ cd src/collabboard.client
$ npm install
up to date, audited 260 packages in 2s
16 vulnerabilities (2 low, 3 moderate, 10 high, 1 critical)

$ npm run build
✓ 28 modules transformed.
dist/assets/index-B7GJRQ39.js   74.07 kB │ gzip: 28.93 kB
✓ built in 562ms
```

未验证（本轮未启动服务，也未运行任何数据库/缓存）：

```sh
npm run dev                                     # https://localhost:27297
dotnet run --project src/CollabBoard.Server     # http://localhost:5166 / https://localhost:7294
dotnet run --project src/CollabBoard.AppHost    # Dashboard http://localhost:15222
```

说明：
- 构建存在依赖审计警告（37 条，含 high/critical）与 npm 16 个漏洞；本轮仅文档改动，未升级依赖。
- `src/CollabBoard.AppHost/AppHost.cs:6` 注入 `PORT`，而 `src/collabboard.client/vite.config.ts:54` 读取 `DEV_SERVER_PORT`；Aspire 下前端端口可能不按预期生效（未验证）。
- 当前版本没有数据库或 Redis 依赖，因此不需要先启动 MySQL/Redis；后续实现 MOD-01/02/04 时才需要。

## 项目结构

```text
CollabBoard/
├─ docs/requirements.md                    # 113 条需求规格（中文）
├─ src/
│  ├─ CollabBoard.AppHost/                 # .NET Aspire 编排入口
│  ├─ CollabBoard.Server/                  # ASP.NET Core API（仅天气样例）
│  ├─ CollabBoard.ServiceDefaults/         # OpenTelemetry 与健康检查
│  ├─ collabboard.client/                  # Vue 3 + Vite 前端（模板页面）
│  └─ CollabBoard.slnx
├─ .github/workflows/add-to-project.yml    # 仅项目看板自动化，非 CI
├─ LICENSE                                 # Apache-2.0
├─ README.md
└─ README.en.md
```

## 文档

- [需求规格](docs/requirements.md)：8 个模块、113 条业务需求（目标状态）。
- [前端说明](src/collabboard.client/README.md)：Vue 3 + Vite 前端模板说明。
- [服务端变更记录](src/CollabBoard.Server/CHANGELOG.md)：服务端模板变更记录。

## 范围说明

`docs/requirements.md` 描述的是目标产品，其中包含 WPF 安装向导（MOD-08）、C 语言算法 DLL（MOD-03）等与当前 Web 技术栈不完全同源的内容。实现前需由产品侧确认范围；本 README 只描述当前代码中可验证的部分。

## 许可证

CollabBoard 以 [Apache-2.0 许可证](LICENSE) 发布。
