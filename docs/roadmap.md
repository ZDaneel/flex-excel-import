# Roadmap

三个周末做到一个可演示、可实际使用的小工具。定位是 **UI 优先的独立 Web 工具**，不是 Maven 库。核心流程打磨到位比功能面广更重要。

## 核心流程（整个项目围绕这一条路径）

> 上传 xlsx → 拖拽列映射 → preview diff → 一键导入 → 浏览/再导出

"导入"的落地走 **Writer 抽象**：引擎解析 + 校验，`Writer` 决定数据去哪。内置若干 Writer，用户在 profile 里下拉选，零代码即可使用。

| Writer | 用途 | 优先级 |
| --- | --- | --- |
| `download` | 清洗后直接下载 xlsx/CSV/JSON | W1 必做 |
| `internal-dataset` | 工具自带的表，UI 有"已导入数据"tab 可查询/再导出 | W1 必做（demo 闭环靠它） |
| `jdbc` | profile 填 JDBC URL + 目标表名，直接写进用户自己的库 | W3 有余力就做 |
| `webhook` | 每行 POST 到配置的 URL | 不做，写在 README 后续可能 |

## W1 — 后端骨架

**目标：** curl 能跑通「上传 → 预览 → 导入」三步。

- 单模块 Spring Boot 3.3 + Java 21（不做 core/starter/web 拆分）
- Apache POI 流式读取 .xlsx（SAX），处理合并单元格、空行、公式结果、日期
- Profile 存 Postgres，Flyway 建表
- `Writer` 接口 + 两个实现：`download`、`internal-dataset`
- REST：`POST /upload`、`POST /preview`（返回 insert/update/unchanged 三段 diff）、`POST /commit`、`GET /datasets/{id}/rows`
- Testcontainers 跑 Postgres 集成测试
- 核心单元测试：空文件、空行、合并表头、损坏 xlsx、日期/数值格式

**Deliverable：** `docker compose up` 起 Postgres，`./mvnw spring-boot:run`，README 里一段 curl 走通全链路。

## W2 — Angular UI

**目标：** UI 独立跑完核心流程，不再依赖 curl。

- Angular 18 standalone components，strict TS，Signals 管状态
- 文件拖拽上传
- **列映射画面**：左边 Excel 表头预览，右边目标字段，拖拽连线 + 字符串相似度自动匹配建议
- **Diff 预览表格**：insert / update / unchanged 三段 tab 切换，cell 级错误红框 + hover 看原因
- SSE 流式进度条
- "已导入数据"浏览 tab：按 profile 列表查询、搜索、按列排序、一键导出 CSV/xlsx
- 中英文 i18n（去掉德语）

**Deliverable：** `npm start` 对接 W1 后端，全流程可操作。

## W3 — 部署与文档

**目标：** 项目可以在外部环境运行，文档足以让人独立理解和使用。

- Dockerfile + docker-compose 一行起本地
- 部署到 Fly.io / Railway 免费档
- GitHub Actions：build + test + coverage badge
- README：
  - 项目背景与适用场景
  - 快速开始（本地起服务 + 一次完整流程的 GIF）
  - 关键截图：列映射、diff preview、数据浏览
  - 在线环境地址
- 架构图：一张 C4 Component view
- 3–4 条 ADR：为什么 config-first、为什么 POI 流式、为什么 diff preview 模式、为什么不做成 Maven 库
- 有余力：实现 `jdbc` writer，README 补一段"接入你自己的 Postgres"示例

**Deliverable：** 在线环境可访问，README 能脱离仓库作者独立看懂。

## 明确不做

和定位冲突、投入产出比差的事：

- Maven Central 发布、release-please、SemVer 自动化 —— 当前定位不是对外发布的 Maven 库
- 德语 i18n —— 英/中文足够
- ArchUnit 架构守护 —— 单模块不需要
- 多模块 core/starter/web 拆分 —— 单模块足够
- RowHandler SPI 作为对外扩展点 —— 用户不写 Java，Writer 内部接口私有即可
- 失败行"下载纠错模板再上传"回路 —— UI 里直接改更顺
- 多 sheet 关联、profile 版本化、多租户 —— 写入 README "后续可能"，不实现
- 非 .xlsx 格式 —— CSV / .xls 都不做
