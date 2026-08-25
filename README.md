# n8n API Monitoring

A small, production-minded n8n portfolio project that monitors one REST API, classifies its health, retries bounded failures, prevents duplicate alerts and records normalized check results.

## Business Problem

An API returning a response is not necessarily healthy. A useful monitor must distinguish slow responses, invalid payloads, HTTP failures, timeouts and network errors. It should alert on meaningful state changes without sending the same incident message every few minutes.

## Solution

The workflow checks one configurable endpoint every five minutes and assigns one of four states:

- `UP`: valid 2xx response within the performance threshold.
- `DEGRADED`: valid response that exceeds the response-time threshold.
- `DOWN`: HTTP failure, invalid expected content, timeout or network error.
- `UNKNOWN`: response information is missing or cannot be classified safely.

It retries `DOWN` and `UNKNOWN` results up to a configurable maximum. Final results are compared with the previous state before alert routing.

## Features

- Manual and scheduled execution.
- Configurable endpoint, timeout and performance threshold.
- HTTP status and expected-content validation.
- Response-time measurement.
- Bounded retries with a fixed delay.
- Incident, performance warning and recovery webhooks.
- Duplicate-alert prevention through state-change detection.
- Structured logs in an n8n Data Table.
- No credentials or environment-specific IDs in the repository export.

## Repository Structure

```text
n8n-api-monitoring/
├── README.md
├── workflows/api-monitoring.json
├── examples/
├── docs/architecture.md
├── docs/testing.md
├── docker-compose.yml
├── .env.example
├── .gitignore
├── CHANGELOG.md
├── SECURITY.md
└── LICENSE
```

## Setup

1. Copy `.env.example` to `.env`.
2. Set `ALERT_WEBHOOK_URL` to a private endpoint that accepts the JSON alert payload.
3. Start n8n with `docker compose up -d` or use an existing n8n instance.
4. Import `workflows/api-monitoring.json`.
5. Create the Data Table described in `examples/data-table-schema.md`.
6. Select that table in `Store Check Result` because environment-specific table IDs are intentionally removed from the export.
7. Review the values in `Load Monitor Configuration`.
8. Execute `Start Manual Test` once, inspect the output, then activate the schedule.

## Default Configuration

| Field | Default |
| --- | --- |
| Service | `JSONPlaceholder API` |
| URL | `https://jsonplaceholder.typicode.com/posts/1` |
| Timeout | 5000 ms |
| Degraded threshold | 1000 ms |
| Expected field/value | `id` / `1` |
| Maximum attempts | 3 |
| Retry delay | 2000 ms |
| Schedule | Every 5 minutes |

## Example Result

```json
{
  "service_name": "JSONPlaceholder API",
  "status": "UP",
  "http_status": 200,
  "response_time_ms": 117,
  "attempt": 1,
  "error_type": null,
  "error_message": null,
  "previous_status": "DOWN",
  "status_changed": true,
  "alert_type": "RECOVERY",
  "alert_delivery_status": "SENT",
  "state_updated": true
}
```

The complete example is available in `examples/example-output.json`.

## Retry and Error Handling

Retries are allowed only while the result is `DOWN` or `UNKNOWN` and the current attempt is below `max_attempts`. With the default values, the workflow makes one initial request and at most two retries, waiting two seconds between attempts. There is no unlimited loop.

HTTP 4xx and 5xx responses are captured for classification. Timeout and network errors continue through the evaluation path rather than stopping the entire workflow.

## Alerting Logic

- `UP` to `DOWN`: incident alert.
- `UP` to `DEGRADED`: performance warning.
- `DOWN` or `DEGRADED` to `UP`: recovery alert.
- Unchanged state: no alert.

If alert delivery fails, the new state is not stored. This makes the next execution eligible to attempt the alert again.

## Security

The workflow reads `ALERT_WEBHOOK_URL` from an environment variable. The exported workflow contains no real credentials, webhook URLs, n8n instance identifiers or Data Table IDs. See `SECURITY.md` for operational recommendations.

## Tested Scenarios

The happy path, slow responses, invalid content, HTTP 404 and 500, timeout, DNS failure, bounded retries, duplicate-alert prevention, recovery and Data Table logging were tested. Details are in `docs/testing.md`.

## Limitations

- The MVP monitors one API.
- State uses n8n workflow static data and is intended for one active n8n instance.
- Publishing a new workflow version can affect static state.
- The included alert transport is a generic JSON webhook, not a provider-specific integration.
- Log retention and dashboards are not included.

## Future Improvements

- Store state in a shared database for queue-mode deployments.
- Load multiple services from a configuration table.
- Add authentication profiles without exposing credentials.
- Add alert-provider adapters and delivery retries.
- Add metrics dashboards and log-retention policies.

## License

MIT
