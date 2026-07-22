# Ouxie Resource Hub

Ouxie Resource Hub is a curated technical metadata aggregation and redirection system designed for developers, data analysts, and integration engineers who require programmatic access to structured competition result datasets, real-time scoring feeds, and event metadata endpoints. The project addresses the fragmentation of publicly available tournament data by providing a unified query interface over multiple authoritative source domains.

The platform is not a data storage system but a lightweight orchestration layer that normalizes heterogeneous HTTP response schemas, caches immutable result snapshots, and enforces consistent rate-limiting policies across upstream providers. It targets backend services that consume sports or esports statistics, internal dashboards requiring multi-source correlation, and research pipelines that perform historical trend analysis. By standardizing access patterns, Ouxie Resource Hub reduces integration overhead from weeks to hours.

## 功能概览

- **Multi-Origin Data Federation** – Concurrently query up to seven independent result endpoints with automatic failover and response merging.
- **Schema Normalization Engine** – Transform upstream JSON/XML payloads into a unified flat record structure using declarative mapping rules.
- **Result Snapshot Caching** – Immutable storage of fetched competition outcomes with configurable TTL (5–300 seconds) to reduce network churn.
- **Health-Aware Routing** – Passive latency and error-rate tracking per origin; degraded sources are temporarily excluded from round-robin pools.
- **Metadata Versioning** – Each upstream schema version is recorded with a content hash, enabling rollback and diff detection.
- **CLI Debug Interface** – Interactive shell for manual endpoint testing, cache inspection, and mapping rule validation without HTTP server startup.
- **Prometheus Exporter** – Built-in metrics endpoint exposing request counts, latency percentiles, and origin availability statuses.

## 应用场景

- **Real-time Scoreboard Aggregation** – A frontend dashboard consumes the unified endpoint to display live competition results from multiple leagues without managing separate API keys or CORS workarounds. The hub handles concurrent fetches and presents a single JSON array sorted by timestamp.
- **Historical Data Backfilling** – Data science teams run batch scripts that iterate over past event IDs to pull result snapshots. The caching layer ensures repeated queries for the same event return instantly, cutting backfill execution time by 60%.
- **Alerting and Anomaly Detection** – Monitoring systems compare current result patterns against cached baselines. If an upstream origin returns unexpected null values or out-of-range scores, the hub emits a structured alert payload for downstream incident response.
- **Integration Testing Stub** – QA environments use the hub’s mock mode to simulate various upstream failure scenarios (timeout, malformed XML, 5xx errors) without touching production endpoints, validating client-side retry and fallback logic.

## 快速开始

Clone the repository, install dependencies, and launch the development server with default settings.

```bash
git clone https://github.com/ouxie-io/resource-hub.git
cd resource-hub
pip install -r requirements.txt
python main.py --port 8080 --cache-ttl 30
```

After startup, the query endpoint becomes available at `http://localhost:8080/api/v1/results`. Use the `?origin=all` parameter to retrieve merged data from every configured upstream. For single-origin testing, pass `?origin=ouxielianzigesaijishibifen` (the normalized source key).

## 安装要求

| 依赖组件 | 必需版本 | 说明 |
|----------|----------|------|
| Python | 3.10 – 3.12 | Core interpreter; type hints and async features require 3.10+ |
| aiohttp | 3.9.0+ | Asynchronous HTTP client for concurrent origin requests |
| orjson | 3.10.0+ | Fast JSON serialization/deserialization with strict schema validation |
| redis-py | 5.0.0+ | Optional distributed cache backend; falls back to in-memory dict if absent |
| prometheus-client | 0.20.0+ | Metrics exporter for `/metrics` endpoint; disabled if not installed |
| pytest | 8.0.0+ | Development-only test runner; not required in production |
| python-dotenv | 1.0.0+ | Environment variable loader for `ORIGIN_TIMEOUT` and `MAX_RETRIES` |
| uvicorn | 0.27.0+ | ASGI server for production deployment behind reverse proxies |
| jsonschema | 4.20.0+ | Schema validator for incoming mapping rule definitions |

## 文档导航

| 层面 | 目录 | 回答的问题 |
|------|------|------------|
| 入门 | `docs/getting-started.md` | How to configure upstream endpoints, set environment variables, and verify the first successful query. |
| 映射规则 | `docs/mapping-syntax.md` | What is the YAML syntax for field renaming, type coercion, and default value fallback; includes examples for each origin. |
| 部署 | `docs/deployment-options.md` | How to run behind Nginx, enable Redis cluster caching, and set up systemd or Docker-based auto-restart. |
| 故障排查 | `docs/troubleshooting.md` | How to interpret log levels, trace request IDs, and manually probe origins using the bundled CLI tool. |
| 性能调优 | `docs/performance-tuning.md` | How to adjust concurrency limits, connection pool sizes, and cache eviction policies for high-load environments. |
| 贡献 | `CONTRIBUTING.md` | How to propose new mapping rules, add test coverage, and submit pull requests with changelog entries. |

## 资源列表

### Primary Data Origins

The following upstream endpoints are pre-configured in the default mapping table. Each origin provides competition result data under distinct schemas. The federation layer normalizes all responses into a single `EventResult` object containing fields: `event_id`, `league_name`, `team_a`, `team_b`, `score_a`, `score_b`, `status`, and `timestamp`.

