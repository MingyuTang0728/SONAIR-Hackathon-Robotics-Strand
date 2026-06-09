# Troubleshooting Guide

This file summarises common issues and fixes when building your SONAIR Campus Sub-Portal.

## 1. The request inbox is blank

Likely cause: `sonair_request_system.js` is missing or not loaded.

Fix:

1. Make sure `sonair_request_system.js` is uploaded to the same repository root as `index.html`.
2. Open this URL directly:

```text
https://YOUR_GITHUB_USERNAME.github.io/YOUR_REPOSITORY_NAME/sonair_request_system.js
```

3. If it returns 404, upload the file again.
4. Open browser DevTools Console and check for `404 sonair_request_system.js`.

## 2. The inbox shows `No requests yet`, but a request was submitted

Likely cause: ID mismatch.

Fix:

Check that these two values match exactly:

```text
federation.json -> node.id
index.html -> window.SONAIR_NODE_ID
```

For example, do not mix these:

```text
ucl
UCL
ucl-node
university-college-london
```

Choose one ID and use it everywhere.

## 3. The submitted application ID starts with `local-`

This means the request was saved locally in the browser instead of the shared request API.

Fix:

1. Check that `window.SONAIR_API_BASE` is correct.
2. Check that the request API endpoint is reachable from your browser.
3. Reload the page and submit again.

A successful shared request usually uses a UUID-like ID, not `local-...`.

## 4. GitHub Pages returns 404 after approval

Likely cause: the URL in `federation.json` is wrong.

Fix:

Update both fields:

```json
"url": "https://YOUR_GITHUB_USERNAME.github.io/YOUR_REPOSITORY_NAME/"
"visitor_url": "https://YOUR_GITHUB_USERNAME.github.io/YOUR_REPOSITORY_NAME/"
```

Then open this URL directly:

```text
https://YOUR_GITHUB_USERNAME.github.io/YOUR_REPOSITORY_NAME/federation.json
```

Make sure it shows the updated URL.

## 5. GitHub Pages site cannot reach an HTTP request API

If your sub-portal is served from `https://...github.io` but the request API is `http://...`, the browser may block the request as mixed content or private-network access.

Fix options:

1. Use the event-provided API endpoint exactly as instructed.
2. Test locally over HTTP during development.
3. Check browser Console for Mixed Content or Failed to fetch errors.

## 6. `theme.config.json` is not applied

Likely cause: the file is missing, invalid JSON, or in the wrong folder.

Fix:

1. Keep `theme.config.json` in the same root folder as `index.html`.
2. Open it directly in your browser.
3. Validate that it is valid JSON.

## 7. Manifest not accepted

Check that your `federation.json` contains these required fields:

```text
node.id
node.name
node.city
node.lat
node.lon
node.color
node.url
node.contact_email
co_creation_card.title
co_creation_card.description
```

Also check that latitude and longitude are real UK coordinates.

## 8. Multiple teams use the same node ID

Each team must use a unique node ID.

Good examples:

```text
ucl
manchester
sheffield
birmingham
```

Bad example:

```text
your-node-id
```

Do not leave placeholder values unchanged.
