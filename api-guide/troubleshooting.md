# Troubleshooting Guide

This guide helps diagnose and resolve common issues when using the Scan API.

---

## Authentication Errors (401)

### Problem
Requests fail with a `401 Unauthorized` response.

### Possible Causes
- Invalid or expired API key
- Missing `Authorization` header
- Incorrect header format

### Solution
- Verify your API key is correct and active
- Ensure the header follows this format:

```
Authorization: Bearer YOUR_API_KEY
```

---

## Permission Errors (403)

### Problem
The API returns `403 Forbidden`.

### Possible Causes
- Insufficient permissions for the requested resource
- Account limitations or plan restrictions

### Solution
- Check your account permissions
- Confirm that your API key has access to the requested endpoint

---

## Scan Not Found (404)

### Problem
The API returns `404 Not Found` when checking scan status.

### Possible Causes
- Invalid or incorrect `scan_id`
- Scan has been deleted or expired

### Solution
- Verify the `scan_id` value
- Ensure the scan exists before querying its status

---

## Rate Limiting (429)

### Problem
Requests are rejected due to rate limits.

### Possible Causes
- Too many requests in a short time window

### Solution
- Implement retry logic with exponential backoff
- Reduce request frequency

---

## Scan Stuck in "queued" State

### Problem
A scan remains in `queued` status for an extended period.

### Possible Causes
- High system load
- Resource constraints on the target environment

### Solution
- Wait a few minutes and retry
- Check system status (if available)
- Contact support if the issue persists

---

## Invalid Request Payload (400)

### Problem
The API returns `400 Bad Request`.

### Possible Causes
- Missing required fields
- Invalid JSON format
- Unsupported scan profile

### Solution
- Validate request payload structure
- Ensure required fields like `target` are included
- Check for correct JSON formatting

---

## Debugging Tips

- Log all API responses for troubleshooting
- Validate requests using tools like Postman or curl
- Start with minimal payloads and increase complexity gradually
- Reproduce issues consistently before escalating
