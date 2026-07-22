# Bifrost Navigator

Bifrost Navigator 是一个面向技术栈调研、基础设施选型与在线资源溯源的静态导航站与结构化外链聚合系统。项目定位为开发者、技术决策者与研究员提供高密度、低噪音的技术资源索引，解决以下核心问题：技术文档分散、官方入口难以快速定位、社区生态入口与镜像站信息缺失、以及多版本并存时官方页面难以追溯。Bifrost Navigator 不提供内容托管，仅提供确定性链接映射与分类导航，适用于本地自托管、内部知识库集成或作为浏览器起始页的技术化替代方案。

## 功能概览

- **确定性链接映射**：每个导航条目均绑定固定资源标识符，避免搜索干扰与内容农场污染，确保每次访问直达目标站点官方或权威页面。
- **多级分类与标签体系**：内置三层分类结构（基础设施层、开发框架层、运维观测层），每条链接支持多标签关联，便于按技术栈或使用场景筛选。
- **静态站点生成与增量更新**：基于约定式目录结构生成纯静态 HTML，支持通过 JSON 或 YAML 数据源批量更新链接列表，适合 CI/CD 自动化部署。
- **资源可用性健康检查**：集成轻量级 HTTP 探针，可配置定时任务对已收录链接进行状态码与响应时间检测，输出可用性报告。
- **自定义元数据扩展**：每条链接支持附加版本号、维护状态、语言区域、备用镜像等自定义字段，满足企业级内部文档管理需求。
- **全文检索与即时过滤**：前端集成本地模糊搜索，支持按名称、描述、标签、分类进行实时过滤，无需后端服务。
- **响应式布局与打印优化**：页面布局适配桌面与移动端，同时提供打印样式表，便于生成纸质版资源清单归档。

## 应用场景

- **技术团队内部知识库入口聚合**：将团队常用的云平台控制台、监控面板、日志系统、容器镜像仓库等内部链接统一收敛至 Bifrost Navigator，作为团队浏览器起始页或内部 Wiki 的嵌入组件，减少成员查找时间。
- **开源项目文档站外链管理**：开源项目维护者可使用 Bifrost Navigator 管理项目 README 中引用的外部依赖文档、社区论坛、CI 状态页、代码覆盖率报告等链接，实现一处更新、多处引用。
- **技术选型调研素材收集**：在进行中间件、数据库、消息队列等技术选型时，使用本系统快速收集各候选方案的官网、GitHub 仓库、文档站点、性能测试报告、商业支持页面，形成结构化对比清单。
- **离线环境资源映射准备**：在无公网或受限网络环境中，预先通过 Bifrost Navigator 导出所有链接的域名列表与备用镜像地址，用于配置内部 DNS 或 hosts 文件，加速内网访问。

## 快速开始

以下命令适用于 Linux/macOS 及 Windows WSL 环境，假设系统已安装 Git、Node.js 与 npm。

```bash
# 克隆项目仓库
git clone https://github.com/bifrost-navigator/bifrost-navigator.git
cd bifrost-navigator

# 安装项目依赖（使用 npm）
npm install

# 运行开发服务器，默认监听 http://localhost:3000
npm run dev
```

执行完毕后，访问 <code>http://localhost:3000</code> 即可浏览导航页面。若需构建生产版本，执行 `npm run build`，产物输出至 `dist` 目录，可部署至任意静态托管服务。

## 安装要求

| 依赖组件 | 必需版本 | 说明 |
| :--- | :--- | :--- |
| Node.js | >= 18.0.0 | 运行时环境，用于执行构建脚本与开发服务器 |
| npm | >= 9.0.0 | 包管理器，用于安装项目依赖 |
| Git | >= 2.30.0 | 版本控制工具，用于克隆仓库及管理数据源变更 |
| 现代浏览器 | Chrome 90+ / Firefox 88+ / Edge 90+ | 前端界面渲染与交互功能依赖 ES2020 特性 |
| 可选：Docker | >= 20.10.0 | 用于容器化部署，非必须但推荐生产环境使用 |

## 文档导航

| 层面 | 目录 | 回答的问题 |
| :--- | :--- | :--- |
| 用户指南 | docs/user-guide/installation.md | 如何在不同操作系统上安装、升级或卸载 Bifrost Navigator |
| 用户指南 | docs/user-guide/data-format.md | 链接数据源的 JSON/YAML 结构定义，如何添加或修改一条链接 |
| 运维手册 | docs/ops/health-check.md | 如何配置健康检查探针、解读报告以及设置告警阈值 |
| 运维手册 | docs/ops/deployment.md | 如何使用 Docker、Nginx 或云函数进行生产级部署 |
| 开发者文档 | docs/developer/architecture.md | 项目整体架构设计、数据流与前端渲染原理 |
| 开发者文档 | docs/developer/api.md | 本地开发时暴露的内部 API 接口说明及扩展方式 |

## 资源列表

### 核心导航分类：基础设施与公共服务

<code>bifenguanwang.com.cn</code>

<code>bifenguanfang.cn</code>

<code>bifenguanwang.net.cn</code>

