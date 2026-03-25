# Getting Started with the Scan API

## Overview
The Scan API allows users to trigger automated scans against a target environment and retrieve scan status programmatically.

This guide explains how to authenticate, submit a scan request, and interpret the response.

## Authentication
Requests must include an API key in the `Authorization` header.

### Example
```bash
Authorization: Bearer YOUR_API_KEY

