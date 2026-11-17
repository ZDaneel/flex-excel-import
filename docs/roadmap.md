# Roadmap

Four weekends to a demoable end-to-end MVP. Each milestone ends with something runnable and reviewable.

## W1 — Core parsing engine

**Goal:** given an Excel file and a profile, emit structured rows.

- Multi-module Maven skeleton (`core` / `starter` / `web`)
- Apache POI streaming reader (handles large files, merged cells, formula cells)
- Profile model (sheet index, header row, column list, type converters)
- SPI contracts: `RowHandler`, `Validator`, `TypeConverter`
- JUnit 5 tests covering: happy path, corrupt file, empty rows, merged headers, bad types
- CLI harness to run imports locally

**Deliverable:** `core` module published locally with ≥80% line coverage.

## W2 — Spring Boot starter + REST demo

**Goal:** a Spring Boot service that accepts uploads and runs the engine.

- `@EnableFlexImport` auto-configuration
- Profile storage: YAML on classpath **and** DB-backed (JDBC)
- REST surface: preview, execute, report, profile CRUD
- Flyway migrations for profile tables
- Testcontainers-based integration tests against real PostgreSQL
- ArchUnit tests (core has no Spring deps, controllers don't call repositories directly)
- OpenAPI spec committed under `docs/openapi.yaml`

**Deliverable:** `./mvnw spring-boot:run -pl web` + a working curl walkthrough in the README.

## W3 — Angular UI

**Goal:** a non-developer can run an import without touching the API.

- Angular 18 standalone components, strict TS, Signals for state
- File upload (drag & drop)
- Column mapping screen (auto-detect + manual override)
- Preview table with validation errors highlighted
- Server-Sent Events for import progress
- Download failed rows as a corrected template
- i18n for English and German

**Deliverable:** `npm start` serves a usable app against the W2 backend.

## W4 — Polish for public release

**Goal:** the repo reads like a production-ready reference, not a student project.

- README expanded with screenshots, architecture diagram (C4 component view)
- 3–5 ADRs covering the real decisions (config-first, POI vs. EasyExcel, core-free-of-Spring, Signals vs. NgRx)
- GitHub Actions: build, test, coverage badge, release-please for SemVer
- Dockerfile + docker-compose for local run
- Public demo deployment (Hetzner / Railway / Fly.io) linked from README
- `core` and `starter` published to Maven Central
- Conventional Commits enforced via commit-msg hook

**Deliverable:** version `0.1.0` tagged and released.

## Out of scope

Explicitly **not** doing these in v0.1:

- Non-Excel formats (CSV, JSON, XML). Possible later; separate module.
- Row-level workflow or approval. Caller's concern.
- Analytics or dashboards. Not a BI tool.
- Multi-tenant profile storage. Single tenant assumed until requested.
