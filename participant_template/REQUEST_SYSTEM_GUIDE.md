# Request Inbox Integration Guide

This guide explains how your sub-portal receives access or collaboration requests.

## Required files

Your GitHub repository root must include:

```text
index.html
federation.json
theme.config.json
sonair_request_system.js
```

## Required HTML block

Keep this block in your `index.html`:

```html
<section class="card">
  <h2>Requests Inbox</h2>
  <p>Requests routed to this campus node will appear here.</p>
  <div id="requests-inbox"></div>
</section>

<script>
  window.SONAIR_NODE_ID  = 'your-node-id';
  window.SONAIR_API_BASE = 'http://EVENT_REQUEST_API:5055';
</script>
<script src="sonair_request_system.js"></script>
<script>
  SONAIR_REQUESTS.renderInbox('requests-inbox');
</script>
```

## The most important rule

Your routing ID must match exactly:

```text
window.SONAIR_NODE_ID = federation.json -> node.id
```

Example:

```json
"node": {
  "id": "ucl"
}
```

must match:

```js
window.SONAIR_NODE_ID = 'ucl';
```

## What the inbox does

The inbox will:

1. query the request API for requests addressed to your node ID;
2. show new pending requests;
3. let your team approve or reject requests;
4. update the request status in the shared event request system.

## Common signs of success

You should see:

```text
SONAIR server · http://...:5055
No requests yet.
```

or a list of pending requests.

## Common signs of a problem

If the inbox area is blank, `sonair_request_system.js` is probably missing or not loaded.

If it says `localStorage only`, the request API endpoint is missing or unreachable.

If requests are submitted but do not appear in your inbox, check that `SONAIR_NODE_ID` matches `federation.json -> node.id`.
