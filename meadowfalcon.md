# NovaLink 技术资源导航

NovaLink 是一个面向数据聚合与赛事信息追踪的开源技术资源导航站，专为需要快速获取多源动态数据的技术人员、运维工程师及信息分析人员设计。该项目通过结构化梳理外部数据接口与信息源，解决分散数据难以统一管理、信息滞后及人工维护成本高的问题，提供一套可自托管的轻量级外链资源中枢。

NovaLink 不直接存储或代理任何数据内容，而是以严格分类的链接集合为核心资产，辅以自动化可用性检测与访问日志记录，帮助团队在内部搭建可信、可维护的外部信息索引层。适用于需要实时参考第三方赛事结果、积分榜单或赛程变更通知的各类内部管理系统。

## 功能概览

- **多源链接分类管理** 支持按数据领域、更新频率及信任等级对原始 URL 进行标签化分组，提供层级清晰的导航树。

- **可用性主动探测** 定时对收录的每一个外部链接执行 HTTP HEAD/GET 探测，自动标记超时、证书过期或返回异常状态码的资源。

- **访问日志聚合** 记录每个外部链接的调用次数、最近访问时间与响应耗时，辅助分析数据源的稳定性与使用热度。

- **自定义元数据扩展** 允许为每条链接附加备注字段，例如数据更新周期、鉴权要求或备用镜像地址，满足企业级维护需求。

- **只读只写分离视图** 提供面向浏览者的只读聚合页与面向管理员的编辑后台，避免误操作，降低维护风险。

- **全文检索与筛选** 基于链接标题、描述、分类标签及备注字段进行快速关键词搜索，支持按状态码、分类或更新时间范围过滤。

- **配置即代码** 所有链接与分组配置以 YAML 文件存储于仓库中，支持版本控制、变更审计与一键回滚。

## 应用场景

1. 内部数据看板的后端数据源配置 运维团队可将 NovaLink 部署为看板服务器的本地数据源配置中心，统一维护所有外部赛事接口与积分查询地址，当外部接口变更时只需更新一处配置，无需逐个修改看板图表。

2. 赛事信息监控系统的前置校验层 数据分析部门可利用 NovaLink 的可用性探测功能，在每日调度任务启动前自动检查各数据源的可达性，若检测到异常则触发告警并切换至备用源，保障监控链路不中断。

3. 合规审计的访问链路记录 在金融或政务类内部系统中，NovaLink 的访问日志可输出为结构化数据，用于证明所有外部数据拉取行为均经过已登记的正规公开源，满足内部合规审查要求。

4. 新员工信息源快速上手 团队新成员可通过 NovaLink 的分类导航树，迅速了解本团队依赖的全部外部数据服务及其用途说明，减少口头传递带来的信息遗漏。

5. 离线文档站的外链代理层 若内部文档站需要引用大量外部赛程或积分链接，可统一改写为 NovaLink 的跳转路由，当外部链接失效时可统一更换目标地址，避免逐个修改文档页面。

## 快速开始

以下命令演示如何从 GitHub 克隆项目、安装依赖并启动开发服务。

```bash
git clone https://github.com/novalink-dev/novalink-core.git
cd novalink-core
npm install
npm run start:dev
```

如需以生产模式运行，请先执行构建步骤：

```bash
npm run build
NODE_ENV=production npm start
```

若使用 Docker 快速体验，可执行：

```bash
docker build -t novalink:latest .
docker run -p 3000:3000 -v ./config:/app/config novalink:latest
```

启动后，访问 <code>http://localhost:3000</code> 即可浏览默认导航页面，管理后台位于 <code>/admin</code> 路径，初始管理员账号与密码请查阅仓库中的 `.env.example` 文件。

## 安装要求

