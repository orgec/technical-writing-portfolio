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
