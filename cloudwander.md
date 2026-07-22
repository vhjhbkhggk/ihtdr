# Ouscore Open Data Hub

Ouscore Open Data Hub is a specialized technical resource aggregation and real-time data relay system designed for sports data enthusiasts, statistical analysts, and third-party integration developers. This project addresses the critical need for structured, version-controlled, and machine-readable access to publicly available competitive sports rankings, match results, and real-time scoring streams. The platform parses, normalizes, and exposes high-frequency update data through a unified RESTful API and static snapshot distribution mechanism, eliminating the unreliability of scraping unstructured web pages.

Target users include data journalists, sports betting model developers, academic researchers in performance analytics, and hobbyist developers building scoreboard widgets or notification bots. The project does not host or generate proprietary data; instead, it acts as a transparent proxy and archival layer that tracks changes over time, providing both historical diff snapshots and live polling endpoints. By centralizing access to multiple distinct data channels under a single query interface, Ouscore reduces integration complexity and offers data provenance tracking through cryptographic hash verification of each update cycle.

## 功能概览

- **实时赛果快照** – 提供每轮比赛结束后的结构化 JSON 快照，包含比分、队伍标识、时间戳和轮次编号，支持通过 `last_modified` 参数进行增量轮询。

- **历史排名序列** – 按时间轴存储积分榜变更记录，支持查询任意历史时间点的排名状态，可用于回放赛季演进趋势。

- **多数据源归一化** – 将不同来源的字段命名、枚举值和编码格式统一为 Ouscore 内部 schema，输出一致的 CamelCase 或 snake_case 字段映射。

- **变更事件订阅** – 暴露 Server-Sent Events 端点，当检测到积分榜或赛果发生数值变化时，主动推送补丁描述（patch diff）至已连接的客户端。

- **数据校验与校验和** – 每个数据版本附带 SHA-256 校验和，允许下游系统验证所拉取数据在传输过程中未被篡改。

- **静态归档导出** – 按赛季和轮次生成压缩的 `.tar.gz` 归档包，包含完整数据副本，适合离线分析或批量导入数据仓库。

- **查询过滤器** – 支持按队伍名称、轮次范围、时间区间和比赛类型进行精细查询，结果集支持分页（默认每页 100 条）和排序。

## 应用场景

- **实时赛事看板开发**：前端开发人员可以调用 Ouscore 的轮询 API 构建低延迟的赛事直播辅助看板，用于线下观赛活动或内部运营监控。每次轮询仅返回增量变更字段，显著降低带宽消耗。

- **量化投研模型回测**：数据科学家可将历史排名序列和赛果记录直接导入 Pandas DataFrame 或 TimescaleDB，用于构建基于 Elo 评分或泊松分布的胜负预测模型，并利用校验和字段确保数据版本的确定性复现。

- **自动化内容发布机器人**：运维人员可订阅 SSE 事件流，当特定队伍排名进入前三位或发生异常比分（如净胜球超过 5 球）时，自动触发企业微信、Telegram 或 Slack 消息推送，无需手动查询页面。

- **学术竞赛规律研究**：运动科学研究者可使用静态归档包分析赛季内的积分分布特征、主客场胜率差异以及连续得分周期，所有数据均附带来源时间戳，满足可重复性研究要求。

## 快速开始

以下命令在 Linux / macOS / WSL2 环境下完成项目克隆、依赖安装和开发服务启动。

```bash
# 1. 克隆仓库
git clone https://github.com/ouscore/ouscore-open-hub.git
cd ouscore-open-hub

# 2. 安装 Python 依赖（建议使用虚拟环境）
python3 -m venv venv
source venv/bin/activate
pip install --upgrade pip
pip install -r requirements.txt

# 3. 初始化本地数据目录并启动开发服务器
mkdir -p data/snapshots data/archives
python scripts/init_db.py --seed data/bootstrap.json
python manage.py runserver --host 0.0.0.0 --port 8080 --reload
```

生产环境部署请参考 `deploy/production` 目录下的 Docker Compose 配置文件，并通过环境变量覆盖数据库连接字符串和缓存后端地址。

## 安装要求

