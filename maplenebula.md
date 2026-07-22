# TechLink Navigator

TechLink Navigator 是一个面向开发人员、技术决策者与内容策展人的轻量级技术资源导航工具。该项目定位于解决技术信息过载背景下的资源筛选与路径回溯问题，通过结构化方式管理外部技术参考链接，并提供可复用的外链聚合方案。目标用户包括技术文档撰写者、开源项目维护人、技术社区运营人员，以及需要持续跟踪多个外部数据源的一线研发团队。

项目本身不存储具体业务数据，而是以配置驱动的方式加载外部资源列表，支持快速检索、分类筛选与访问状态检测。通过将原始 URL 清单作为数据输入，TechLink Navigator 能够生成静态导航页面、命令行快速跳转工具，或嵌入现有文档站点的外链模块。其核心价值在于将零散、易失效的外部链接转化为可维护、可审计、可团队共享的资产，降低因链接遗忘或变更导致的信息断裂风险。

## 功能概览

- **批量链接导入与解析**：支持一次性导入多条原始 URL，自动识别协议头、域名后缀与路径结构，并生成标准化条目。

- **分类标签管理系统**：允许为每条链接分配一个或多个分类标签（如“比分数据”、“赛事结果”、“技术博客”），支持按标签筛选与聚合。

- **链接可用性健康检查**：内置简易 HTTP 状态探测模块，可定时或手动检查链接是否可访问，并标记异常状态。

- **自定义输出模板**：提供默认 Markdown 表格模板与 JSON 结构化输出，用户也可编写自己的 Jinja2 模板以生成不同格式的导航页。

- **命令行交互界面**：提供 CLI 工具，支持搜索、添加、删除、导出等常用操作，便于在终端环境中快速管理外链库。

- **版本化变更日志**：每次链接增删改操作均记录时间戳与操作摘要，支持回滚至任意历史状态。

- **多格式数据导出**：支持导出为 Markdown 列表、HTML 目录树、JSON API 数据源或纯文本 hosts 风格清单。

## 应用场景

1. **技术文档站的外链治理**：当项目文档中引用大量第三方规范、工具主页或数据接口时，使用 TechLink Navigator 集中管理这些引用，避免文档正文中的链接随版本迭代而散落失效。

2. **赛事数据汇总页生成**：对于需要汇总多个数据源（如比分网站、排名页面）的体育数据分析团队，可将所有原始链接录入系统，自动生成可公开访问的数据聚合导航页。

3. **内部研发资源门户**：企业研发部门可将常用的内部工具地址、代码仓库镜像、CI/CD 控制台链接统一托管，配合健康检查功能及时发现不可用服务。

4. **开源项目的贡献者入口**：开源项目维护者将社区常用的资源链接（如 issue 追踪、邮件列表、会议记录）整理为导航模块，降低新贡献者的学习成本。

5. **个人知识库外链备份**：技术博主或研究员可定期将自己的收藏夹导出为 TechLink Navigator 数据集，避免浏览器书签丢失，并方便跨设备同步。

## 快速开始

以下步骤适用于 Linux / macOS / Windows（WSL 或 Git Bash）环境。

```bash
# 1. 克隆仓库
git clone https://github.com/techlink-navigator/navigator-core.git
cd navigator-core

# 2. 安装依赖（推荐使用 Python 3.10+ 虚拟环境）
python -m venv venv
source venv/bin/activate  # Windows: venv\Scripts\activate
pip install -r requirements.txt

# 3. 运行初始化引导，导入示例资源
python cli.py init --sample
python cli.py import --file samples/urls.txt --category sports

# 4. 生成静态导航页面（输出到 dist/ 目录）
python cli.py build --output dist --format markdown

# 5. 启动本地预览服务（可选）
python -m http.server 8000 --directory dist
```

完成上述步骤后，打开浏览器访问 `http://localhost:8000` 即可查看生成的导航页。

## 安装要求

| 依赖 | 必需版本 | 说明 |
|---|---|---|
| Python | 3.10 及以上 | 核心解释器，低于 3.10 会导致类型注解解析失败 |
| pip | 22.0 及以上 | 包管理工具，用于安装 requirements.txt 中列出的依赖 |
| Git | 2.25 及以上 | 用于克隆仓库及版本管理操作 |
| 网络连接 | 任意 | 用于链接健康检查及首次安装时下载依赖包 |
| 终端环境 | 支持 ANSI 转义 | CLI 彩色输出需要，Windows 建议使用 Windows Terminal |
| 磁盘空间 | 至少 50 MB | 包含代码、依赖及生成的输出文件 |
| 内存 | 256 MB 以上 | 健康检查并发扫描时建议 512 MB |

## 文档导航

| 层面 | 目录 | 回答的问题 |
|---|---|---|
| 用户手册 | docs/user-guide/ | 如何安装、配置、导入链接、生成输出、自定义模板 |
| 运维指南 | docs/ops/ | 如何部署到服务器、设置定时健康检查、备份数据文件 |
| 开发者文档 | docs/developer/ | 模块架构说明、插件编写方法、API 接口定义 |
| 设计决策 | docs/design/ | 为何选择 SQLite 作为存储、为何不内置浏览器渲染引擎 |
| 常见操作 | docs/recipes/ | 如何迁移旧书签数据、如何与 CI/CD 集成自动生成导航 |
| 故障排查 | docs/troubleshooting/ | 健康检查超时、模板渲染错误、字符编码问题的解决方案 |

