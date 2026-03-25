# Integration Guide

This guide explains how to integrate the Scan API into real-world workflows, including backend services and automation pipelines. It focuses on practical usage patterns and system-level considerations.

---

## Typical Workflow

1. Authenticate using your API key
2. Trigger a scan using the `/scans` endpoint
3. Store the returned `scan_id`
4. Poll the scan status using `/scans/{scan_id}`
5. Process results once the scan is complete

---

## Example Integration Flow

```
1. User submits target URL
2. Backend sends POST /scans request
3. API returns scan_id
4. Backend periodically checks scan status
5. Results are displayed to the user
```

---

## Automation Example

## Python Integration Example

The following example shows how to trigger a scan, poll for completion, and retrieve findings using Python.

```python
import time 
import requests

API_KEY = "YOUR_API_KEY"
BASE_URL = "https://api.example-security-platform.com/v1"

headers = {
    "Authorization": f"Bearer {API_KEY}",
    "Content-Type": "application/json"
}

def start_scan(target: str, profile: str = "standard") -> str:
    """Trigger a new scan and return the scan_id."""
    response = requests.post(
        f"{BASE_URL}/scans",
        headers=headers,
        json={"target": target, "profile": profile}
    )
    response.raise_for_status()
    return response.json()["scan_id"]

def wait_for_completion(scan_id: str, interval: int = 10, timeout: int = 300) -> dict:
    """Poll scan status until completed or timeout is reached."""
    elapsed = 0
    while elapsed < timeout:
        response = requests.get(f"{BASE_URL}/scans/{scan_id}", headers=headers)
        response.raise_for_status()
        data = response.json()

        status = data["status"]
        print(f"Status: {status} (elapsed: {elapsed}s)")

        if status == "completed":
            return data
        if status == "failed":
            raise RuntimeError(f"Scan {scan_id} failed.")

        time.sleep(interval)
        elapsed += interval

    raise TimeoutError(f"Scan {scan_id} did not complete within {timeout} seconds.")

def get_findings(scan_id: str, severity: str = None) -> list:
    """Retrieve findings for a completed scan, optionally filtered by severity."""
    params = {}
    if severity:
        params["severity"] = severity

    response = requests.get(
        f"{BASE_URL}/scans/{scan_id}/findings",
        headers=headers,
        params=params
    )
    response.raise_for_status()
    return response.json()["findings"]

# --- Main workflow ---

scan_id = start_scan("https://example.com")
print(f"Scan started: {scan_id}")

wait_for_completion(scan_id)

findings = get_findings(scan_id, severity="critical")
print(f"\nCritical findings: {len(findings)}")
for f in findings:
    print(f"  [{f['severity'].upper()}] {f['title']}")
    print(f"  → {f['recommendation']}\n")
```

### Notes

- Store `API_KEY` in an environment variable rather than hardcoding it:
  ```python
  import os
  API_KEY = os.environ.get("SCAN_API_KEY")
  ```
- The `wait_for_completion` function uses simple polling. For production use, consider a webhook-based approach to avoid unnecessary requests (see Webhook-Based Workflow above).
- `raise_for_status()` will raise an `HTTPError` for 4xx and 5xx responses. Wrap calls in `try/except` blocks for production error handling.

### Use Case
Run a security scan on every deployment.

### Steps
- Trigger scan after deployment
- Monitor scan status asynchronously
- Block release if critical vulnerabilities are detected

---

## Best Practices

- Store API keys securely (never expose in frontend code)
- Use environment variables for authentication
- Implement retry logic for failed requests
- Handle asynchronous workflows properly
- Log scan results for auditing and debugging

---

## Error Handling Strategy

- Handle expected errors (401, 403, 404, 429)
- Implement retries for transient failures
- Provide meaningful error messages to users

---

## Scaling Considerations

- Avoid excessive polling (use backoff strategies)
- Batch scan requests where possible
- Monitor API usage and rate limits

---

## Webhook-Based Workflow (Advanced)

Instead of polling for scan status, you can configure a webhook to receive updates when scan status changes.

### Benefits
- Reduces unnecessary API calls
- Improves system efficiency
- Enables real-time processing of results

## Security Considerations

- Never expose sensitive endpoints publicly
- Validate all user inputs before sending requests
- Ensure secure storage of scan results