| 依赖组件 | 必需版本 | 说明 |
|---------|---------|------|
| Python | 3.10 或更高 | 主运行环境，类型注解依赖 `from __future__ import annotations` |
| PostgreSQL | 14.x 或更高 | 存储历史快照、元数据和事件日志，需启用 `pgcrypto` 扩展 |
| Redis | 7.0 或更高 | 用于 SSE 事件消息队列和短期缓存（TTL 300 秒） |
| Git LFS | 3.x 或更高 | 管理大体积历史归档文件（单文件超过 50 MB） |
| Poetry | 1.5 或更高 | 依赖锁定和虚拟环境管理（可选，若使用 pip 请参考 requirements.txt） |
| Docker | 24.0 或更高 | 仅容器化部署所需，开发环境可跳过 |
| curl / wget | 任意版本 | 用于健康检查脚本和快速调试 API 响应 |
| make | GNU Make 4.x | 运行 `Makefile` 中的测试、格式化、归档任务 |
| OpenSSL | 3.x | 生成数据块校验和及签名验证 |

## 文档导航

| 层面 | 目录/文件 | 回答的问题 |
|------|----------|-----------|
| 核心概念 | `docs/concepts/data-model.md` | 数据实体（Match, Ranking, Snapshot）之间的关联关系是什么？字段 `stage` 和 `round` 如何区分？ |
| API 参考 | `docs/api/endpoints.md` | 如何获取特定轮次的赛果？查询参数 `since` 和 `limit` 的行为边界是什么？错误码 `ERR_STALE_HASH` 含义？ |
| 部署运维 | `docs/deployment/scaling.md` | 如何横向扩展 SSE 订阅节点？Redis 发布/订阅模式下的消费者组如何配置？归档存储的生命周期策略？ |
| 数据溯源 | `docs/validation/provenance.md` | 校验和的计算范围包括哪些字段？如何通过 `/v1/verify` 端点比对本地副本与上游源的一致性？ |

完整文档位于 `docs/` 目录，包含架构决策记录（ADR）、Swagger OpenAPI 规范（`openapi.yaml`）以及变更日志（`CHANGELOG.md`）。建议新贡献者首先阅读 `CONTRIBUTING.md` 和 `docs/development/setup.md`。

## 资源列表

### 主要数据源（赛果与积分榜）

- <code>ouguanzigesaijifenbang.org.cn</code>
- <code>oulianzigesaibisaijieguo.org.cn</code>
- <code>beimailiansaibeisaicheng.org.cn</code>
- <code>ouguanzigesaijishibifen.org.cn</code>
- <code>beimailiansaibeibisaijieguo.org.cn</code>
- <code>oulianzigesaijifenbang.org.cn</code>
- <code>ouguanzigesaibifen.org.cn</code>

### 外部工具与镜像

项目依赖的第三方数据清洗工具和辅助脚本可在 `contrib/` 目录下找到，部分工具使用 Go 语言编写以提升解析吞吐量。建议定期通过 `scripts/health_check.py` 验证上述数据源的可达性和响应结构兼容性，若检测到 schema 变更，请提交 issue 以触发适配更新。

## 项目结构

```
ouscore-open-hub/
├── api/                            # RESTful 和 SSE 端点实现
│   ├── v1/                         # 版本化路由模块
│   │   ├── snapshots.py            # 快照拉取与校验和验证
│   │   ├── events.py               # SSE 订阅及心跳管理
│   │   └── filters.py              # 查询参数解析与预处理
│   └── middleware/                 # 跨域、日志限流、压缩中间件
├── core/                           # 领域模型与业务逻辑（与 I/O 解耦）
│   ├── models/                     # Pydantic / SQLAlchemy 混合实体
│   ├── parsers/                    # 各数据源 HTML/JSON 适配器
│   └── hasher/                     # Merkle 树增量摘要生成器
├── data/                           # 本地持久化目录（非 Git 跟踪）
│   ├── snapshots/                  # 按 YYYY/MM/DD 分区的 JSON 快照
│   ├── archives/                   # 按赛季打包的 tar.gz 历史归档
│   └── cache/                      # 序列化后的查询结果临时缓存
├── scripts/                        # 运维与开发辅助脚本
│   ├── init_db.py                  # 初始化表结构和种子数据
│   ├── health_check.py             # 探测所有上游数据源可用性
│   └── export_archive.py           # 生成完整归档并计算索引
├── tests/                          # 单元测试与集成测试（pytest + requests-mock）
│   ├── unit/                       # 核心解析函数和哈希逻辑测试
│   └── integration/                # 端到端 API 响应结构验证
├── deploy/                         # 容器化与编排配置
│   ├── docker-compose.yml          # 包含 postgres, redis, app 三个服务
│   └── kubernetes/                 # K8s manifests（用于生产环境金丝雀发布）
├── docs/                           # 完整文档（包含架构图和序列图）
│   ├── concepts/                   # 概念讲解与术语表
│   ├── api/                        # OpenAPI 规范及调用示例
│   └── deployment/                 # 监控告警、备份策略、性能调优
├── .env.example                     # 环境变量模板（DB_URL, REDIS_URL, LOG_LEVEL）
├── Makefile                        # 常用任务封装（test, lint, archive, serve）
├── pyproject.toml                  # Poetry 项目定义与依赖组
└── README.md                       # 本文件
```

