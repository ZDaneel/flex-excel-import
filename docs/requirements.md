# Requirements

Scope for the v0.1 release. Expanded as decisions solidify.

## Functional

### F-1 Profile definition

A **profile** describes how a specific Excel layout maps to a target domain object. A profile specifies:

- Sheet selection (by index or name)
- Header row (supports multi-row headers; the engine flattens or joins them)
- Columns: Excel header text → target field name, type, required flag, format hints
- Validators: per-field (required, regex, range) and cross-row (unique, sum check)
- Row handler reference (which SPI implementation consumes the parsed rows)

Profiles are declared in YAML on the classpath **or** stored in the database and edited through the UI.

### F-2 Upload & preview

The user uploads an `.xlsx` file and chooses a profile. The engine returns the first N rows parsed, highlighting any validation errors without side effects.

### F-3 Execute import

On confirmation the engine streams through the file, invoking the configured `RowHandler` for each valid row. Invalid rows are collected into a report, not silently skipped.

### F-4 Report & failed-row recovery

After an import, the user can:

- See counts: total, succeeded, failed, skipped
- Browse failed rows with the specific validation message per cell
- Download a corrected template (original file + an error column) to fix and re-upload

### F-5 Progress feedback

Imports longer than a few seconds stream progress to the UI via Server-Sent Events.

## Non-functional

### NF-1 Correctness over cleverness

The engine must handle real-world Excel quirks: merged cells, empty rows mid-file, formula cells (resolve to value), locale-specific number/date formats, trailing whitespace. Each edge case has a regression test.

### NF-2 Streaming, not loading

Files up to 200 MB must import without blowing the JVM heap. Apache POI's streaming (SAX) reader is the baseline.

### NF-3 Zero Spring in core

The `core` module compiles and tests without Spring on the classpath. Enforced by ArchUnit.

### NF-4 Test pyramid

- Unit tests on `core` with ≥80% line coverage
- Integration tests on `starter` + `web` running against real PostgreSQL via Testcontainers
- End-to-end UI tests via Playwright hitting a running backend

### NF-5 Observability

Structured logs (JSON) with per-import correlation ID. Metrics exposed via Micrometer (rows processed, failures, duration). No full observability stack in v0.1 — just the hooks.

### NF-6 Internationalization

UI strings in `en-US` and `de-DE`. Server-side error messages localized by `Accept-Language`.

## Assumptions

- Excel means `.xlsx` (OOXML). Legacy `.xls` out of scope.
- Target backend runs on Java 21+. No older-JDK compatibility shims.
- Single tenant; profiles are globally visible to all users.

## Open questions

Tracked as issues on GitHub, not frozen here.
