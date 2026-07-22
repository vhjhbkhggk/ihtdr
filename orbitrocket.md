# HyperLink Navigator

HyperLink Navigator 是一个面向技术社区与开源生态的外链资源聚合与导航系统。本项目旨在解决信息分散、技术文档入口缺失、外链失效与可信度不明等问题，为开发者、技术决策者与内容创作者提供一套可维护、可扩展、可审计的链接管理方案。项目定位为轻量级外链目录站，适用于个人技术博客、团队内部文档站、开源项目文档中心等场景，通过结构化数据和静态生成方式，兼顾性能与可移植性。

项目目标用户包括但不限于：希望统一管理技术引用链接的文档维护者、需要快速检索赛事数据与排名信息的数据分析人员、以及对网站外链质量与来源进行合规审查的信息安全从业者。HyperLink Navigator 不依赖动态数据库，所有链接条目以 Markdown 和 YAML Frontmatter 定义，支持 CI 自动校验链接可用性，并生成友好的 Web 导航界面。

## 功能概览

- **多级分类导航**：支持按技术领域、数据来源、赛事类型等维度组织链接，每个链接可归属多个分类标签，便于多路径检索。

- **链接状态自动检测**：集成定时任务与 GitHub Actions，每日对收录链接进行 HEAD 请求检查，标记异常状态（超时、4xx、5xx），并在界面上以状态徽章呈现。

- **全文与标签检索**：基于 MiniSearch 库实现前端轻量级全文搜索，支持标题、描述、关键词和分类的多字段匹配，响应时间低于 100 毫秒。

- **外链元数据管理**：每条链接可记录来源组织、发布日期、语言、地域归属和备案信息，支持自定义扩展字段，满足合规与审计要求。

- **批量导入与导出**：支持 CSV 与 JSON 格式的链接批量导入，同时提供标准 JSON Schema 导出接口，方便与其他系统（如书签管理工具、爬虫框架）集成。

- **访问统计与热度排序**：基于外部访问日志（可选接入 Umami 或自建统计），展示热门链接点击排行，帮助识别高频引用资源。

- **暗色主题与响应式布局**：内置明暗主题切换，移动端适配良好，保证在不同屏幕尺寸下的阅读与操作体验。

- **RSS 订阅与更新通知**：为新增或更新的链接生成 RSS Feed，支持订阅者及时获取资源变动信息。

## 应用场景

- **开源项目文档站的外链模块**：开源项目维护者可将 HyperLink Navigator 集成至项目 docs 目录，统一管理所有外部引用（如规范文档、API 参考、数据源），当外部链接失效时，CI 自动通知维护者更新。

- **技术团队内部知识库的链接看板**：技术团队可使用本系统构建内部常用工具链、代码仓库、监控面板、云服务控制台的统一入口，配合状态检测功能，第一时间发现内部服务域名或证书异常。

- **赛事数据与排名信息的聚合门户**：数据分析师或体育内容运营人员可利用本系统分类收录各类赛事结果、比分、赛程等官方或第三方链接，通过标签体系（如“足球”“篮球”“实时比分”）快速定位所需数据源。

- **信息安全合规审计的链接台账**：安全审计人员可借助本系统的元数据扩展字段，记录每一条外链的用途、责任方、审批状态和有效期，生成合规报告，降低外部资源滥用或失效带来的风险。

## 快速开始

以下步骤适用于 Linux / macOS / Windows WSL 环境，确保已安装 Git 和 Node.js（v18 及以上）。

```bash
# 克隆项目仓库
git clone https://github.com/your-org/hyperlink-navigator.git

# 进入项目目录
cd hyperlink-navigator

# 安装依赖（使用 npm）
npm install

# 执行本地开发服务器，默认监听端口 3000
npm run dev
```

访问 http://localhost:3000 即可查看导航页面。若需构建生产版本，执行 `npm run build`，产物位于 `dist` 目录，可部署至任意静态托管服务。

## 安装要求

