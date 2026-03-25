# Getting Started with the Scan API

## Overview
The Scan API allows users to trigger automated scans against a target environment and retrieve scan status programmatically.

This guide explains how to authenticate, submit a scan request, and interpret the response.

## Authentication
Requests must include an API key in the `Authorization` header.

### Example
```bash
Authorization: Bearer YOUR_API_KEY
```

### Base URL
All endpoints are relative to the following base URL:
https://api.example-security-platform.com/v1


## Start a Scan

### Endpoint

POST /scans

### Example Request

curl -X POST https://api.example-security-platform.com/v1/scans \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "target": "https://example.com",
    "profile": "standard"
  }'

### Request Body
| Field   | Type   | Required | Description                     |
| ------- | ------ | -------- | ------------------------------- |
| target  | string | Yes      | Target URL or hostname to scan  |
| profile | string | No       | Scan profile (standard or full) |


## Example Response

{
  "scan_id": "scn_12345",
  "status": "queued",
  "created_at": "2026-03-25T10:15:00Z"
}

## Check Scan Status

### Endpoint

GET /scans/{scan_id}

### Example Request

curl https://api.example-security-platform.com/v1/scans/scn_12345 \
  -H "Authorization: Bearer YOUR_API_KEY"

### Example Response

{
  "scan_id": "scn_12345",
  "status": "running",
  "progress": 45
}

## Common Errors

| Status code | Meaning                  | Recommended action     |
| ----------- | ------------------------ | ---------------------- |
| 401         | Invalid API key          | Verify token and retry |
| 403         | Insufficient permissions | Check account access   |
| 404         | Scan not found           | Verify scan_id         |
| 429         | Rate limit exceeded      | Wait and retry         |


## Notes
Scan creation is asynchronous.
A queued status means the scan has been accepted but not started.
If failures persist, verify input format and rate limits.