## 资源列表

本部分按类别整理用户提供的原始链接数据，所有链接均保持原样输出，未做任何格式转换或补全。

数据来源类别：赛事比分与排名参考站点

<code>bajiajishibifen.net.cn</code>

<code>ajiajifenbang.net.cn</code>

<code>fajiabisaijieguo.net.cn</code>

<code>nuochaobifen.net.cn</code>

<code>dejiabifen.net.cn</code>

<code>dejiabisaijieguo.net.cn</code>

<code>dejiajishibifen.com.cn</code>

## 项目结构

```
navigator-core/
├── cli.py                      # 命令行入口，解析子命令并调度
├── requirements.txt            # 生产依赖清单（Flask, requests, Jinja2）
├── dev-requirements.txt        # 开发依赖（pytest, black, mypy）
├── README.md                   # 项目主文档
├── LICENSE                     # MIT 许可证文本
├── .gitignore                  # Git 忽略规则
├── config/
│   ├── default.yaml            # 默认配置（输出目录、健康检查间隔）
│   └── schema.json             # 链接条目 JSON Schema 定义
├── src/
│   ├── __init__.py
│   ├── core/
│   │   ├── engine.py           # 核心数据引擎，管理内存缓存与持久化
│   │   ├── link.py             # Link 数据类定义与状态枚举
│   │   └── checker.py          # 异步 HTTP 健康检查器
│   ├── cli/
│   │   ├── commands.py         # 各子命令实现（import, build, check）
│   │   └── formatter.py        # 终端输出格式化工具
│   └── exporters/
│       ├── markdown.py         # Markdown 表格导出器
│       ├── json_exporter.py    # JSON API 数据导出
│       └── html.py             # 简易 HTML 目录树生成器
├── templates/
│   ├── default.md.j2           # 默认 Markdown 导航模板
│   └── compact.html.j2         # 紧凑型 HTML 模板
├── samples/
│   ├── urls.txt                # 示例链接列表（含注释行）
│   └── categories.yaml         # 示例分类映射
├── tests/
│   ├── unit/
│   │   ├── test_link.py        # Link 类单元测试
│   │   └── test_checker.py     # 健康检查模块测试
│   └── integration/
│       └── test_cli_flow.py    # 端到端 CLI 命令测试
└── dist/                       # 默认输出目录（构建后生成，不提交至 Git）
```

## 贡献指南

1. **Fork 仓库并创建功能分支**：从主仓库 fork 到个人账户，然后基于 `main` 分支新建 `feature/your-feature-name` 分支，避免直接向主分支提交。

2. **编写或修改代码并确保测试通过**：所有新增功能需在 `tests/` 下补充对应单元测试或集成测试，运行 `pytest` 确认全部测试用例通过，且覆盖率不低于 85%。

3. **遵循代码风格规范**：使用 `black` 与 `isort` 进行自动格式化，提交前运行 `make lint`（若未安装 make，可手动执行 `black . && isort .`），确保无格式警告。

4. **更新文档与示例**：若改动涉及用户可见行为（如新增命令参数、修改配置文件字段），需同步更新 `README.md`、`docs/user-guide/` 对应章节，并在 `samples/` 中添加示例。

5. **提交 Pull Request 并描述变更**：推送分支至个人远程仓库后，向主仓库发起 Pull Request。PR 标题应简明扼要，正文需包含变更动机、实现方式、测试结果以及是否影响向后兼容性。

## 常见问题

**Q：导入大量链接（超过 1000 条）时性能如何？**

A：当前存储层使用 SQLite 内存模式配合定期持久化，批量导入 1000 条链接（每条含标签与描述）耗时约 1.2 秒。健康检查采用异步并发（默认 10 个 worker），1000 条链接首次检查约需 15-30 秒，具体取决于网络质量。若需优化，可调整 `config/default.yaml` 中的 `checker_workers` 参数。

**Q：是否支持链接的模糊搜索或正则筛选？**

A：CLI 命令 `search` 支持按域名、描述、标签进行子串匹配（不区分大小写）。若需正则表达式筛选，可使用 `export --filter "regex"` 配合 `--format json` 输出后，通过 `jq` 等工具二次过滤。后续版本计划加入原生正则搜索支持。

**Q：如何迁移已有浏览器书签或 Pocket 收藏夹？**

A：项目提供独立的迁移脚本 `tools/migrate_from_html.py`，支持解析 Chrome / Firefox 导出的 `bookmarks.html` 文件，以及 Pocket 的 CSV 导出格式。运行 `python tools/migrate_from_html.py --input bookmarks.html --output import.json` 生成中间文件，再使用 `cli.py import --file import.json` 导入。详细步骤见 `docs/recipes/migrate-bookmarks.md`。

## 许可证

MIT

> 外链数量: 7 | 生成时间: 2026-07-22 11:10:39
