---
name: Remediate application dependencies with Root
description: Analyze a package manifest against Root's vulnerability database and retrieve patched, drop-in remediation recommendations for an ecosystem (pypi, npm, maven, golang, nuget, composer, apt/apk/yum).
api: openapi/root-fka-slimai-openapi-original.json
operations:
  - "POST /v3/analyze/pypi"
  - "POST /v3/analyze/npm"
  - "POST /v3/analyze/maven"
  - "POST /v3/analyze/golang"
  - "GET /v3/patches"
  - "GET /v3/packages"
generated: '2026-07-21'
method: generated
---

# Remediate application dependencies with Root

Use Root's REST API to find Root-patched replacements for vulnerable dependencies without changing package versions where possible.

## Auth
- Send your Root API key as an HTTP Basic username (empty password) OR as a Bearer token in the `Authorization` header. See `authentication/root-fka-slimai-authentication.yml`.
- Base host: `https://api.root.io`.

## Steps
1. Collect the dependency list you want to check for one ecosystem.
2. Call the matching analyze endpoint with the package list in the body:
   - Python: `POST /v3/analyze/pypi`
   - JavaScript: `POST /v3/analyze/npm`
   - Java: `POST /v3/analyze/maven`
   - Go: `POST /v3/analyze/golang`
   - (also `/v3/analyze/nuget`, `/v3/analyze/composer`, `/v3/analyze/apt`, `/v3/analyze/apk`, `/v3/analyze/yum`)
3. Read the remediation recommendations (patched versions / skipped packages) from the response.
4. To inspect the underlying CVE tickets and patches, call `GET /v3/patches` filtered by `cve_id`, `ecosystem`, `severities`, or `patch_status`.
5. To browse Root-secured packages directly, call `GET /v3/packages` (paginate with `after` + `limit`).

## Conventions
- Cursor pagination: `limit` (default 100, max 1000) and `after`. See `conventions/root-fka-slimai-conventions.yml`.
- Request bodies over 32MB return `413`. Rate limiting returns `429` with `X-RateLimit-*` and `Retry-After`.
- Error semantics: `errors/root-fka-slimai-problem-types.yml` (JSON message bodies, not RFC 9457).

## Notes
- For CI, the first-party `rootio_patcher` CLI wraps these flows (`rootio_patcher pip|npm|maven remediate`). See `cli/root-fka-slimai-cli.yml`.
