# API Reference

This document provides a structured reference for the Scan API endpoints, including request formats, parameters, and response structures. It is intended for developers who need precise technical details.

---

## Base URL

```
https://api.example-security-platform.com/v1
```

---

## Authentication

All requests must include an API key in the `Authorization` header:

```
Authorization: Bearer YOUR_API_KEY
```

---

# Endpoints

---

## Create Scan

Trigger a new scan against a target.

### Request

```
POST /scans
```

### Headers

| Header        | Required | Description              |
|--------------|----------|--------------------------|
| Authorization| Yes      | Bearer API key           |
| Content-Type | Yes      | application/json         |

### Request Body

| Field   | Type   | Required | Description                     |
|--------|--------|----------|---------------------------------|
| target | string | Yes      | Target URL or hostname to scan  |
| profile| string | No       | Scan profile (standard or full) |

### Example Request

```
curl -X POST https://api.example-security-platform.com/v1/scans \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "target": "https://example.com",
    "profile": "standard"
  }'
```

### Response

```
{
  "scan_id": "scn_12345",
  "status": "queued",
  "created_at": "2026-03-25T10:15:00Z"
}
```

---

## Get Scan Status

Retrieve the status of a scan.

### Request

```
GET /scans/{scan_id}
```

### Path Parameters

| Parameter | Type   | Required | Description        |
|----------|--------|----------|--------------------|
| scan_id  | string | Yes      | Unique scan ID     |

### Example Request

```
curl https://api.example-security-platform.com/v1/scans/scn_12345 \
  -H "Authorization: Bearer YOUR_API_KEY"
```

### Response

```
{
  "scan_id": "scn_12345",
  "status": "running",
  "progress": 45
}
```

---
## Get Scan Findings

Retrieve detailed vulnerability findings for a completed scan.

### Request

```
GET /scans/{scan_id}/findings
```


### Path Parameters

| Parameter | Type   | Required | Description    |
|-----------|--------|----------|----------------|
| scan_id   | string | Yes      | Unique scan ID |

### Query Parameters

| Parameter | Type    | Required | Default  | Description                                              |
|-----------|---------|----------|----------|----------------------------------------------------------|
| severity  | string  | No       | all      | Filter by severity: `critical`, `high`, `medium`, `low` |
| limit     | integer | No       | 20       | Number of results per page (max: 100)                   |
| offset    | integer | No       | 0        | Pagination offset                                        |

### Example Request

```
curl https://api.example-security-platform.com/v1/scans/scn_12345/findings \
  -H "Authorization: Bearer YOUR_API_KEY"
```

### Example Response

```json
{
  "scan_id": "scn_12345",
  "status": "completed",
  "completed_at": "2026-03-25T10:42:00Z",
  "total_findings": 3,
  "findings": [
    {
      "finding_id": "fnd_001",
      "severity": "critical",
      "title": "SQL Injection in Login Endpoint",
      "description": "User-supplied input in the `username` parameter is not sanitised before being passed to a database query. An attacker can manipulate the query to bypass authentication or extract data.",
      "affected_url": "https://example.com/login",
      "parameter": "username",
      "recommendation": "Use parameterised queries or prepared statements. Never concatenate user input directly into SQL queries.",
      "references": [
        "https://owasp.org/www-community/attacks/SQL_Injection"
      ]
    },
    {
      "finding_id": "fnd_002",
      "severity": "high",
      "title": "Missing HTTP Strict Transport Security (HSTS) Header",
      "description": "The server does not return an HSTS header, leaving connections vulnerable to protocol downgrade attacks.",
      "affected_url": "https://example.com",
      "parameter": null,
      "recommendation": "Add `Strict-Transport-Security: max-age=31536000; includeSubDomains` to all HTTPS responses.",
      "references": [
        "https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Strict-Transport-Security"
      ]
    },
    {
      "finding_id": "fnd_003",
      "severity": "medium",
      "title": "Server Version Disclosure",
      "description": "The `Server` response header exposes the web server version, providing attackers with information useful for targeting known vulnerabilities.",
      "affected_url": "https://example.com",
      "parameter": null,
      "recommendation": "Configure the server to suppress or genericise the `Server` header.",
      "references": []
    }
  ],
  "pagination": {
    "total": 3,
    "limit": 20,
    "offset": 0
  }
}
```

### Findings Object

| Field        | Type   | Description                                              |
|--------------|--------|----------------------------------------------------------|
| finding_id   | string | Unique identifier for the finding                        |
| severity     | string | Severity level: `critical`, `high`, `medium`, or `low`  |
| title        | string | Short description of the vulnerability                   |
| description  | string | Full explanation of the issue and its potential impact   |
| affected_url | string | URL where the vulnerability was detected                 |
| parameter    | string | Affected request parameter, if applicable                |
| recommendation | string | Suggested remediation steps                            |
| references   | array  | Links to relevant standards or documentation             |

### Notes

- This endpoint is only available for scans with `status: completed`.
- Querying findings for a scan that is still `running` or `queued` returns a `409 Conflict` error.
- Results are sorted by severity (critical first) by default.
---

## Scan Status Values

| Status   | Description                          |
|----------|--------------------------------------|
| queued   | Scan accepted but not started        |
| running  | Scan in progress                     |
| completed| Scan finished successfully           |
| failed   | Scan failed due to an error          |

---

## Error Responses

| Status Code | Meaning                  | Description                     |
|------------|--------------------------|---------------------------------|
| 400        | Bad Request              | Invalid request payload         |
| 401        | Unauthorized             | Invalid or missing API key      |
| 403        | Forbidden                | Insufficient permissions        |
| 404        | Not Found                | Resource does not exist         |
| 429        | Too Many Requests        | Rate limit exceeded             |
| 500        | Internal Server Error    | Unexpected server error         |

---

## Rate Limiting

The API enforces rate limits to ensure stability.

- Requests exceeding limits will return `429`
- Implement retry logic with backoff strategies

---

## Versioning

The API version is included in the URL:

```
/v1/
```

Future versions may introduce breaking changes.

---
This document provides a structured reference for all Scan API endpoints, including request formats, parameters, and response structures. It is intended for developers who need precise and complete technical details.

## Notes

- All responses are in JSON format
- All timestamps are in ISO 8601 format
- Scans are processed asynchronously