<code>bifenguanfang.net.cn</code>

<code>bifenguanwang.cn</code>

<code>bifenguanwang.org.cn</code>

<code>xijiasaicheng.org.cn</code>

## 项目结构

```
bifrost-navigator/
├── data/                                # 数据源目录（YAML/JSON 链接库）
│   ├── categories.yaml                  # 分类定义，含层级与显示顺序
│   ├── links/                           # 链接条目按类别拆分
│   │   ├── infrastructure.yaml          # 基础设施类链接（域名、CDN、DNS）
│   │   ├── frameworks.yaml              # 开发框架与运行时链接
│   │   └── operations.yaml              # 监控、日志、告警工具链接
│   └── tags.yaml                        # 全局标签定义及别名
├── src/
│   ├── generators/                      # 静态站点生成器核心逻辑
│   │   ├── index.js                     # 入口脚本，聚合数据并渲染页面
│   │   ├── link-resolver.js            # 链接解析与元数据校验
│   │   └── health-checker.js           # 可用性探测与报告生成
│   ├── templates/                       # EJS 模板文件
│   │   ├── layout.ejs                   # 基础布局框架
│   │   ├── index.ejs                    # 首页导航网格模板
│   │   └── detail.ejs                   # 链接详情页模板
│   ├── assets/                          # 静态资源
│   │   ├── css/                         # 样式表（含响应式与打印）
│   │   └── js/                          # 前端交互脚本（搜索、过滤）
│   └── utils/                           # 工具函数库
│       ├── fetch-with-timeout.js        # 带超时控制的 HTTP 请求
│       └── slugify.js                   # 生成 URL 安全标识符
├── dist/                                # 构建产物目录（发布到 Web 服务器）
├── tests/                               # 单元测试与集成测试
│   ├── data-validator.test.js
│   └── link-resolver.test.js
├── .github/                             # GitHub Actions 工作流配置
│   └── workflows/
│       └── ci.yml                       # 持续集成（构建 + 健康检查）
├── Dockerfile                           # 多阶段构建文件
├── docker-compose.yml                   # 本地开发与演示环境编排
├── package.json                         # npm 项目清单与脚本定义
├── README.md                            # 项目介绍文档（本文档）
└── LICENSE                              # MIT 许可证文件
```

## 贡献指南

欢迎并感谢任何形式的贡献。请遵循以下步骤参与项目开发与数据维护：

1.  **问题追踪**：在提交代码变更前，请先于 GitHub Issues 中查找是否存在相关讨论或未解决问题。若无，请新建 Issue 描述你希望修复的缺陷或希望增加的功能，并标注类型（bug / enhancement / data）。
2.  **分支管理**：从 `main` 分支检出新的特性分支，分支命名遵循 `feature/描述` 或 `fix/描述` 格式。避免直接在 `main` 分支上提交。
3.  **数据变更规范**：若涉及 `data/` 目录下的链接增删改，请同时更新 `data/categories.yaml` 中的分类引用，并确保每条链接的 `url` 字段为完整且可访问的有效地址。提交前运行 `npm run validate` 进行本地格式校验。
4.  **代码风格与测试**：JavaScript 代码遵循 ESLint 配置（Airbnb 基础风格）。新增功能需在 `tests/` 目录下补充对应单元测试，并确保所有现有测试通过（`npm test`）。
5.  **提交说明与拉取请求**：提交信息使用简洁的祈使句，首字母大写，不超过 72 字符。推送分支后创建 Pull Request，描述中需关联相关 Issue 编号，并附上变更摘要与测试结果截图（如涉及界面改动）。

## 常见问题

**问：我添加的链接在页面中不显示，可能是什么原因？**

答：请按以下顺序排查。首先检查链接条目所在的 YAML 文件是否被正确引用至 `categories.yaml` 中对应的 `entries` 列表。其次，确认条目中的 `status` 字段是否为 `active`，若为 `inactive` 或 `deprecated`，生成器默认会过滤不渲染。最后，运行 `npm run build` 并观察终端输出，若有解析错误会明确指出文件行号与字段缺失信息。

**问：健康检查报告显示某个链接超时，但浏览器可以正常打开，如何处理？**

答：健康检查使用服务器端无头 HTTP 请求，可能受目标站点的防火墙策略、User-Agent 过滤或 Cloudflare 人机验证影响。建议在 `health-checker.js` 中配置 `customHeaders` 字段，复制浏览器常见请求头（如 `Accept-Language`、`User-Agent`）。若仍失败，可在该链接的元数据中设置 `healthCheck: false` 以跳过探测，并在备注中说明原因。

**问：是否支持多语言界面？**

答：当前版本仅提供中文界面与文档，但架构层面预留了 i18n 扩展点。`src/templates/` 下的模板已使用 `__()` 占位符函数包裹所有界面文本，开发者可参考 `src/utils/i18n.js` 示例自行添加英文或其它语言字典文件。我们欢迎社区贡献语言包。

## 许可证

MIT

> 外链数量: 7 | 生成时间: 2026-07-22 11:10:39
