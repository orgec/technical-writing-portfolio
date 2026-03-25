# Contributing to This Documentation

This document outlines the standards and workflow used to maintain this documentation set. It is intended as a reference for contributors and reviewers.

---

## Documentation Structure

```
technical-writing-portfolio/
├── api-guide/
│   ├── getting-started.md      # Quickstart for new developers
│   ├── api-reference.md        # Full endpoint reference
│   ├── integration-guide.md    # Workflow and automation patterns
│   └── troubleshooting.md      # Common errors and fixes
└── README.md
```

Each file serves a distinct purpose and a distinct audience. Before adding content, confirm it belongs in the intended file, this will avoid duplicating information across documents.

---

## Writing Standards

### Voice and tone
- Write in second person ("you", not "the user" or "one")
- Use active voice wherever possible
- Be direct — lead with what the reader needs to do, not with background context

### Formatting
- Use sentence case for headings (not Title Case)
- Use backticks for all inline code, parameter names, endpoint paths, and status codes: `scan_id`, `POST /scans`, `401`
- Use fenced code blocks with a language identifier: ` ```json `, ` ```python `, ` ```bash `
- Use tables for structured reference content (parameters, error codes, status values)

### Code examples
- All examples must be functional or clearly annotated as illustrative
- Use `YOUR_API_KEY` as the placeholder for authentication tokens
- Use `https://example.com` as the placeholder target URL
- Prefer `curl` for simple single-request examples; Python for multi-step workflows

---

## Contribution Workflow

1. **Branch** — create a branch from `main` using the format `docs/short-description`
2. **Write** — follow the writing standards above
3. **Review** — self-review against the checklist below before opening a PR
4. **PR** — open a pull request with a clear title and a one-line description of what changed and why

---

## Pre-PR Checklist

- [ ] Content is accurate and consistent with existing docs
- [ ] No information is duplicated across files
- [ ] All code examples are syntactically correct
- [ ] Tables are properly formatted and render correctly in GitHub preview
- [ ] Links point to the correct files or anchors
- [ ] No placeholder text left unresolved

---

## What Not to Include

- Marketing language or feature promotion
- Speculation about future functionality
- Content that duplicates what is already covered in another file — link to it instead
