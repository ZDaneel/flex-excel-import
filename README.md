# flex-excel-import

> Config-driven Excel import engine for Spring Boot, with an Angular admin UI.

[![Build](https://github.com/ZDaneel/flex-excel-import/actions/workflows/ci.yml/badge.svg)](https://github.com/ZDaneel/flex-excel-import/actions)
![Java](https://img.shields.io/badge/java-21-orange)
![Spring Boot](https://img.shields.io/badge/spring--boot-3.3-brightgreen)
![Angular](https://img.shields.io/badge/angular-18-red)
![License](https://img.shields.io/badge/license-MIT-blue)

**Status:** 🚧 Work in progress — targeting a usable MVP by the end of month.

---

## The Problem

Enterprise backends spend a disproportionate amount of time writing one-off Excel importers. Every time the business team tweaks a column — a new field, a reordered header, a different date format — a developer has to touch code, rebuild, and redeploy. Existing libraries (Apache POI, EasyExcel, Hutool) are **code-first**: each Excel format needs a dedicated annotated class.

## The Solution

`flex-excel-import` flips the model to **config-first**: the parser is driven by a declarative schema (YAML or DB-backed), so business users can remap columns through a UI without a release. Developers define *how* rows are handled; configuration defines *which* columns feed *which* fields.

```yaml
import-profile:
  name: clean-report
  sheet: 0
  header-row: 2
  columns:
    - excel: "Product Code"
      field: productCode
      required: true
    - excel: "Arrival Date"
      field: arrivalDate
      type: date
      format: "yyyy-MM-dd"
  validators:
    - type: unique
      field: productCode
```

## Non-Goals

- **Not a BI tool.** No dashboards, no analytics — pure ingestion.
- **Not a workflow engine.** Rows are processed and handed off; downstream flow is the caller's concern.
- **Not a generic ETL framework.** Excel-specific, opinionated about spreadsheet quirks (merged cells, formula cells, multi-row headers).

## Architecture

See [`docs/architecture.puml`](docs/architecture.puml) and the ADR log in [`docs/adr/`](docs/adr/).

Three-module Maven backend:

- `core` — parser, validator, SPI contracts. **Zero Spring dependency**, pure Java 21.
- `starter` — Spring Boot auto-configuration, binds config properties, registers beans.
- `web` — demo REST app showing end-to-end usage.

Angular 18 frontend (standalone components + Signals) in `frontend/`.

## Quickstart

```bash
# Backend
cd backend && ./mvnw spring-boot:run -pl web

# Frontend
cd frontend && npm install && npm start
```

Open http://localhost:4200 and drop an Excel file onto the upload zone.

## Trade-offs

Documented in [`docs/adr/`](docs/adr/):

- **ADR-0001** — Why ADRs at all
- **ADR-0002** — Config-first over annotation-first _(coming)_
- **ADR-0003** — Why Apache POI over EasyExcel _(coming)_
- **ADR-0004** — Keep `core` free of Spring _(coming)_

## License

MIT — see [LICENSE](LICENSE).
