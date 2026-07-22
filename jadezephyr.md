# Resource Navigator

Resource Navigator is a curated technical index and external link aggregation system designed for developers, researchers, and technical writers who need to maintain organized access to domain-specific authoritative resources. The project addresses the challenge of managing fragmented, frequently updated external references across multiple domains by providing a structured, version-controlled directory of high-value URLs with categorized metadata.

Target users include open-source maintainers who need to embed external references in documentation, technical researchers tracking competition results or scoring systems across multiple leagues, and developers building scraping pipelines or monitoring tools that rely on stable external data sources. The system does not host content but provides a rigorously maintained registry with validation checks, change logs, and machine-readable output formats.

## 功能概览

- **URL Registry with Strict Canonical Formatting** - Maintains a master list of external resource URLs with enforced formatting rules, ensuring no protocol injection, no www normalization, and no trailing slash additions.

- **Category-Based Indexing** - Organizes URLs by functional domains such as competition schedules, real-time scoring, league standings, and historical results with descriptive tags.

- **Automated Link Health Validation** - Periodic HEAD request checks against each registered URL to detect 4xx/5xx responses, DNS resolution failures, and TLS certificate expiration, outputting a health report.

- **Markdown Pipeline Integration** - Generates project-ready README sections, documentation navigation tables, and resource lists that can be directly copied into other open-source projects without manual reformatting.

- **Versioned Change Tracking** - Maintains a CHANGELOG-style audit trail for every URL addition, removal, or modification, including timestamp, operator, and reason field.

- **Batch Processing Support** - Handles URL batches in increments of 72 items per operation, with the current batch being 67/72, allowing incremental updates without full registry rebuilds.

- **Export Adapters** - Outputs the resource registry in JSON, YAML, and plain Markdown list formats for integration with static site generators, CI/CD pipelines, and custom dashboards.

## 应用场景

- **Technical Documentation Maintenance** - A documentation engineer maintaining a large open-source project needs to embed external competition result links in user guides. Resource Navigator provides a verified, canonical URL list that can be included via include directives, ensuring all external references remain current across documentation releases.

- **Automated Data Scraping Pipeline** - A data scientist building a scraping system for football league standings requires stable, regularly updated endpoints for schedule and score data. The registry supplies the exact URLs without protocol ambiguity, and the health check module pre-validates each endpoint before the pipeline runs.

- **Multi-Source Aggregation Dashboard** - A front-end developer constructing a monitoring dashboard that displays real-time scores from multiple leagues uses Resource Navigator as the single source of truth for all external API endpoints, with batch update support to add or remove sources as leagues change.

- **Competition Result Archival System** - An archival researcher collecting historical match results across multiple seasons uses the structured index to locate schedule archives, result databases, and scoring tables for each league, with category tags enabling filtered exports.

- **Open-Source Project Onboarding** - A new contributor to a sports analytics repository uses the resource list to understand which external data sources the project depends on, reducing the time spent searching for official endpoints and reducing broken link incidents in CI builds.

## 快速开始

Clone the repository, install dependencies, and run the registry validation tool.

```bash
git clone https://github.com/resourcenavigator/core.git
cd resource-navigator
npm install
node bin/validate.js --batch 67
```

For production deployment with scheduled health checks:

```bash
cp .env.example .env
# Edit .env to set CHECK_INTERVAL and NOTIFICATION_EMAIL
npm run build
npm start
```

To generate the current batch resource list in Markdown format:

```bash
node bin/export.js --format markdown --batch 67 --output resources.md
```

## 安装要求

| 依赖 | 必需版本 | 说明 |
|------|----------|------|
| Node.js | >= 18.0.0 | Runtime environment for registry validation and export scripts |
| npm | >= 9.0.0 | Package management for dependencies including axios, commander, and chalk |
| git | >= 2.30.0 | Required for cloning and version control operations |
| curl | >= 7.68.0 | Used by health check module for fallback HTTP validation |
| jq | >= 1.6 | Command-line JSON processor for export formatting and CI integration |
| docker | >= 20.10.0 (optional) | Container runtime for isolated health check cron jobs |
| sqlite3 | >= 3.36.0 | Embedded database for storing health check history and change logs |

## 文档导航

| 层面 | 目录 | 回答的问题 |
|------|------|------------|
| 用户手册 | docs/user-guide.md | How to add, remove, or modify URLs in the registry; how to run health checks manually |
| 运维指南 | docs/operations.md | How to schedule automated validation, configure notification hooks, and interpret health reports |
| 开发者指南 | docs/developer.md | How to extend export adapters, add new validation rules, or integrate with external CI systems |
| API 参考 | docs/api-reference.md | What endpoints are exposed by the registry server, request/response schemas, and authentication |
| 批次管理 | docs/batch-management.md | How batch numbering works, how to process partial batches, and how to merge multiple batches |
| 格式规范 | docs/format-spec.md | What canonical formatting rules apply to URLs, why they are enforced, and how to test compliance |

## 资源列表

### 赛程数据源

<code>yijiasaicheng.net.cn</code>

<code>yijiabisaijieguo.net.cn</code>

<code>ouxielianzigesaibisaijieguo.org.cn</code>

