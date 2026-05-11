# api-support-toolkit
# API Support Toolkit

Reusable Postman tooling for diagnosing API issues quickly during customer support work.

## What's in here

### Diagnostic Logger (`scripts/diagnostic-logger.js`)

A Postman post-response script that automatically generates a structured diagnostic report for every HTTP request. It logs four layers of information that matter for troubleshooting:

1. **Status code** — classified as success, client error, or server error
2. **Headers** — Content-Type, request ID, Retry-After, WWW-Authenticate, rate limit info
3. **Body** — parsed structure if JSON, snippet otherwise
4. **Timing & size** — useful for distinguishing fast code errors from slow timeouts

### Diagnostic Collection (`collections/day6-diagnostic-collection.json`)

A Postman collection with the diagnostic logger applied at the collection level, so every request inside automatically produces a diagnostic report in the Postman console.

## Why I built this

Most "the API is broken" tickets are actually input-triggered, auth-related, or environment-specific — but you can't tell which without structured observation. This tool forces a diagnostic-first habit: see the full picture of a response before forming a hypothesis or replying to the customer.

## How to use

1. Import the collection: in Postman, click **Import** → drag in `day6-diagnostic-collection.json`
2. Send any request inside the collection
3. Open the Postman Console (bottom-left `>_` icon) to see the diagnostic report

To apply the script to your own collection, copy `scripts/diagnostic-logger.js` into your collection's **Scripts → Post-response** tab.

## Example output

When a request to `POST /booking` is sent with an incomplete body, the console produces:

```
========== DIAGNOSTIC REPORT ==========

[1] STATUS: 500 Internal Server Error — 🔥 SERVER ERROR

[2] HEADERS:
    Content-Type:   text/plain; charset=utf-8
    Request-ID:     none
    Retry-After:    none
    WWW-Auth:       none

[3] BODY:
    Format:         text/plain
    Snippet:        Internal Server Error

[4] TIMING: 1030ms (slow)
    Size:    789 bytes
```

This tells me at a glance: the 500 was input-triggered (no request ID, plain-text body, slow response indicates the server crashed mid-processing rather than rejecting outright). The next step would be to retry with a complete body to confirm — which in this case returns 200.

## Built during

Self-directed API support engineering training, May 2026.