| 依赖组件 | 必需版本 | 说明 |
|---------|---------|------|
| Node.js | v18.x 或 v20.x LTS | 运行时环境，推荐使用 v20.11.0 以上版本 |
| npm | v9.x 或 v10.x | 包管理器，随 Node.js 自动安装 |
| SQLite3 | 系统内置或附加模块 | 默认使用嵌入式 SQLite 存储日志与探测记录，无需额外安装数据库服务 |
| Git | v2.25+ | 用于克隆仓库及后续拉取配置更新 |
| 系统时区数据 | 任意 POSIX 兼容 | 用于正确记录探测时间戳，建议设置为 Asia/Shanghai |
| 网络出口策略 | 允许对外 TCP/80 与 TCP/443 出站 | 可用性探测需要访问外部 HTTP/HTTPS 服务，须开放相应出方向防火墙 |
| 磁盘空间 | 至少 200 MB 可用 | 存储代码、依赖及 SQLite 数据库文件，日志轮转后可进一步压缩 |

## 文档导航

| 层面 | 目录 | 回答的问题 |
|-----|------|-----------|
| 入门指南 | `/docs/getting-started.md` | 如何从零开始部署 NovaLink，包括环境配置、首次启动及默认账户设置 |
| 配置参考 | `/docs/configuration.md` | 详解 `config/links.yaml` 中所有字段含义、分类结构设计及自定义探测参数 |
| API 手册 | `/docs/api-reference.md` | 提供内部 RESTful API 端点说明，用于与现有监控系统或自动化脚本集成 |
| 运维指南 | `/docs/operations.md` | 涵盖日志清理策略、探测频率调优、异常告警配置及备份恢复方案 |
| 架构设计 | `/docs/architecture.md` | 说明模块分层、探测调度器设计、存储模型及扩展点，适用于二次开发 |

## 资源列表

本导航项目收录的全部外部数据源依据原始提供方进行分类陈列，所有链接均严格按照用户原始数据原样呈现，未做任何格式修饰或协议补全。用户部署后可根据自身需求对分类标签进行本地化调整，但原生配置中保留以下完整清单。

体育赛事综合类

- <code>ouxielianzigesaibisaijieguo.org.cn</code>

- <code>ouxielianzigesaisaicheng.org.cn</code>

联赛积分榜单类

- <code>yingchaojifenbang.net.cn</code>

- <code>yijiajishibifen.net.cn</code>

- <code>fenchaojifenbang.net.cn</code>

- <code>fenchaobifen.net.cn</code>

赛程信息类

- <code>yingchaosaicheng.net.cn</code>

## 项目结构

项目遵循分层模块化设计，核心代码与配置严格分离，便于多环境部署。

```
novalink-core/
├── config/                        # 配置文件目录（不含敏感信息）
│   ├── default.yaml               # 默认端口、日志级别、探测间隔
│   └── links.yaml                 # 所有外部链接的分类与元数据定义
├── src/
│   ├── core/                      # 核心调度与生命周期管理
│   │   ├── app.module.ts          # 应用主模块依赖注入
│   │   └── bootstrap.ts           # 启动引导与异常捕获
│   ├── probe/                     # 可用性探测模块
│   │   ├── http-checker.ts        # 基于 axios 的 GET/HEAD 探测实现
│   │   └── scheduler.ts           # 基于 cron 的定时任务编排
│   ├── storage/                   # 数据存储抽象层
│   │   ├── sqlite-repo.ts         # SQLite3 访问接口（日志与状态）
│   │   └── memory-cache.ts        # 运行时热点数据缓存
│   ├── web/                       # HTTP 服务与路由
│   │   ├── controllers/           # 请求处理器（导航页、管理 API）
│   │   ├── middlewares/           # 鉴权、日志、跨域中间件
│   │   └── views/                 # 服务端渲染模板（ejs）
│   └── utils/                     # 通用工具函数
│       ├── yaml-loader.ts         # 递归加载并校验配置
│       └── logger.ts              # 结构化日志封装（pino）
├── tests/                         # 单元与集成测试
│   ├── unit/                      # 独立模块测试
│   └── integration/               # 端到端流程测试（含模拟外部服务）
├── scripts/                       # 辅助运维脚本
│   ├── migrate-db.sh              # 数据库表结构初始化/升级
│   └── health-check.sh            # 外部依赖连通性预检
├── docs/                          # 完整文档源码（参见文档导航）
├── Dockerfile                     # 多阶段构建镜像文件
├── docker-compose.yml             # 快速启动 compose 编排（含 SQLite 持久化卷）
├── package.json                   # npm 清单，含 scripts 与依赖锁定
└── README.md                      # 本文档
```

