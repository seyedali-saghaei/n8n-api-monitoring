# Data Table Schema

Create an n8n Data Table named `api_monitoring_logs` before activating the workflow.

| Column | Type |
| --- | --- |
| `service_name` | String |
| `url` | String |
| `checked_at` | Date |
| `logged_at` | Date |
| `status` | String |
| `http_status` | Number |
| `response_time_ms` | Number |
| `attempt` | Number |
| `error_type` | String |
| `error_message` | String |
| `previous_status` | String |
| `status_changed` | Boolean |
| `alert_type` | String |
| `alert_delivery_status` | String |
| `alert_delivery_error` | String |
| `state_updated` | Boolean |

After importing the workflow, open `Store Check Result` and select this table. The export intentionally contains no environment-specific Data Table ID.

