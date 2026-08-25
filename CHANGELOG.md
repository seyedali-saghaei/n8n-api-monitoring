# Changelog

All notable changes to this project are documented in this file.

## [Unreleased]

### Planned

- Support multiple APIs from a shared configuration source.
- Replace workflow static data with an external state store when horizontal scaling is required.
- Add dashboards and retention policies for monitoring logs.

## [0.1.0] - 2026-08-25

### Added

- Scheduled and manual API health checks.
- HTTP status, response-time and expected-content validation.
- Bounded retry handling with configurable delay.
- `UP`, `DEGRADED`, `DOWN` and `UNKNOWN` status classification.
- State-change detection with incident, performance and recovery alerts.
- Structured logging in an n8n Data Table.

