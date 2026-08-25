# Security Policy

## Secrets

Do not place webhook URLs, API keys, tokens, passwords or customer data in the workflow export. Configure `ALERT_WEBHOOK_URL` through the runtime environment and keep the real `.env` file outside version control.

The example workflow does not contain credentials. The exported Data Table reference is intentionally empty and must be selected after import.

## Reporting a Vulnerability

Please report security issues privately to the repository owner. Do not include real secrets, production payloads or personal data in a public issue.

## Operational Recommendations

- Use HTTPS for external endpoints.
- Restrict access to the n8n editor.
- Back up the n8n volume and encryption key.
- Review alert payloads before monitoring services that return sensitive data.
- Pin the n8n image version for production deployments.