## 贡献指南

我们欢迎各类形式的贡献，包括但不限于新增链接分类规则、改进探测逻辑、完善文档或报告可用性检测误报。请遵循以下步骤参与项目。

1. 查阅现有 Issues 与 Projects 看板 在提交新功能或修复前，请先访问项目的 GitHub Issues 页面，确认是否存在相同或相关的讨论，避免重复劳动。若为新需求，建议先创建 Issue 进行设计沟通。

2. 派生仓库并创建功能分支 将主仓库派生至个人账户，然后基于 `main` 分支新建以 `feature/` 或 `fix/` 为前缀的分支，例如 `feature/add-football-group`。本地开发时请保持分支原子性，每次提交仅关联一个逻辑变更。

3. 编写或更新测试用例 所有新增探测策略或配置解析逻辑必须附带对应的单元测试，确保覆盖率不低于 80%。运行 `npm run test` 验证全部用例通过后方可提交。

4. 提交前执行代码规范检查 使用 `npm run lint` 与 `npm run format` 统一代码风格，并确保 `npm run build` 无报错。提交信息遵循 Conventional Commits 格式，例如 `feat(probe): add retry policy for timeout errors`。

5. 发起 Pull Request 并关联 Issue 推送分支后向主仓库 `main` 分支发起 Pull Request，描述中明确说明变更目的、测试结果及影响范围。至少一位核心维护者审查通过后即可合并。

## 常见问题

**问：启动后日志显示外部链接全部探测失败，但浏览器可直接访问这些域名，原因是什么？**

答：绝大多数情况是由于部署环境位于内网或容器中，未配置正确的 DNS 解析服务器。请检查宿主机的 `/etc/resolv.conf` 或容器 `--dns` 参数，确保可解析公网域名。此外，若企业网络要求通过代理出站，请在 `config/default.yaml` 中设置 `httpAgent` 与 `httpsAgent` 代理地址。同时，部分域名运营商可能对非浏览器 User-Agent 返回 403，您可在链接元数据中自定义 `probeHeaders` 覆盖默认请求头。

**问：如何批量导入新的外链资源，而不必逐条编辑 YAML 文件？**

答：项目提供了 `scripts/import-csv.ts` 工具，支持从标准 CSV 格式（列标题：category, title, url, description, ttl）导入新增链接，执行 `npm run import-csv -- ./data/new-links.csv` 即可将数据追加至 `config/links.yaml` 中。导入前请确保 CSV 文件编码为 UTF-8，且 url 列不包含协议前缀以外的多余字符。导入后建议使用 `npm run validate-config` 进行格式校验，避免 YAML 缩进错误导致服务无法启动。

**问：探测日志增长速度过快，如何调整存储策略？**

答：SQLite 数据库默认保留最近 7 天的探测记录，每日凌晨 2:00 执行自动清理任务。如需修改保留周期，可在 `config/default.yaml` 中调整 `storage.retentionDays` 参数。若仍觉空间不足，可启用外部 PostgreSQL 或 MySQL 作为替代存储后端，具体配置方法参见 `/docs/operations.md` 中的“数据库迁移”章节。对于高频访问场景，建议同时开启 `storage.compressLogs` 选项，对历史记录进行 gzip 压缩存储。

## 许可证

MIT

> 外链数量: 7 | 生成时间: 2026-07-22 11:10:39
