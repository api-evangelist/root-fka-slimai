---
name: Monitor security findings and subscribe to remediated images
description: Track CVE security findings across subscribed images and libraries, subscribe to Root-remediated artifacts, and register a webhook to be notified when a new remediated tag is created.
api: openapi/root-fka-slimai-openapi-original.json
operations:
  - "GET /v3/security_findings"
  - "GET /v3/security_findings/summary"
  - "POST /v3/subscriptions"
  - "GET /v3/subscriptions"
  - "POST /v3/settings/webhooks"
  - "GET /v3/avrs/{avr_id}/artifacts/sbom"
generated: '2026-07-21'
method: generated
---

# Monitor security findings and subscribe to remediated images

Continuously track CVE exposure and wire up notifications for new Root Remediated Tags (RRTs).

## Auth
- Root API key via HTTP Basic (username, empty password) or Bearer token. See `authentication/root-fka-slimai-authentication.yml`.
- Base host: `https://api.root.io`.

## Steps
1. List current findings: `GET /v3/security_findings` (paginate with `after` + `limit`; filter by severity/status).
2. Get a rollup: `GET /v3/security_findings/summary` (counts by status and severity) or `/summary/daily` for a time series.
3. Subscribe to images/libraries you want continuously remediated: `POST /v3/subscriptions` (bulk); confirm with `GET /v3/subscriptions`.
4. Register a webhook so you are notified when a new remediated tag lands: `POST /v3/settings/webhooks` with your HTTPS URL and the `io.root.cr.image.created.v1` event type. Store the returned signing secret.
5. Verify deliveries against the `webhook-signature` header (Standard Webhooks, HMAC-SHA256); reject timestamps older than 5 minutes. See `asyncapi/root-fka-slimai-webhooks.yml`.
6. Pull compliance artifacts per AVR as needed: SBOM `GET /v3/avrs/{avr_id}/artifacts/sbom`, VEX `.../vex`, provenance `.../provenance`.

## Conventions
- Cursor pagination (`after`/`limit`), `429` rate limiting with `Retry-After`, JSON error bodies. See `conventions/root-fka-slimai-conventions.yml`.

## Notes
- A reference webhook consumer (AWS Lambda that mirrors remediated images to ECR with signature verification) is published at `github.com/rootio-avr/ecr-mirror-lambda`.