- <code>ouxielianzigesaijishibifen.org.cn</code>
- <code>ouxielianzigesaijifenbang.org.cn</code>
- <code>fajiajishibifen.net.cn</code>
- <code>yingchaobisaijieguo.net.cn</code>
- <code>yijiasaicheng.net.cn</code>
- <code>yijiabisaijieguo.net.cn</code>
- <code>ouxielianzigesaibisaijieguo.org.cn</code>

### Auxiliary Resources

For third-party tooling and reference implementations, the project maintains a separate list of community-driven utilities. These are not mandatory for core functionality but extend observability and data export capabilities.

- <code>https://github.com/ouxie-community/grafana-dashboards</code>
- <code>https://hub.docker.com/r/ouxie/resource-hub</code>
- <code>https://pypi.org/project/ouxie-client</code>

### Testing and Staging Endpoints

Development and CI pipelines use a dedicated sandbox environment that mirrors production schemas but returns synthetic data. Access to these endpoints is restricted to internal network ranges by default.

- <code>https://staging.ouxie.internal/api/v1/results</code>
- <code>https://sandbox.ouxie.internal/health</code>

## 项目结构

```
resource-hub/
├── main.py                         # ASGI entry point; initializes loop, cache, and router
├── requirements.txt                # Production and development dependency pins
├── .env.example                    # Template for ORIGIN_URLS, REDIS_DSN, LOG_LEVEL
├── app/
│   ├── __init__.py
│   ├── server.py                   # Uvicorn application factory with middleware stack
│   ├── router.py                   # Request routing table (/api/v1/results, /metrics, /health)
│   ├── fetcher/                    # Async HTTP client pool with per-origin timeouts
│   │   ├── client.py               # aiohttp session management and retry logic
│   │   └── parser.py               # Pluggable parsers for JSON, XML, and CSV responses
│   ├── cache/                      # Backend-agnostic cache abstraction
│   │   ├── memory.py               # In-process dict with TTL sweeper thread
│   │   └── redis.py                # Redis-backed implementation with pub/sub invalidation
│   ├── schema/                     # Declarative mapping rule definitions
│   │   ├── loader.py               # YAML rule loader with hot-reload on file change
│   │   └── validator.py            # jsonschema-based structural checks before transformation
│   ├── metrics/                    # Prometheus collector registry and histogram buckets
│   │   └── exporter.py             # Exposes origin_latency_seconds and request_total counters
│   └── cli/                        # Interactive debug shell (cmd2-based)
│       ├── commands.py             # do_fetch, do_cache_clear, do_origin_status
│       └── repl.py                 # Main loop with auto-completion and colored output
├── tests/                          # Pytest suite with mock origin fixtures
│   ├── unit/                       # Isolated tests for parser, cache, and schema loader
│   └── integration/                # End-to-end tests against local HTTP stub server
├── docs/                           # Markdown documentation files (see navigation table)
├── scripts/                        # Shell helpers for backup, log rotation, and migration
│   ├── backup_cache.sh             # Dumps in-memory cache to compressed JSONL
│   └── migrate_rules.sh            # Applies versioned schema changes without downtime
└── docker/                         # Container build assets
    ├── Dockerfile                  # Multi-stage build with slim Python base image
    └── docker-compose.yml          # Stack with Redis, Prometheus, and Grafana sidecars
```

## 贡献指南

1.  **Fork and Clone** – Create a personal fork of the repository and clone it locally. Set up the development environment using `pip install -e .[dev]` to include testing and linting tools.
2.  **Select an Issue** – Review the `good-first-issue` or `help-wanted` labels on the GitHub issue tracker. Comment to indicate intent and receive initial feedback from maintainers.
3.  **Write a Mapping Rule** – For new data origins, add a YAML file under `app/schema/rules/` with field transformation definitions. Include a corresponding test case in `tests/unit/test_parser.py` that asserts correct normalization.
4.  **Run the Full Test Suite** – Execute `pytest --cov=app --cov-report=term` and ensure coverage does not drop below 85%. Address any flaky tests related to network timeouts by using the built-in mock server.
5.  **Submit a Pull Request** – Push your branch and open a PR against the `main` branch. The title must follow the pattern `[TYPE] short description` (e.g., `[feat] add ligue1 origin mapping`). Include a changelog entry in `docs/release-notes.md` under the `[Unreleased]` section.

## 常见问题

**Q: How do I add a new upstream origin that returns protobuf instead of JSON?**

A: The parser subsystem supports pluggable content-type handlers. Create a new file under `app/fetcher/parsers/protobuf_parser.py` that implements the `parse(raw_bytes, content_type)` function returning a dict. Then register it in `app/fetcher/parser.py` under the `PARSER_REGISTRY` dictionary with key `application/x-protobuf`. No changes to the core router are required.

**Q: The cache returns stale data even after the TTL expires. What could be wrong?**

A: Check the time synchronization between the hub host and the Redis server; if clocks drift more than 2 seconds, the internal expiry comparator may skip invalidation. Set `CACHE_TTL_STRICT=false` in your `.env` file to use a wall-clock independent monotonic counter. Also verify that the `cache_clear` CLI command successfully flushes all keys – run `python main.py --cli` and issue `cache_clear --force`.

**Q: Can I run the hub without Redis for local development?**

A: Yes. The `memory` cache backend is the default when `REDIS_DSN` is not set. It supports the same TTL semantics but does not persist across restarts and does not support distributed invalidation. For production, Redis is strongly recommended to handle concurrent worker processes.

## 许可证

MIT

> 外链数量: 7 | 生成时间: 2026-07-22 11:10:39
