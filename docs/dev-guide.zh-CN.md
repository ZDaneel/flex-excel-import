# 开发说明（中文）

本地开发速查，方便在不同设备上快速接上进度。英文文档是对外的，这份是写给自己的。

## 环境要求

| 工具      | 版本         | 安装                                      |
| --------- | ------------ | ----------------------------------------- |
| Java      | 21 (LTS)     | `brew install openjdk@21` 或 SDKMAN       |
| Maven     | 3.9+         | 项目里带 `./mvnw`，无需本机装             |
| Node.js   | 20 LTS       | `brew install node@20` 或 nvm             |
| Docker    | 最新稳定版   | Docker Desktop 或 OrbStack                |
| PostgreSQL | 16          | 走 docker-compose 起，不用本地装          |
| Angular CLI | 18+        | `npm i -g @angular/cli`（可选）           |

## 克隆 & 启动

```bash
# 首次克隆
git clone git@github.com:ZDaneel/flex-excel-import.git
cd flex-excel-import

# 后端（W2 之后可用）
cd backend && ./mvnw -pl web spring-boot:run

# 前端（W3 之后可用）
cd frontend && npm install && npm start
```

## 分支与提交

- 主干：`main`，所有改动走 PR
- 分支命名：
  - `feat/<短描述>` 新功能
  - `fix/<短描述>` bug
  - `docs/<短描述>` 纯文档
  - `chore/<短描述>` 构建/配置/杂项
- 提交信息走 [Conventional Commits](https://www.conventionalcommits.org/)：
  - `feat: add YAML profile loader`
  - `fix(core): handle merged header cells`
  - `docs: expand ADR-0003`
  - `test: cover corrupt-xlsx edge case`

## 当前进度

- [x] W0 仓库 scaffold（README / LICENSE / .gitignore / ADR-0001）
- [ ] W1 core 模块 + POI 解析 + SPI
- [ ] W2 starter + REST demo + Testcontainers
- [ ] W3 Angular UI + i18n
- [ ] W4 打磨 + 发布 0.1.0

## 下一步要做的事（换设备也能快速接上）

1. 建 Maven 多模块：`backend/pom.xml` 作为 parent，`core` / `starter` / `web` 作为子模块
2. `core` 先实现 `ExcelReader` + `ProfileModel` + 三个 SPI 接口
3. 写第一批单元测试：空文件、空行、合并单元格、损坏 xlsx、日期格式
4. 决策记录：ADR-0002（为什么 config-first）、ADR-0003（为什么选 POI）—— 在动手写代码前先写 ADR，避免事后补文档

## 常用命令

```bash
# 后端
./mvnw -pl core test                    # 只跑 core 模块测试
./mvnw -pl core -am install             # 装 core（依赖模块一起编译）
./mvnw verify                           # 全量跑测试 + ArchUnit
./mvnw spring-boot:run -pl web          # 起 demo 服务

# 前端
npm run lint                            # ESLint + Prettier
npm test                                # Jest
npm run e2e                             # Playwright
npm run build                           # 生产构建

# Docker
docker compose up -d postgres           # 只起数据库
docker compose up -d                    # 全起
docker compose logs -f web              # 跟踪后端日志
```

## 决策日志（ADR）

写在 `docs/adr/`，一个决策一个文件，编号顺序。模板很短：Context / Decision / Consequences。写 ADR 的时机是 **做决定之前**，不是事后补。

## 发布流程（W4 之后）

1. `./mvnw release:prepare release:perform`（走 Maven Central）
2. GitHub release tag `v0.1.x`
3. `release-please` 自动更新 CHANGELOG 和 bump 版本
