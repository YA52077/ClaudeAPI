# ClaudeAPI

<div align="center">

![Go](https://img.shields.io/badge/Go-1.26+-00ADD8?style=flat-square&logo=go)
![Vue](https://img.shields.io/badge/Vue-3.x-42b883?style=flat-square&logo=vue.js)
![Vite](https://img.shields.io/badge/Vite-5.x-646CFF?style=flat-square&logo=vite)
![TailwindCSS](https://img.shields.io/badge/TailwindCSS-3.x-38BDF8?style=flat-square&logo=tailwindcss)
![PostgreSQL](https://img.shields.io/badge/PostgreSQL-15+-336791?style=flat-square&logo=postgresql)
![Redis](https://img.shields.io/badge/Redis-7+-DC382D?style=flat-square&logo=redis)
![Docker](https://img.shields.io/badge/Docker-Compose-2496ED?style=flat-square&logo=docker)

**一个统一的大模型 API Gateway 与管理平台**

基于 **Sub2API** 深度定制，提供统一接入、账号调度、API Key 分发、用量统计、后台管理与运营能力。

</div>

---

## 项目简介

ClaudeAPI 是一个面向开发者、团队和企业场景的 AI API 中转与管理平台。  
通过统一接口接入多种上游 AI 能力，并提供完整的后台管理、账号池调度、配额控制、计费统计和运营扩展能力。

本项目在原始 Sub2API 的基础上进行了品牌化与前端体验定制，当前以 **ClaudeAPI** 作为统一对外展示名称，适合自有品牌部署、团队内部使用或运营型 AI 接入平台搭建。

---

## 功能亮点

- **统一模型接入**
  - 一个入口统一调用多种 AI 模型能力
- **多账号池管理**
  - 多上游账号统一管理、分组、调度
- **API Key 分发**
  - 面向用户分配独立访问密钥
- **用量统计与计费**
  - 支持请求数、Token、缓存等多维度统计
- **后台管理系统**
  - 用户、账号、分组、渠道、订阅、系统设置一体化管理
- **支付与运营能力**
  - 支持订单、充值、兑换码、优惠码等扩展场景
- **品牌化前端定制**
  - 已完成 ClaudeAPI 首页、登录页、后台菜单和移动端体验优化
- **支持私有部署**
  - 支持 Docker、源码构建、宝塔面板等部署方式

---

## 适用场景

ClaudeAPI 适用于：

- 团队统一接入 AI 能力
- 自建 AI API 中转平台
- 多账号模型聚合与稳定调用
- 面向内部成员或客户分发 API Key
- 需要后台管理、配额控制、计费统计的 AI 平台场景

---

## 技术栈

### 前端
- Vue 3
- Vite
- Pinia
- Vue Router
- Tailwind CSS

### 后端
- Go
- Gin
- Ent
- Viper

### 基础依赖
- PostgreSQL
- Redis
- Docker / Docker Compose

---

## 项目截图

<div align="center">

![首页展示](img/ScreenShot_2026-07-01_144513_757.png)

![后台管理界面](img/ScreenShot_2026-07-01_144535_860.png)

![移动端界面展示](img/ScreenShot_2026-07-01_144552_620.png)

</div>

---

## 当前定制内容

当前仓库版本已对前端进行品牌与交互层定制，包括：

- 统一品牌名称为 **ClaudeAPI**
- 首页视觉与移动端展示优化
- 登录页与认证页样式优化
- 后台顶部菜单与用户菜单增强
- 自定义字体接入
- 支持信息与售后展示增强
- 多处文案与交互细节优化

适合用于：
- 自有品牌 AI 平台
- 商业化展示场景
- 对外部署与团队协作使用

---

## 快速开始

### Docker Compose（推荐）

```bash
cd deploy
cp .env.example .env
docker compose -f docker-compose.local.yml up -d
```

访问：

```text
http://YOUR_SERVER_IP:8080
```

---

### 源码构建

#### 前端构建
```bash
cd frontend
pnpm install
pnpm build
```

#### 后端构建
```bash
cd backend
VERSION="$(./scripts/resolve-version.sh)"
go build -tags embed -ldflags="-X main.Version=${VERSION}" -o sub2api ./cmd/server
```

> **Note:** The `-tags embed` flag embeds the frontend into the binary. Without this flag, the binary will not serve the frontend UI.

#### 配置文件
```bash
cp ../deploy/config.example.yaml ./config.yaml
nano config.yaml
```

**Key configuration in `config.yaml`:**

```yaml
server:
  host: "0.0.0.0"
  port: 8080
  mode: "release"

database:
  host: "localhost"
  port: 5432
  user: "postgres"
  password: "your_password"
  dbname: "sub2api"

redis:
  host: "localhost"
  port: 6379
  password: ""

jwt:
  secret: "change-this-to-a-secure-random-string"
  expire_hour: 24

default:
  user_concurrency: 5
  user_balance: 0
  api_key_prefix: "sk-"
  rate_multiplier: 1.0
```

### Sora Status (Temporarily Unavailable)

> ⚠️ Sora-related features are temporarily unavailable due to technical issues in upstream integration and media delivery.
> Please do not rely on Sora in production at this time.
> Existing `gateway.sora_*` configuration keys are reserved and may not take effect until these issues are resolved.

Additional security-related options are available in `config.yaml`:

- `cors.allowed_origins` for CORS allowlist
- `security.url_allowlist` for upstream/pricing/CRS host allowlists
- `security.url_allowlist.enabled` to disable URL validation (use with caution)
- `security.url_allowlist.allow_insecure_http` to allow HTTP URLs when validation is disabled
- `security.url_allowlist.allow_private_hosts` to allow private/local IP addresses
- `security.response_headers.enabled` to enable configurable response header filtering (disabled uses default allowlist)
- `security.csp` to control Content-Security-Policy headers
- `billing.circuit_breaker` to fail closed on billing errors
- `server.trusted_proxies` to enable X-Forwarded-For parsing
- `turnstile.required` to require Turnstile in release mode

**⚠️ Security Warning: HTTP URL Configuration**

When `security.url_allowlist.enabled=false`, the system performs minimal URL validation by default, **rejecting HTTP URLs** and only allowing HTTPS. To allow HTTP URLs (e.g., for development or internal testing), you must explicitly set:

```yaml
security:
  url_allowlist:
    enabled: false                # Disable allowlist checks
    allow_insecure_http: true     # Allow HTTP URLs (⚠️ INSECURE)
```

**Or via environment variable:**

```bash
SECURITY_URL_ALLOWLIST_ENABLED=false
SECURITY_URL_ALLOWLIST_ALLOW_INSECURE_HTTP=true
```

**Risks of allowing HTTP:**
- API keys and data transmitted in **plaintext** (vulnerable to interception)
- Susceptible to **man-in-the-middle (MITM) attacks**
- **NOT suitable for production** environments

**When to use HTTP:**
- ✅ Development/testing with local servers (http://localhost)
- ✅ Internal networks with trusted endpoints
- ✅ Testing account connectivity before obtaining HTTPS
- ❌ Production environments (use HTTPS only)

**Example error without this setting:**
```
Invalid base URL: invalid url scheme: http
```

If you disable URL validation or response header filtering, harden your network layer:
- Enforce an egress allowlist for upstream domains/IPs
- Block private/loopback/link-local ranges
- Enforce TLS-only outbound traffic
- Strip sensitive upstream response headers at the proxy

#### ⚠️ Important: Creating the Admin Account

The initial admin account is **only created via the setup wizard** (served at `http://<host>:8080` on first run). The `default.admin_email` / `default.admin_password` fields in `config.yaml` are **not used** to create it — they exist in the template for historical reasons.

Because pre-creating `config.yaml` can cause the setup wizard to be skipped on first run, the safest path is:

1. **Recommended — let the wizard generate `config.yaml`:** Do not create `config.yaml` manually before first startup. Start `./sub2api` directly and complete setup in the browser.

2. **If you already created `config.yaml`:** Temporarily move it aside so the wizard can trigger on first run:
   ```bash
   mv config.yaml config.yaml.bak
   ./sub2api
   # complete setup in the browser, then restore or reconcile your config afterwards
   ```

#### 启动后端
```bash
./sub2api
```

---

## 部署建议

推荐生产环境使用：

- Docker / Docker Compose
- 宝塔面板 / Nginx 反向代理
- HTTPS
- PostgreSQL
- Redis
- 域名访问

如果你使用的是源码构建镜像部署，请确认最终运行的是**你自己的源码构建镜像**，而不是官方预构建镜像，否则前端自定义内容可能不会生效。

---

## 宝塔面板部署建议

如果你使用宝塔面板，推荐：

1. 拉取项目源码到服务器
2. 使用 Docker Compose 启动项目
3. 在宝塔中创建网站
4. 通过反向代理转发到：
   ```text
   http://127.0.0.1:8080
   ```
5. 配置 SSL，开启 HTTPS
6. 在 Nginx 主配置中建议开启：
   ```nginx
   underscores_in_headers on;
   ```

---

## 项目结构

```text
sub2api/
├── backend/                  # Go 后端服务
│   ├── cmd/server/           # 后端入口
│   ├── internal/             # 核心业务模块
│   └── resources/            # 静态与资源文件
│
├── frontend/                 # Vue 前端
│   ├── src/views/            # 页面视图
│   ├── src/components/       # 公共组件
│   ├── src/stores/           # 状态管理
│   ├── src/router/           # 路由配置
│   └── src/i18n/             # 国际化文案
│
└── deploy/                   # Docker / Compose / 部署配置
```

---

## 升级说明

如果你在此项目基础上维护了自己的品牌化前端，升级上游版本时建议：

1. 先同步上游后端与功能更新
2. 保留本地品牌/UI 改动
3. 手工处理前端冲突文件
4. 升级后重新构建前端并验证页面显示
5. 确认 Docker 部署仍然使用你的源码构建镜像

---

## 注意事项

- 本项目依赖 PostgreSQL 与 Redis
- 若使用 Docker 部署，请确认运行的是**你自己的源码构建镜像**
- 若已经做了前端品牌化修改，不建议直接覆盖本地前端文件
- 建议升级前做好数据库与部署配置备份
- 建议生产环境使用域名 + HTTPS 部署

---

## 项目目标

ClaudeAPI 致力于提供一个完整的：

> **AI API 接入、管理、调度、计费与运营一体化平台**

帮助开发者和团队通过统一方式管理模型能力、用户访问、调用成本与业务运营。

---

## 上游项目

本项目基于上游项目 **Sub2API** 进行定制与扩展开发。  
感谢原项目作者及社区贡献者。

- [Wei-Shaw/sub2api](https://github.com/Wei-Shaw/sub2api)

---

## License

请根据你当前仓库的实际授权策略进行保留或调整。  
如果继续沿用上游授权，请确保与上游 License 保持一致。
