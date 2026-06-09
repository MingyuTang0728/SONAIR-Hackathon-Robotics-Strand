# Submission Checklist

Before submitting, check the following:

## Website

- [ ] My sub-portal loads at a stable GitHub Pages URL.
- [ ] The page does not require login.
- [ ] The page is suitable for iframe embedding.
- [ ] The page clearly states my institution name and city.
- [ ] The page explains my lab identity or robotics capability.
- [ ] The page includes at least one equipment item or dataset.
- [ ] The page includes a collaboration contact.

## Required files

- [ ] `index.html` exists in the repository root.
- [ ] `federation.json` exists in the repository root.
- [ ] `theme.config.json` exists in the repository root.
- [ ] `sonair_request_system.js` exists in the repository root.

## Manifest

- [ ] `federation.json -> node.id` is unique.
- [ ] `federation.json -> node.id` exactly matches `window.SONAIR_NODE_ID`.
- [ ] `node.url` is my real GitHub Pages URL.
- [ ] `co_creation_card.visitor_url` is my real GitHub Pages URL.
- [ ] `node.lat` and `node.lon` are real UK coordinates.

## Dashboard

- [ ] RTT time-series plot included.
- [ ] Command latency histogram included.
- [ ] Plain-language interpretation included.
- [ ] Optional: GRANT / RELEASE / ESTOP annotations included.

## Request inbox

- [ ] The request inbox is visible on the page.
- [ ] `sonair_request_system.js` loads successfully.
- [ ] `SONAIR_API_BASE` is set to the event request API endpoint.
- [ ] Requests can be approved or rejected from the sub-portal.

## Final link to submit

Submit this URL:

```text
https://YOUR_GITHUB_USERNAME.github.io/YOUR_REPOSITORY_NAME/federation.json
```
