# CollabBoard

English | [简体中文](README.md)

**A collaborative whiteboard scaffold built with Vue 3 and ASP.NET Core on .NET Aspire.** · An experimental scaffold: Vue 3 + ASP.NET Core + .NET Aspire, with no business features implemented yet.

**Positioning**: Developers who want to implement a collaborative whiteboard from a 113-requirement specification, and maintainers evaluating the existing scaffold. This repository is a .NET Aspire + Vue 3 + ASP.NET Core template scaffold, not a deployable product.
**Status**: Experimental / unfinished (engineering scaffold) · no Release, no tag, no CI · main HEAD `7f7955b` (2026-10-24). `docs/requirements.md` contains 113 business requirements; 0 are implemented (1 partial: logging-level configuration).

[![License](https://img.shields.io/github/license/HestiaLab/CollabBoard)](LICENSE)

[Requirements](docs/requirements.md) · [License](LICENSE) · [Frontend notes](src/collabboard.client/README.md) · [Server changelog](src/CollabBoard.Server/CHANGELOG.md)

There are no screenshots or live demo. The first-screen material is a real build terminal record, the project tree, and startup commands (see Quick start); screenshots will be added after the business features land.

CollabBoard is a **scaffold** for a collaborative whiteboard orchestrated by .NET Aspire: the AppHost starts the ASP.NET Core backend and the Vue 3 + Vite frontend together. The backend includes OpenTelemetry and health checks, and the frontend reaches the API through the Vite proxy.
`docs/requirements.md` describes eight modules (users, whiteboards, drawing, real-time collaboration, history, export, system support, installation) in 113 requirements, but none of these business modules are implemented; the runnable part is the template weather endpoint.
This repository is a starting point for implementing the specification, not a whiteboard you can deploy as-is.

> **Tech stack note**: the current code contains only Vue 3 + ASP.NET Core (.NET 10) + .NET Aspire. The Redis, MySQL, and SignalR dependencies planned in the requirements are not wired up; there are no database scripts, migrations, connection strings, or related packages in the repository.

## Implemented vs. not implemented

| Capability | Evidence | Status |
|---|---|---|
| .NET Aspire orchestration of backend and frontend | `src/CollabBoard.AppHost/AppHost.cs:3-8` | Implemented (code) |
| Backend `/weatherforecast` sample endpoint | `src/CollabBoard.Server/Controllers/WeatherForecastController.cs:14-24` | Implemented (template sample; not started) |
| Frontend page and API call sample | `src/collabboard.client/src/App.vue:7-17`, `src/collabboard.client/src/components/HelloWorld.vue:74-83` | Implemented (template pages; build passes, dev server not started) |
| Health checks `/health`, `/alive` | `src/CollabBoard.ServiceDefaults/Extensions.cs:100-123` | Implemented (code; not started) |
| OpenTelemetry metrics/traces/logs | `src/CollabBoard.ServiceDefaults/Extensions.cs:47-78` | Implemented (code; not started) |
| Registration/login/JWT/Redis sessions (MOD-01) | `docs/requirements.md` | Not implemented |
| Whiteboards/invite codes/owner-editor-viewer permissions (MOD-02) | `docs/requirements.md` | Not implemented |
| Drawing tools and canvas (MOD-03) | `docs/requirements.md` | Not implemented |
| SignalR real-time collaboration (MOD-04) | `docs/requirements.md` | Not implemented |
| Undo/redo and history playback (MOD-05) | `docs/requirements.md` | Not implemented |
| PNG/SVG/PDF export (MOD-06) | `docs/requirements.md` | Not implemented |
| Logging/cache/jobs/pub-sub (MOD-07) | Only the log levels in `src/CollabBoard.Server/appsettings.json:2-7` | Partial (1 item) |
| MySQL schema and migrations | No SQL, migration, or `docker-compose` files | Not implemented |
| WPF installer and packaging (MOD-08) | `docs/requirements.md` | Not implemented |
| CI / Release / tests | Only `.github/workflows/add-to-project.yml`; no CI workflow, no tag, no test project | Not implemented |

## Quick start

Prerequisites: .NET SDK 10 (`net10.0`, see `src/CollabBoard.Server/CollabBoard.Server.csproj:4`); Node.js `^20.19.0` or `>=22.12.0` (see `src/collabboard.client/package.json:36-38`).

Verified (Windows, .NET 10.0.401, Node 24.18.0):

```console
$ dotnet build src/CollabBoard.slnx --no-restore
37 warnings
0 errors

$ cd src/collabboard.client
$ npm install
up to date, audited 260 packages in 2s
16 vulnerabilities (2 low, 3 moderate, 10 high, 1 critical)

$ npm run build
✓ 28 modules transformed.
dist/assets/index-B7GJRQ39.js   74.07 kB │ gzip: 28.93 kB
✓ built in 562ms
```

Not verified (no services or databases were started in this round):

```sh
npm run dev                                     # https://localhost:27297
dotnet run --project src/CollabBoard.Server     # http://localhost:5166 / https://localhost:7294
dotnet run --project src/CollabBoard.AppHost    # Dashboard http://localhost:15222
```

Notes:
- The build reports dependency audit warnings (37, including high/critical) and npm reports 16 vulnerabilities. This round is documentation-only; dependencies were not upgraded.
- `src/CollabBoard.AppHost/AppHost.cs:6` injects `PORT`, while `src/collabboard.client/vite.config.ts:54` reads `DEV_SERVER_PORT`; the frontend port may not behave as expected under Aspire (not verified).
- The current version has no database or Redis dependency, so MySQL/Redis do not need to be started yet; they will be needed when MOD-01/02/04 are implemented.

## Project structure

```text
CollabBoard/
├─ docs/requirements.md                    # 113 requirements (Chinese)
├─ src/
│  ├─ CollabBoard.AppHost/                 # .NET Aspire orchestration entry
│  ├─ CollabBoard.Server/                  # ASP.NET Core API (weather sample only)
│  ├─ CollabBoard.ServiceDefaults/         # OpenTelemetry and health checks
│  ├─ collabboard.client/                  # Vue 3 + Vite frontend (template pages)
│  └─ CollabBoard.slnx
├─ .github/workflows/add-to-project.yml    # project-board automation only, not CI
├─ LICENSE                                 # Apache-2.0
├─ README.md
└─ README.en.md
```

## Documentation

- [Requirements](docs/requirements.md): eight modules and 113 business requirements (target state).
- [Frontend notes](src/collabboard.client/README.md): Vue 3 + Vite frontend template notes.
- [Server changelog](src/CollabBoard.Server/CHANGELOG.md): server template change history.

## Scope note

`docs/requirements.md` describes the target product, including a WPF installer (MOD-08) and a C algorithm DLL (MOD-03) that do not fully match the current web stack. The product side must confirm the scope before implementation; this README describes only what the current code verifiably contains.

## License

CollabBoard is released under the [Apache-2.0 License](LICENSE).
