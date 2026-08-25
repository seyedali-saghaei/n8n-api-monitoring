# Test Results

Tests were executed on 25 August 2026 against public test endpoints.

| Scenario | Expected result | Result |
| --- | --- | --- |
| Healthy API | `UP`, HTTP 200 | Passed |
| Slow threshold | `DEGRADED`, `SLOW_RESPONSE` | Passed |
| HTTP 404 | `DOWN`, three attempts | Passed |
| HTTP 500 | `DOWN`, three attempts | Passed |
| Invalid expected content | `DOWN`, `INVALID_RESPONSE` | Passed |
| Request timeout | `DOWN`, `TIMEOUT`, three attempts | Passed |
| DNS failure | `DOWN`, `NETWORK_ERROR`, three attempts | Passed |
| Persistent failure | No duplicate alert | Passed |
| Recovery | `DOWN` to `UP`, recovery alert | Passed |
| Structured Data Table log | One normalized row per final result | Passed |

The alert transport was tested with a public HTTP endpoint returning a successful 2xx response. Integration with a specific production alert provider remains deployment-specific.

