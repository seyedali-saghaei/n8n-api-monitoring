# Architecture

## Processing Flow

1. `Start Manual Test` and `Schedule API Check` start the workflow.
2. `Load Monitor Configuration` provides one API configuration.
3. `Record Check Start` records the request start time.
4. `Check API Health` sends the HTTP request without stopping on non-2xx responses.
5. `Merge Check Context` combines the response with its configuration.
6. `Evaluate API Status` validates HTTP status, response content and response time.
7. `Should Retry Check` permits only bounded retries for `DOWN` and `UNKNOWN` results.
8. `Load Previous State` reads the last status from workflow static data.
9. `Detect Status Change` decides whether an alert is required.
10. The alert branch prepares and sends an incident, performance or recovery webhook.
11. `Store Current State` updates state only after a successful alert or when no alert is required.
12. `Prepare Check Log` and `Store Check Result` write a normalized result to the Data Table.

## Status Rules

| Condition | Status |
| --- | --- |
| 2xx, expected content and response time within threshold | `UP` |
| 2xx and valid content, but response time exceeds threshold | `DEGRADED` |
| Non-2xx, invalid content, timeout or network error | `DOWN` |
| Missing or unsupported response information | `UNKNOWN` |

## Alert Transitions

| Previous | Current | Alert |
| --- | --- | --- |
| `UP` | `DOWN` | Incident |
| `UP` | `DEGRADED` | Performance warning |
| `DOWN` | `UP` | Recovery |
| `DEGRADED` | `UP` | Recovery |
| Same status | Same status | None |

An initial `DOWN` or initial `DEGRADED` result also creates an alert. A failed alert delivery does not update state, allowing a later execution to try again.

## State Limitation

The MVP uses workflow static data for the previous state. This works for a single active n8n instance. Publishing a new workflow version can reset or change the available static state, and queue-mode deployments should use a shared external state store instead.