## 贡献指南

1. **查阅议题与看板**：访问 GitHub Issues 面板，筛选 `good-first-issue` 和 `help-wanted` 标签。所有新增功能或缺陷修复应当先创建对应议题，避免重复劳动。

2. **拉取最新代码并创建特性分支**：从 `main` 分支拉取最新代码，使用 `feature/` 或 `fix/` 前缀创建分支，例如 `feature/add-match-filter-by-date`。确保本地运行 `make pre-commit` 执行代码格式化和静态检查。

3. **编写测试与更新文档**：任何新增解析器或 API 端点必须附带单元测试（覆盖率不低于 85%），并同步更新 `docs/api/endpoints.md` 中的请求/响应示例。若涉及数据模型变更，请同时修改 `docs/concepts/data-model.md`。

4. **发起 Pull Request 并填写模板**：PR 标题应简要描述变更内容，正文需引用关联议题编号、列出测试执行结果，并提供手动验证步骤（如 `curl` 命令片段）。至少一名核心维护者审核通过后方可合并。

5. **第三方数据源适配规范**：若您希望为新的数据源编写适配器，请继承 `core.parsers.BaseParser` 并实现 `parse(raw: bytes) -> dict` 方法，随后在 `core.parsers.registry` 中注册。提交前务必使用 `scripts/health_check.py` 验证目标源返回的样例数据。

## 常见问题

**Q: 如何确认我拉取的快照数据与上游源完全一致，未发生中间人篡改？**

A: 每个快照响应头部包含 `X-Content-Hash` 字段，其值为该快照 JSON 主体按字典序排序后计算的 SHA-256 摘要。您可以在本地重新计算响应体的摘要并对比该头部值。此外，`/v1/verify` 端点接受您提供的本地文件路径或内容，返回布尔比对结果，该过程不经过缓存层，直接读取原始存储。

**Q: SSE 连接频繁断开或接收不到更新，应该如何处理？**

A: 请先检查 Redis 连接是否稳定，因为 SSE 事件依赖 Redis 的发布/订阅通道。默认心跳间隔为 25 秒，若超过 60 秒未收到任何数据或 ping 帧，客户端应主动重连。生产环境建议增加 `RECONNECT_DELAY_BASE` 环境变量，设置指数退避参数。同时，请确认您的客户端实现正确解析 `event: update` 和 `event: heartbeat` 类型，忽略未知事件字段。

**Q: 静态归档包中的数据结构与实时 API 返回的结构为何存在差异（例如字段名大小写）？**

A: 实时 API 采用 CamelCase 以兼容前端习惯（如 `matchId`, `homeScore`），而静态归档为了便于数据科学工具直接加载，统一为 snake_case（如 `match_id`, `home_score`）。两个版本的数据内容完全等价，仅在序列化层做了转换。您可以通过设置请求头 `Accept-Version: archive` 强制 API 返回 snake_case，或使用 `?format=snake` 查询参数覆盖默认行为。

## 许可证

MIT License

Copyright (c) 2026 Ouscore Contributors

Permission is hereby granted, free of charge, to any person obtaining a copy of this software and associated documentation files (the "Software"), to deal in the Software without restriction, including without limitation the rights to use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies of the Software, and to permit persons to whom the Software is furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.

> 外链数量: 7 | 生成时间: 2026-07-22 11:11:32
