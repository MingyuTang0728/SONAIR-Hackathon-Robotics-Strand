# SONAIR Request System — Integration Guide
## sonair_request_system.js · v3

Add this file to your sub-portal to receive and manage visit/collaboration
requests submitted from the main SONAIR portal.

---

## Correct request flow

```
Visitor opens main portal → Co-Creation Space
→ finds your card → clicks Apply Visit / Apply Collaborate
→ main portal POSTs to Python server  (subportal_id = your node.id)
→ Python server stores the record
→ your sub-portal Requests Inbox polls and displays it
→ your team clicks Approve / Reject
```

**Your sub-portal needs only the Inbox — not the Apply form.**
The Apply buttons are in the main portal Co-Creation Space, not in your sub-portal.

---

## Step 1 — Configure the backend

Ask the organiser for the server IP at the start of the day.

```html
<!-- Both lines required — place BEFORE sonair_request_system.js -->
<script>
  window.SONAIR_NODE_ID  = 'your-institution-id'; // must match node.id in federation.json
  window.SONAIR_API_BASE = 'http://ORGANISER_IP:5055'; // organiser provides on the day
</script>
<script src="sonair_request_system.js"></script>
```

> ⚠️ Without `SONAIR_NODE_ID` the inbox will always be empty (`fetch('federation.json')` fails under `file://` protocol).

---

## Step 2 — Add an inbox container

```html
<div id="my-inbox"></div>
<script>SONAIR_REQUESTS.renderInbox('my-inbox');</script>
```

That's it. The inbox will:
- Automatically fetch requests tagged with your `node.id` from the server
- Auto-refresh every 5 seconds
- Show Approve / Reject buttons — clicking them PATCHes status on the server

---

## Full example page

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="utf-8"/>
  <title>Our Lab — SONAIR Sub-Portal</title>
</head>
<body>
  <h1>Our Robotics Lab</h1>
  <p>We work on sim2real transfer for manipulator arms.</p>

  <h2>Incoming Requests</h2>
  <p>Requests submitted from the main SONAIR portal appear below.</p>

  <div id="requests-inbox"></div>

  <script>window.SONAIR_API_BASE = 'http://ORGANISER_IP:5055';</script>
  <script src="sonair_request_system.js"></script>
  <script>SONAIR_REQUESTS.renderInbox('requests-inbox');</script>
</body>
</html>
```

---

## Why the inbox receives the right requests

When a visitor submits via the main portal, it sets `subportal_id` to your
card's `node.id` (from your federation.json). The Python server stores it by
that ID. Your inbox queries `GET /api/applications?subportal_id=YOUR_NODE_ID`
and receives only requests addressed to your portal.

---

## JavaScript API (inbox side only)

| Function | Description |
|----------|-------------|
| `SONAIR_REQUESTS.renderInbox('element-id')` | Render the inbox — call this once |
| `SONAIR_REQUESTS.updateRequest(...)` | Auto-injected into Approve/Reject buttons — no manual call needed |
| `SONAIR_REQUESTS.deleteRequest(...)` | Auto-injected into Delete buttons — no manual call needed |
| `SONAIR_REQUESTS.exportCSV()` | Returns all requests as CSV string — optional |
| `SONAIR_REQUESTS.getAll()` | Returns all requests as array (Promise) — optional |

---

## Fallback if server is unreachable

The inbox shows an orange warning and falls back to localStorage (same device
only). This means real requests from the main portal will NOT appear.
Fix: confirm you're on the same WiFi as the organiser's machine and that
`SONAIR_API_BASE` has the correct IP.