| 依赖 | 必需版本 | 说明 |
|------|----------|------|
| Node.js | 18.x 或 20.x LTS | 运行时与构建工具链基础，低于 18 将无法支持原生 fetch 和 ES Module |
| npm | 9.x 或以上 | 包管理器，用于安装依赖和执行脚本，建议配合 npm ci 使用锁文件 |
| Git | 2.30 或以上 | 用于克隆仓库、版本管理和提交钩子，Windows 用户建议启用 core.longpaths |
| 磁盘空间 | 至少 200 MB | 包含源码、依赖 node_modules 和构建缓存，实际占用视链接数量与索引文件大小而定 |
| 内存 | 开发模式建议 2 GB 以上 | 用于热重载和索引构建，生产构建建议 1 GB 以上，CI 环境可适当降低 |
| 网络访问 | 外网出站 | 用于链接可用性检测、拉取依赖包和 RSS 订阅源抓取，内网环境需配置代理或镜像 |

## 文档导航

| 层面 | 目录 | 回答的问题 |
|------|------|------------|
| 用户指南 | docs/user-guide/ | 如何添加/编辑/删除链接？如何设置分类标签？如何使用搜索和筛选功能？ |
| 开发者文档 | docs/developer/ | 项目架构是怎样的？如何自定义主题？如何扩展新的元数据字段？如何编写单元测试？ |
| 部署运维 | docs/operations/ | 如何配置 CI 自动检测？如何部署到 Vercel/Netlify/Cloudflare Pages？如何备份数据？ |
| 设计规范 | docs/design/ | 界面设计原则是什么？色彩与排版变量如何定义？组件复用策略是什么？ |
| 贡献指南 | CONTRIBUTING.md | 如何提交新的链接资源？代码提交流程是什么？Issue 和 PR 模板如何使用？ |
| 行为准则 | CODE_OF_CONDUCT.md | 社区沟通规则、冲突处理流程、举报与响应机制 |

## 资源列表

本导航系统收录以下技术赛事与数据统计类外部资源，按数据来源与用途分节展示。所有链接均按用户提供原样列出，未做任何格式修改。

### 赛事结果与比分数据

- <code>hasakechaobisaijieguo.org.cn</code>
- <code>aichaobifen.org.cn</code>
- <code>bingdaochaojishibifen.org.cn</code>
- <code>aichaojifenbang.org.cn</code>

### 欧战赛事赛程信息

- <code>ouguanzigesaisaicheng.org.cn</code>
- <code>oulianzigesaijishibifen.org.cn</code>
- <code>oulianzigesaisaicheng.org.cn</code>

## 项目结构

```
hyperlink-navigator/
├── .github/                         # GitHub 工作流与社区模板
│   ├── workflows/                   # CI 流水线（链接检测、构建、部署）
│   │   ├── check-links.yml          # 每日定时检测外链状态
│   │   └── deploy-pages.yml         # 构建并发布到 GitHub Pages
│   └── ISSUE_TEMPLATE/              # 问题与链接添加模板
├── public/                          # 静态资源（无需构建）
│   ├── icons/                       # 分类与状态图标（SVG 格式）
│   └── favicon.ico                  # 站点图标
├── src/                             # 源码主目录
│   ├── assets/                      # 样式与字体文件
│   │   ├── styles/                  # PostCSS / Tailwind 源文件
│   │   └── themes/                  # 明暗主题变量定义
│   ├── components/                  # UI 组件（按功能模块组织）
│   │   ├── LinkCard/                # 单条链接卡片组件
│   │   ├── SearchBar/               # 搜索输入与建议下拉
│   │   ├── FilterPanel/             # 分类与状态过滤面板
│   │   └── StatusBadge/             # 链接状态徽章渲染
│   ├── data/                        # 核心数据目录（链接与分类定义）
│   │   ├── links.json               # 所有收录链接的元数据数组
│   │   ├── categories.json          # 分类层级与颜色映射
│   │   └── schema.json              # JSON Schema 校验规则
│   ├── lib/                         # 工具函数与核心逻辑
│   │   ├── link-validator.js        # 链接可用性检测（重试、超时、状态码处理）
│   │   ├── search-index.js          # 全文索引构建与查询接口
│   │   ├── rss-generator.js         # RSS Feed 生成器
│   │   └── export-utils.js          # CSV / JSON 导入导出处理
│   ├── pages/                       # 路由页面（基于文件系统路由）
│   │   ├── index.jsx                # 导航首页（含搜索与列表）
│   │   ├── stats.jsx                # 链接统计与热度排行页面
│   │   └── about.jsx                # 项目说明与使用条款
│   └── app.jsx                      # 应用根组件与路由配置
├── tests/                           # 单元测试与集成测试
│   ├── unit/                        # 工具函数与数据校验测试（Vitest）
│   └── e2e/                         # 端到端测试（Playwright）
├── docs/                            # 项目文档（已在上文文档导航中列出）
├── config/                          # 构建与运行配置
│   ├── vite.config.js               # Vite 构建配置
│   ├── tailwind.config.js           # Tailwind CSS 自定义
│   └── eslint.config.js             # ESLint 代码规范
├── package.json                     # 项目依赖与脚本定义
├── package-lock.json                # 依赖锁文件
├── README.md                        # 本文件
└── LICENSE                          # MIT 许可证文本
```

