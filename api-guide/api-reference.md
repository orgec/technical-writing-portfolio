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