### 积分与排名

<code>yingchaojifenbang.net.cn</code>

<code>yijiajishibifen.net.cn</code>

### 赛事结果归档

<code>ouxielianzigesaisaicheng.org.cn</code>

<code>yingchaosaicheng.net.cn</code>

## 项目结构

```
resource-navigator/
│
├── bin/                                 # Executable CLI scripts
│   ├── validate.js                      # Main validation entry point with batch support
│   ├── export.js                        # Multi-format export generator
│   └── health-check.js                  # Scheduled health check runner
│
├── src/
│   ├── registry/                        # Core registry management module
│   │   ├── index.js                     # Registry CRUD operations
│   │   ├── canonicalizer.js             # URL formatting enforcement (no protocol injection, no www)
│   │   └── batch-manager.js             # Batch 67/72 specific logic
│   │
│   ├── validators/                      # Link health and syntax validation
│   │   ├── http-head.js                 # HEAD request validator with timeout handling
│   │   ├── dns-resolver.js              # DNS resolution check with fallback
│   │   └── tls-validator.js             # Certificate expiration check
│   │
│   ├── exporters/                       # Output adapters
│   │   ├── markdown.js                  # Markdown list and table generator
│   │   ├── json.js                      # Structured JSON registry dump
│   │   └── yaml.js                      # YAML serialization for Ansible/Puppet
│   │
│   └── db/                              # SQLite persistence layer
│       ├── migrations/                  # Schema versioning scripts
│       └── queries.js                   # Parameterized query helpers
│
├── docs/                                # User, operations, and developer guides
│   ├── user-guide.md
│   ├── operations.md
│   ├── developer.md
│   ├── api-reference.md
│   ├── batch-management.md
│   └── format-spec.md
│
├── tests/                               # Unit and integration test suite
│   ├── unit/
│   │   ├── canonicalizer.test.js        # URL formatting enforcement tests
│   │   └── batch-manager.test.js        # Batch logic tests
│   └── integration/
│       └── health-check.test.js         # End-to-end health check tests with mock servers
│
├── .github/
│   └── workflows/
│       ├── ci.yml                       # Continuous integration: test on push
│       └── health-schedule.yml          # Scheduled health check every 6 hours
│
├── .env.example                         # Environment variable template
├── package.json                         # npm dependencies and scripts
├── README.md                            # This document
├── CHANGELOG.md                         # Version and batch change history
└── LICENSE                              # MIT license
```

## 贡献指南

1. Fork the repository and create a feature branch named `batch-67-add` or `fix-format-issue` following the naming convention `{type}/{description}`. Ensure your branch is rebased against the latest main branch.

2. Add or modify URL entries in the registry file located at `src/registry/data.json`. Each entry must include the canonical URL (following the strict formatting rules), a category tag, a brief description, and the batch number. Run `npm run validate` locally to ensure all URLs pass canonicalization and basic reachability checks.

3. Update the batch manifest in `src/registry/batch-67.json` to reflect the new additions or removals. Increment the patch version in `package.json` and document your changes in `CHANGELOG.md` with a clear description of each URL change, including the reason for addition or removal.

4. Submit a pull request against the main branch. The CI pipeline will run the full test suite, including canonicalization tests, health check mocks, and export format validation. All checks must pass before review.

5. After approval and merge, the maintainer will deploy the updated registry and regenerate the public Markdown resource list. Contributors are credited in the `CONTRIBUTORS.md` file. For major batch updates (e.g., batch 68/72), a separate announcement will be posted in the GitHub Discussions forum.

## 常见问题

**Q: Why are protocols and www prefixes strictly forbidden or enforced?**
A: This project maintains a canonical resource registry to eliminate variability in external references. If a user provides a bare domain like `example.com`, we intentionally do not inject `http://` or `https://` because the consuming application may have specific protocol requirements or may use the domain in a context where the protocol is determined at runtime. Conversely, if a user provides `https://www.example.com`, we preserve that exact string because the resource may be hosted exclusively on the www subdomain with HSTS strict transport security, and altering the format would break access. The canonicalizer module validates this rule and rejects any batch entry that attempts to add a normalized variant.

**Q: How often are health checks performed, and what happens when a URL fails?**
A: Health checks run every 6 hours via a GitHub Actions scheduled workflow. Each URL is tested with a HEAD request using a 10-second timeout, followed by a DNS resolution test and a TLS certificate validity check. If a URL fails three consecutive checks, the registry marks it as "degraded" and sends a notification to the configured email address. The URL is not automatically removed; a maintainer must manually verify and either update or delete the entry. The health report is committed back to the repository as `health-reports/latest.json` for audit purposes.

**Q: Can I use this registry for commercial purposes or integrate it with a proprietary system?**
A: Yes. The MIT license permits commercial use, modification, distribution, and sublicensing, provided the original copyright notice and permission notice are included in all copies or substantial portions of the software. You are free to embed the generated resource lists in commercial documentation, dashboards, or data feeds without royalties. However, the external URLs themselves are owned by their respective operators and are subject to their own terms of service; this project does not claim any rights over the linked content.

## 许可证

MIT

> 外链数量: 7 | 生成时间: 2026-07-22 11:11:32