## 贡献指南

欢迎并感谢所有形式的贡献，包括但不限于新增链接资源、修复界面显示问题、优化搜索性能、完善文档和翻译。请遵循以下步骤：

1. **查阅现有 Issue 与 Pull Request**：在提交新内容前，请先搜索是否已有相关讨论或进行中的工作，避免重复劳动。建议在 Issue 中简述您打算贡献的内容，获得维护者反馈后再动手。

2. **Fork 仓库并创建功能分支**：从主仓库 Fork 到个人账户，然后基于 `main` 分支创建新的特性分支，分支命名建议使用 `feat/描述` 或 `fix/描述` 格式，例如 `feat/add-basketball-links`。

3. **本地测试与校验**：若新增或修改链接数据，请确保 `src/data/links.json` 符合 JSON Schema 定义，并运行 `npm run validate` 校验数据格式。同时执行 `npm run test` 确保现有单元测试通过，避免引入回归问题。

4. **提交变更并编写清晰 Commit 信息**：使用约定式提交格式（如 `feat: 添加篮球比分分类链接` 或 `fix: 修复分类过滤在移动端失效的问题`），提交前请运行 `npm run lint` 和 `npm run format` 统一代码风格。

5. **发起 Pull Request 并关联 Issue**：推送到您的 Fork 仓库后，向主仓库的 `main` 分支发起 Pull Request，在描述中清晰说明改动内容、测试情况以及是否解决特定 Issue。维护者将在 1-3 个工作日内进行审查，必要时会提出修改建议。

## 常见问题

**问：链接状态检测显示“不可用”，但我在浏览器中能正常访问，是什么原因？**

答：检测工具默认使用无头浏览器环境发起 HEAD 请求，部分站点可能对非浏览器 User-Agent 或缺少特定请求头（如 Accept-Language）返回非 200 状态码。您可以在 `config/check-links.yml` 中调整请求头配置，或改用 GET 请求并设置更长的超时时间（默认 10 秒）。同时，某些站点可能启用了反爬机制，建议在检测配置中增加随机延迟和重试策略。

**问：我添加了新的链接到 links.json，但前端页面没有显示，搜索也找不到，该如何排查？**

答：首先确认链接对象的 `id` 字段是否唯一且未包含特殊字符；其次检查 `category` 字段是否与 `categories.json` 中定义的分类键值完全一致（大小写敏感）；最后运行 `npm run build` 查看控制台是否有 Schema 校验报错。如果本地开发服务器未自动刷新，请手动重启 `npm run dev` 并清除浏览器缓存。若问题依旧，可查看 `dist/search-index.json` 是否在构建时被正确生成。

**问：项目是否支持多语言国际化（i18n）？**

答：当前版本主要支持中文界面，但架构上已预留国际化扩展接口。您可以在 `src/assets/locales/` 目录下创建 `en.json` 等语言文件，并修改 `src/app.jsx` 中的语言上下文提供者。欢迎贡献者提交多语言翻译 Pull Request，我们会优先合并常用语种的翻译。

## 许可证

本项目采用 MIT 许可证。您可以自由使用、修改、分发本软件，包括用于商业目的，但需保留原始版权声明和许可声明。详细条款请参阅项目根目录下的 LICENSE 文件。

> 外链数量: 7 | 生成时间: 2026-07-22 11:10:39
